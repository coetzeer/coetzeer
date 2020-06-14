---
layout: post
title: "InfluxDB via RabbitMQ"
date: 2020-06-06
description: 'Gather metrics through Telegraph and RabbitMQ'
img: influxrmq.jpg 
tags: [rmq,influxdb,metrics] # add tag
toc: true
---

For a while I've been using InfluxDB as a Time Series Database (TSB) to collect metrics from various sources around my home (mainly 2 Freenas servers using Graphite, and Raspberry Pie's using CollectD). Influxdb is incredibly easy to use. Being a Go binary, it's very portable across platforms, and also easy to run. I once spent an evening trying to setup a Graphite server in a FreeBSD jail and eventually just gave up. InfluxDB however, just works.

It has another interesting set of features in that it impersonate most of the existing metrics collecting servers. With very little effort it can impersonate a Graphite server, a collectD server receiving the native collectD metrics as published by 'network' plugin, and a host of other options that I haven't personally used but are equally as easy to set up. So for ages I've been running an InfluxDB set up that looks a bit like this: 


{% include mermaid.html content="
graph LR
    A[Freenas] -- publishing graphite --> D
    B(Raspberry Pi) -- publishing collectd --> D
    C[Telegraph] -- publishing native Influx format --> D
    D[(InfluxDB)] --  native influx queries --> E((Grafana));
" %}

The config file looked a bit like this:

```
[http]
  enabled = true
  bind-address = ":8086"
  auth-enabled = false

[[graphite]]
  enabled = true
  database = "graphite"
  retention-policy = ""
  bind-address = ":2003"
  protocol = "tcp"
  consistency-level = "one"
  templates = [
     "*.app env.service.resource.measurement",
     "servers.* .host.resource.measurement*",
   ]

[[collectd]]
  enabled = true
  bind-address = ":25826"
  database = "collectd"
  retention-policy = ""
  security-level = "none"
  batch-size = 5000
  batch-pending = 100
  read-buffer = 0
  typesdb = "/usr/local/share/collectd"

```

{% include important.html 
    content="Using a collectd format requires that you install a package that gives you a collectd types.db file. This is usually achieved by installing the collectd base package on your platform. " %}

## Why move?

This setup worked well, until I had the recent hardware failure that took out my Freenas box. Since then I've been a bit more aware of the workloads that are happening on that machine. It used to live on the bottom rack of my 12u Startech rack, out of site and largely out of mind. However, now it lives on top of the rack, and I hear every time there is a significant IO load making the hard drives work. 

With a little bit of digging around [^1] I found that there were 2 main culprits of constant, low grade IO overhead: influxDB and consul. Given that I am pushing quite a few metrics through to this server, it made sense that there would be at least some, constant IO workload. 

So I resolved to move InfluxDB off my precious FreeNAS server, and away from the hard drives that host my precious data. Completely by coincidence, right before my FreeNAS hardware failure, I had purchased a machine that I had intended to use for backups. Having done a bit of rethinking about hardware failures and how backups should work, I decided that the new machine [Thraskii](/docs/gear.html#thraskii) would be better used as lab: somewhere to test out new ideas, where the cost if it dying is not too high, and where I don't mind giving it a good kicking every now and then. So, Thraskii is the new home of the 'Metrics stack'.

Another goal I wanted to accomplish was to find a reasonable way to replicate metrics out to another instance. The commercial version of InfluxDB contains support for HA, multiple instances, replication etc, but I don't want a full enterprise stack, and of course I don't really want to pay. I could just do regular zfs snapshots, that is clean and pretty fool proof option, but has 2 distinct downsides - the lowest granularity of the backup is the highest frequency of zfs snapshots I can get FreeNAS to orchestrate, and a snapshot mid transaction is likely to lave me with a database in an inconsistent state. The latter is not a deal breaker - influxDB seems to support a write ahead log (WAL), so with some fiddling I could probably recover the data. The former is a bigger problem, mainly because I've gotten myself into a pickle with millions of unmanaged snapshots before. So the importance of managing those very frequent snapshots becomes high. Neither of these problems are deal breakers, but ideally if I could run 2, up to date copies of the same DB, I'd be happier.

## The Telegraf swiss army knife

I decided to add a layer or 2 of indirection. After reading a bit about Telegraf (the Influx native metrics retriever/swiss army knive), I could see that it was straight forward enough to set up a Telegraf instance as a proxy to forward metrics streams to an InfluxDB instance. What's more, it can use RabbitMQ as transport for sending the metrics to the InfluxDB server using another Telegraf instance as a consumer. This was a compelling way to achieve a level of HA, as it's trivial to setup 2 or more queues inside RabbitMQ that get messages from the same Exchange i.e. it's setup as a topic that queues can subscribe to. 

```
 [[outputs.amqp]]
   brokers = ["amqp://localhost:5672/influxdb"]
   exchange = "influx"
   exchange_type = "direct"
   username = "influx"
   password = "influx"
   routing_key = "telegraf"
```

To test the Telegraf instance, I added the RabbitMQ input plugin that gathers metrics off the web based admin console of rabbitMQ. This felt like a good way of killing 2 birds with one stone: I could test the new transport mechanism without having to worry about whether the collectd or grapite facade's were working and I could get some insight into how much work the rabbitMQ instance is doing.

```
 [[inputs.rabbitmq]]
    url = "http://localhost:15672"
   username = "monitoring"
   password = "monitoring"
```

Another useful debugging tool is the file output. This emits to a file the metrics that would be emitted into InfluxDB or in this case RabbitMQ. It's worth remembering to switch this off once you've got everything up and running as it does increase the io load of the process. 

```
# Send telegraf metrics to file(s)
[[outputs.file]]
    files = ["/var/log/telegraf/telegraf.out"]
    rotation_interval = "1d"
    rotation_max_size = "100MB"
    rotation_max_archives = 2
    data_format = "influx"

```



<div class="notebox">
     <b>AMQP/RabbitMQ config in telegraf</b>
     
     <p>
     It's worth noting that there are 3 different amqp/rabbitmq related plugins that you can use in Telegraf config
 
     <ul>
     <li><b>outputs.amqp</b> - writes a metrics stream to an AMQP exhange</li>
     <li><b>inputs.amqp_consumber</b> - reads metrics off an AMQP queue</li>
     <li><b>inputs.rabbitmq</b> - gathers runtime metrics from a rabbitmq installation via the web based admin plugin</li>
     </ul>
     
     </p>
</div>

## Keeping up the facade

With even more digging I could see that the clever people from Influx have decoupled support for handling inputs for different storage backends into 'how' the data is received and the exact format in which it is received. This [page](https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_INPUT.md) on their github shows the different formats that they support: collectd, csv, Graphite, nagios to name but a few. The example on that page shows an 'exec' plugin that is expecting data in a 'json' format. What I wanted to do was to open two different TCP connections, one that received data in a collectd format, the other to receive it in a Graphite format, thereby preserving the current behaviour of my InfluxDB instance. This was fairly straight forward to do:

```
# Native Influx HTTP write listener
[[inputs.influxdb_listener]]
   service_address = ":8086"

# Collectd
[[inputs.socket_listener]]
  service_address = "udp://0.0.0.0:25826"
  data_format = "collectd"
  collectd_typesdb = ["/usr/local/share/collectd/types.db"]
    [inputs.socket_listener.tags]
      source = "collectd"

# Graphite
[[inputs.socket_listener]]
  service_address = "tcp://0.0.0.0:2003"
  data_format = "graphite"
  separator = "."
  templates = [
     "servers.* .host.resource.measurement*",
      "*.app env.service.resource.measurement"
    ]
    [inputs.socket_listener.tags]
      source = "graphite"
```


So now, the flow of how metrics are collected looks more like this:


{% include mermaid.html content="
graph TB

    A[Freenas] -- graphite --> D
    B(Raspberry Pi) -- collectd --> D
    C[Telegraph] -- native --> D

    subgraph Telegraf receiver jail
    D[Telegraph proxy] --> F
    end
           
    subgraph InfluxDB consumer jail
        F[/Rabbit MQ/] --> G

    G[Telegraph consumer] --> E[(InfluxDB)]
    end;

" %}


## Ready for the tinfoil hat

I used 2 separate jails, with single instances of everything. However you could split this out to be much more granular, giving you the option of doubling up certain components for HA purposes. For example if you wanted to achieve redundancy at every layer, you could have 2 of everything:

{% include mermaid.html content="
graph TB

    A[Freenas] -- graphite --> D
    B(Raspberry Pi) -- collectd --> D
    C[Telegraph] -- native --> D
    D --> E
    D[HA Proxy] --> F
    
    subgraph aa1 [Metrics Proxy Cluster]
    E[Telegraph proxy]
    F[Telegraph proxy]
    end 
 
    subgraph aa2 [Rabbit MQ cluster]
    E --> G[/Rabbit MQ/]
    F --> I[/Rabbit MQ/]
    end 
    
    subgraph aa3 [Metrics Consumer]
    J[Telegraf consumer] --> K[(InfluxDB)]
    I --> J
    end
    
    subgraph aa4 [Metrics Consumer]
    L[Telegraf consumer] --> M[(InfluxDB)]
    G --> L
    end;
" %}


## Wiring it all up - the proxy + producer

### Step 1 - create a jail

I created a jail called InfluxProxy01. I also created a CNAME to this jail in my pi hole, so that I could swap out my old InfluxDB instance with this proxy once it was all ready. This layer of indirection gives the opportunity to add in load balancer at a later stage too.

```
iocage create -n influx01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.3-RELEASE boot=on
```

### Step 2 - install Telegraf and rabbitMQ

```
pkg update
pkg install rabbitmq telegraf collectd
sysrc rabbitmq_enable=YES
service rabbitmq start
sysrc telegraf_enable=YES
```

### Step 3 - configure RabbitMQ

Some intial setup: 
- enable the web admin console
- create an influx vhost
- create an admin user, runtime user, and a monitoring user.

```
rabbitmq_management
rabbitmq-plugins enable rabbitmq_management
service rabbitmq start

rabbitmqctl add_vhost influx
rabbitmqctl add_user raymondcoetzee foobarbaz --vhost influx
rabbitmqctl set_user_tags raymondcoetzee administrator
rabbitmqctl set_permissions --vhost influx raymondcoetzee '.*' '.*' '.*'

rabbitmqctl add_user monitoring monitoring --vhost influx
rabbitmqctl set_user_tags monitoring monitoring
rabbitmqctl set_permissions --vhost influx monitoring '.*' '.*' '.*'

rabbitmqctl add_user influx influx --vhost influx
rabbitmqctl set_permissions --vhost influx influx '.*' '.*' '.*'

```

Adding queues and exchanges via the commandline requires a different tool - [rabbitmqadmin](https://www.rabbitmq.com/management-cli.html). This requires a little extra setup:

```
pkg install python37 wget
wget http://localhost:15672/cli/rabbitmqadmin -O /usr/local/sbin/rabbitmqadmin
chmod a+x /usr/local/sbin/rabbitmqadmin
```

Now we can add the config that we'll need:

```
rabbitmqadmin declare exchange name=influx type=direct --vhost=influx --username=raymondcoetzee --password=foobar 
rabbitmqadmin declare queue name=metrics01 --vhost=influx --username=raymondcoetzee --password=foobar
rabbitmqadmin declare binding source=influx destination=metrics01 \
       routing_key=telegraf --vhost=influx --username=raymondcoetzee --password=foobar
```


### Step 4 - configure Telegraf 

Add the following stanzas to the /usr/local/etc/telegraf.conf file

```
[global_tags]
  dc = "study"

[agent]
  interval = "10s"
  round_interval = false
  metric_batch_size = 1000
  metric_buffer_limit = 10000
  collection_jitter = "0s"
  flush_interval = "10s"
  flush_jitter = "0s"

# # Publishes metrics to an AMQP broker
[[outputs.amqp]]
   brokers = ["amqp://localhost:5672/influx"]
   exchange = "influx"
   exchange_type = "direct"
   exchange_passive = false
   exchange_durability = "durable"
   username = "influx"
   password = "influx"
   #routing_tag = "influx"
   routing_key = "telegraf"
   delivery_mode = "persistant"
   use_batch_format = true

# generate some metrics off the rabbit mq instance
[[inputs.rabbitmq]]
    url = "http://localhost:15672"
    name = "rmq-server-1"
    username = "monitoring"
    password = "monitoring"

# Native Influx HTTP write listener
[[inputs.influxdb_listener]]
   service_address = ":8086"

# Collectd
[[inputs.socket_listener]]
  service_address = "udp://0.0.0.0:25826"
  data_format = "collectd"
  collectd_typesdb = ["/usr/local/share/collectd/types.db"]
    [inputs.socket_listener.tags]
      source = "collectd"

# Graphite
[[inputs.socket_listener]]
  service_address = "tcp://0.0.0.0:2003"
  data_format = "graphite"
  separator = "."
  templates = [
     "servers.* .host.resource.measurement*",
      "*.app env.service.resource.measurement"
    ]
    [inputs.socket_listener.tags]
      source = "graphite"

```

### Step 5 - verify that that metrics are being produced onto RMQ

You can use the ```rabbitmqadmin``` tool to query metrics01 queue. This will show if there are metrics coming through to queue:

```
rabbitmqadmin get queue=metrics01 --vhost=influx --username=raymondcoetzee --password=foobar

```


## Wiring it all up - the consumer + db

### Step 1 - create a jail

```
iocage create -n influxwriter01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.3-RELEASE boot=on
```

### Step 2 - Install influxDB and Telegraf

```
pkg update
pkg install telegraf influxdb

sysrc influxd_enable=true
service influxd start

sysrc telegraf_enable=true
service telegraf start

```

### Step 3 - setup influxDB

Given that all the smarts are in the telegraph instance on the other end, we can run with vanilla influxDB configuration.

### Step 4 - setup the rabbit consumer

There's a neat trick here where you can set the target database name using a tag. For backwards compatiblity I need all collectd metrics to go into a database called collectd and all graphite metrics to go into a database called graphite. This can be reliably achieved by setting a 'collectd' or 'graphite' tag on the socket configurations on the receiver config, and then setting ```database_tag="source"```. 

```
[agent]
  interval = "10s"
  round_interval = true
  metric_batch_size = 1000
  metric_buffer_limit = 10000
  collection_jitter = "0s"
  flush_interval = "10s"
  flush_jitter = "0s"
  precision = ""
  hostname = ""
  omit_hostname = false

[[outputs.influxdb]]
  urls = ["http://127.0.0.1:8086"]
  database = "telegraf"
  database_tag = "source"

[[inputs.amqp_consumer]]
   brokers = ["amqp://influx01.home:5672/influx"]
   username = "influx"
   password = "influx"
   queue = "metrics01"
   queue_passive = true

```

### Step 5 - verify you're getting your metrics

```
Connected to http://localhost:8086 version 1.7.9
InfluxDB shell version: 1.7.9
> show databases
name: databases
name
----
_internal
telegraf
graphite
collectd
> 


```

[^1]: as per http://blog.arainho.me/freebsd/top/io/2016/02/10/monitor-io-in-freebsd-with-top.html
