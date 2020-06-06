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


{% mermaid %}
graph LR
    A[Freenas] -- publishing graphite --> D
    B(Raspberry Pi) -- publishing collectd --> D
    C[Telegraph] -- publishing native Influx format --> D
    D{InfluxDB} --  native influx queries --> E((Grafana)) 
    
{% endmermaid %}

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

This setup worked well, until I had the recent hardware failure that took out my Freenas box. Since then I've been a bit more aware of the workloads that are happening on that machine. It used to live on the bottom rack of my 12u Startech rack, out of site and largely out of mind. However, now it lives on top, and I hear every time there is a significant IO load making the hard drives work. 

With a little bit of digging around [^1] I found that there were 2 main culprits of constant, low grade IO overhead: influxDB and consul. Given that I am pushing quite a few metrics through to this server, it made sense that there would be at least some, constant IO workload. 

So I resolved to move InfluxDB off my precious FreeNAS server, and away from the hard drives that host my precious data. Right before my FreeNAS hardware failure, I had purchased a machine that I had intended to use for backups. Having done a bit of rethinking about hardware failures and how backups should work, I decided that the new machine [Thraskii](/docs/gear.html#thraskii) would be better used as lab: somewhere to test out new ideas, where the cost if it dying is not too high, and where I don't mind giving it a good kicking every now and then. So, Thraskii is the new home.

Another goal I wanted to accomplish was to find a reasonable way to replicate metrics out to another instance. The commercial version of InfluxDB contains support for HA, multiple instances, replication etc, but I don't want a full enterprise stack, and of course I don't really want to pay. I could just do regular snapshots - that's an option.

## The Telegraf swiss army knife

What I decided on doing was to add a layer or 2 of indirection. After reading a bit about Telegraf (the Influx native metrics retriever/swiss army knive), I could see that it was straight forward enough to set up a Telegraf instance as a proxy to forward metrics streams to an InfluxDB instance. What's more, it can use RabbitMQ as transport for sending the metrics to the InfluxDB server. This was a compelling way to achieve a level of HA, as it's trivial to setup 2 or more queues inside RabbitMQ that get messages from the same Exchange i.e. it's setup as a topic that queues can subscribe to. 

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


With even more digging I could see that the clever people from Influx have decoupled support for handling inputs for different storage backends into 'how' the data is received and the exact format in which it is received. This [page](https://github.com/influxdata/telegraf/blob/master/docs/DATA_FORMATS_INPUT.md) on their github shows the different formats that they support: collectd, csv, Graphite, nagios to name but a few. The example on that page shows an 'exec' plugin that is expecting data in a 'json' format. What I wanted to do was to open two different TCP connections, one that received data in a collectd format, the other to receive it in a Graphite format. This was fairly straight forward to do:














[^1]: as per http://blog.arainho.me/freebsd/top/io/2016/02/10/monitor-io-in-freebsd-with-top.html
