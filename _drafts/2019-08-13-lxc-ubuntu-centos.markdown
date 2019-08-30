---
layout: post
title: "LXC on Ubuntu"
date: 2019-08-13
description: How to setup and start working with LXC on Fedora and Centos 7
img: lxc_fedora.jpg # Add image post (optional)
tags: [lxc,containers] # add tag
toc: true
---

# Installing LXC/LCD on Ubuntu

Ubuntu keeps things interesting. Firstly they have broken LXC in 2: there's now LXC and LXD. Rather, they've layered LXD as a management daemon on top of LXC, according to the [Offical Website](https://linuxcontainers.org/lxd/introduction/). So now instead of having a set of different command line utilities called '```lxc-*```' (lxc-top, lxc-ls, etc), there is now only one command with many different sub commands e.g. ```lxc launch``` and ```lxc stop```

The second way they keep things interesting is by mixing up how lxd is installed. I was very confused by this at first, but there is a subtle difference in how lxd and lxc are installed depending on whether you're using the Workstation edition or the Server edition of Ubuntu.

__On the Server Edition__

1. LXC/LXD comes preinstalled but not enabled
2. There is no LXC service, only a LXD service

```buildoutcfg
coetzeer@vbox-ubuntuserver:~$ systemctl status lxd
● lxd.service - LXD - main daemon
   Loaded: loaded (/lib/systemd/system/lxd.service; indirect; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:lxd(1)

```
3. LXD is installed a regular apt package, with a regular binary running under ```/usr/bin/lxd```

```shell script
coetzeer@vbox-ubuntuserver:~$ which lxd
/usr/bin/lxd
```

__On the Workstation Edition__

1. LXD/LXC does NOT come preinstalled


        lxc launch images:centos/7 centos


# Somethings that go wrong

