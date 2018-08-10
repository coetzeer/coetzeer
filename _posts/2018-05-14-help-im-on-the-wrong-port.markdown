---
layout: post
title: Help! I'm on the wrong port
date: 2018-05-14
description: How to redirect from one port to another
img: 20180301-19-Lunch_with_Andy.jpg # Add image post (optional)
tags: [ports] # add tag
toc: true
---

One of my little bug bug bears is when you install a new program and it comes up on a random port. The first couple of apps you can remember fine: transmission is 9091, nexus is 8081, the weblogic admin server is 7001 (why do I still remember that?) But now, nearing my 40's, I can't be bothered to remember the default plex port, or where Jira is lurking or what magic numbers I need to put in order to get to confluence.

So, what are the options:
* crack open each application, wade through the config and change to port to 80. This of course means that either the application has to run as root, or you fiddle with your ``/etc/sysctl.conf`` by adding ``net.inet.ip.portrange.reservedhigh=79``. But those two steps are the opposite of 'hardening', especially if the  studious package maintainer has gone to all the effort of setting up the application with a non-root user.
* You can install nginx in the same jail and reverse proxy the web requests. That is fine, but if you've got a lot of jails then that's a lot nginx instances that you have hanging around.
* You can install one nginx instance and set up lots of upstreams. You can set up a load of cnames to that nginx instance and let nginx to decide which upstream based on a [server name](http://nginx.org/en/docs/http/server_names.html). This is a great solution for the enterprise but it means you need two sets of names: a cname for 'plex' and an arecord for the actual plex server.
* You can use a firewall rule on each jail to forward network packets from one port to another. While that is great, frankly IP tables is right up there with regular expressions for things on my learning bucket (list that I'm never going to get to). I had a brief look around at freebsd firewall rules and it made my brain hurt.
* Use rinetd - the TCP port forwarding daemon.
  * Now, I don't know how it performs against the likes of nginx. It might be shite.
  * It's old and crufty compared to the shiny sexiness that is nginx. Nginx plus has a DASHBOARD!
  * You need an ip address, which I suppose isn't the end of the world. I would have preferred to say 'eth0', but rinet is the internet relay daemon, so you could be routing from eth0 to something on the other end of the network, so I'll let this one pass.
  * it works, in 3 lines of config.

# Step 1: install

```
pkg install rinetd
sysrc rinetd_enable="YES"
```

# Step 2: configure

```
vi /usr/local/etc/rinetd.conf


# bindadress    bindport  connectaddress  connectport
192.168.0.44 80 192.168.0.44 8081
logfile /var/log/rinetd.log
logcommon
```

# Step 3: restart

```
service rinetd start
```

{% include figure image_path="/assets/img/20180301-19-Lunch_with_Andy.jpg" alt="Spring time for happiness" class="image-medium image-centre" caption="Spring in Dublin" %}

# About the picture:
This photo was taken in Powerscourt in Spring 2018.
