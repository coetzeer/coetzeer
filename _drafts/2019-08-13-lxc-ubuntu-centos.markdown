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

The second way they keep things interesting is by mixing up how lxd is installed. I was very confused by this at first, but there is a subtle difference in how lxd and lxc are installed depending on whether you're using the snap for LXD or the dep package. This correlates with whether you are using the Workstation edition or the Server edition of Ubuntu, although not 100%: if you're using the Server edition, it comes with the LXD deb package pre-installed. If you're using the Desktop Edition, it really depends. For brevity, and because it was my use-case, I'll focus on the dev/Server Edition commands, and summarize some differences you might see at the end of the article.


1. LXC/LXD comes preinstalled but not enabled
2. There is no LXC service, only a LXD service (Note: this is what the LXD systemctl status looks like until you run lxd init.)

```
coetzeer@vbox-ubuntuserver:~$ systemctl status lxd
● lxd.service - LXD - main daemon
   Loaded: loaded (/lib/systemd/system/lxd.service; indirect; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:lxd(1)

```

{:start="3"}

3. LXD is installed a regular apt package, with a regular binary running under ```/usr/bin/lxd```

```
coetzeer@vbox-ubuntuserver:~$ which lxd
/usr/bin/lxd
```

__On the Workstation Edition__

1. LXD/LXC does NOT come preinstalled
2. You can install lxd using a snap [^1] or a traditional package. If you install it with the snap, your executable lives at a different place (/snap/bin/lxd instead of /usr/bin/lxd). 

```
coetzeer@coetzeer-VirtualBox:~$ which lxd
/snap/bin/lxd
```

{:start="3"}

3. If you install lxd using a snap, you get an lxc.service systemd service (seems to be a short lived exec rather than a daemon), but not an LXD service. LXD as a daemon would be managed by snapd:

```
● lxc.service - LXC Container Initialization and Autoboot Code
   Loaded: loaded (/lib/systemd/system/lxc.service; enabled; vendor preset: enabled)
   Active: active (exited) since Wed 2019-09-04 18:01:09 IST; 11min ago
     Docs: man:lxc-autostart
           man:lxc
  Process: 973 ExecStart=/usr/lib/x86_64-linux-gnu/lxc/lxc-containers start (code=exited, status=0/SUCCESS)
  Process: 956 ExecStartPre=/usr/lib/x86_64-linux-gnu/lxc/lxc-apparmor-load (code=exited, status=0/SUCCESS)
 Main PID: 973 (code=exited, status=0/SUCCESS)

```

{:start="3.1"}

3.1 If you install LXD using a traditional package you get both the lxd and lxc systemd units.

Clear as mud.

# Initializing lxc

Before you can start using lxc, you need to execute ```lxd init``` to setup storage, networking, etc.

__Server__

```
● lxd.service - LXD - main daemon
   Loaded: loaded (/lib/systemd/system/lxd.service; indirect; vendor preset: enabled)
   Active: active (running) since Wed 2019-09-04 17:50:59 UTC; 2min 49s ago
     Docs: man:lxd(1)
  Process: 1508 ExecStartPost=/usr/bin/lxd waitready --timeout=600 (code=exited, status=0/SUCCESS)
  Process: 1491 ExecStartPre=/usr/lib/x86_64-linux-gnu/lxc/lxc-apparmor-load (code=exited, status=0/SUCCESS)
 Main PID: 1507 (lxd)
    Tasks: 13
   CGroup: /system.slice/lxd.service
           ├─1507 /usr/lib/lxd/lxd --group lxd --logfile=/var/log/lxd/lxd.log
           └─1703 dnsmasq --strict-order --bind-interfaces --pid-file=/var/lib/lxd/networks/lxdbr0/dnsmasq.pid --except-interface=lo --interf


```

```yaml

config: {}
networks:
- config:
    ipv4.address: auto
    ipv6.address: none
  description: ""
  managed: false
  name: lxdbr0
  type: ""
storage_pools:
- config: {}
  description: ""
  name: default
  driver: dir
profiles:
- config: {}
  description: ""
  devices:
    eth0:
      name: eth0
      nictype: bridged
      parent: lxdbr0
      type: nic
    root:
      path: /
      pool: default
      type: disk
  name: default
cluster: null

```

__Desktop__





        lxc launch images:centos/7 centos


# Somethings that go wrong

[^1]: On later ubuntu desktop editions (older than 18.04) you only get the nsap



config: {}
networks: []
storage_pools: []
profiles:
- config: {}
  description: ""
  devices:
    eth0:
      name: eth0
      nictype: bridged
      parent: lxdbr0
      type: nic
  name: default
cluster: null


