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

So I resolved to move InfluxDB off my precious FreeNAS server, and away from the hard drives that host my precious data. I 












[^1]: as per http://blog.arainho.me/freebsd/top/io/2016/02/10/monitor-io-in-freebsd-with-top.html
