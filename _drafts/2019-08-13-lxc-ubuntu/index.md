---
layout: post
title: "LXC on Ubuntu"
date: 2019-08-13
description: How to setup and start working with LXC on Fedora and Centos 7
img: lxc_ubuntu.jpg # Add image post (optional)
tags: [lxc,containers] # add tag
toc: true
---

# Installing LXC/LCD on Ubuntu

        lxc launch images:centos/7 centos
        
```yaml        
cat <<EOF | lxd init --preseed
config:
  core.https_address: '[::]:8443'
  core.trust_password: Bl0ss0m01
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
EOF
``` 
        
# Introduction

I've already written about setting up LXC on Fedora/Centos/Rhel. This article goes into some detail on how to setup lxc, and it's ubuntu-specific management daemon, LXD.

Why are they different? Ubuntu seem to have done a large amount work on LXC in relation to their MAAS (Metal as a service) product line. As such, the command line and whole mechanism of interacting with LXC on Ubuntu is VERY different. There are some hacks out there that explain how to install LXD onto Centos using a SNAP, but in my limited experience, provided you're not doing anything too fancy like clustering your LXC servers, the standard non-Ubuntu command line is just fine.

# What is LXD?

LXD (or LX Daemon) is a management layer written on top of lxc (linux containers). Lxc comes with it's own set of command line tools, some of which we talked about in the previous article - these are  the ones prefixed with 'lxc-'. LXD has it's own command line wrapper around liblxc, providing a similar but not quite exactly the same user experience. LXD also has an rest layer built into the daemon that you can choose to expose. Canonical have use this layer to hook LXD into other technologies like OpenNebula and OpenStack.

# Installation and setup

The first thing you have to do is install LXD. From what I've seen, depending on certain things like which version of Ubuntu you're using (18.04 or 19.04) or what flavour you're using (Server or Desktop), you might or might not get a SNAP package when you install lxd

Either way, you get going by installing lxd via apt:

```bash
root@ubuntu-bionic:~# apt install lxd
```

TODO: find out what causes this:

If you've gone snap route, you should have a lxd systemd unit running


This should give you a systemd daemon:



On a 18.04 vagrant box, this gives you:


 

 



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


