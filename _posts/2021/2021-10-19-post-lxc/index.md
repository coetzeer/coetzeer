---
layout: post
title: "Ubuntu Impish. And Far Too Much Rum"
date: 2021-10-19
description: ""
img: lxc.jpg
tags: [ubuntu,rum,holiday,lxc,lxd,quickemu] # add tag
toc: true
---

I am in Mauritias, in a hotel called Shandrani. I've just crossed over into my second 24hrs here - so it's technically my second day, but the first day was spent recovering from the grualling 18 hours of airport dread that is a mandatory right of passage for a holiday in Mauritius. So technically, on my second day, I'm still settling in. I haven't shed the calcified shell of stress and andrenalin that's built up over days of back to back video calls and last minute home work assignments. I'm still finding bits of the hotel that I didn't know about: they Gym, the Spa, the Tennis court, the Boat house. I'm in the middle of a major, alcohol related over-correction. That is, in finding the balance of fun vs work, I have erred on the side of fun, and had one too many rums.

So I thought I'd fill the gaps of a an article that I started [back in 2019]({{site.baseurl}}/2019/06/26/lxc-fedora-centos-index.html), and never completed: that is the journey into the land of non-docker based containers: LXC and LXD. Previously, I looked at the redhat/fedora flavour of lxc. Today I shall be attempting to summarise getting started with the Ubuntu flavour of LXC - LXD (or Lex-D as it's known by the cool kids).

This coincides with 2 things: the recent release of Ubuntu Impish Indri (21.10) and a recent re-discovery of [quickemu](https://github.com/wimpysworld/quickemu). Both of these have been the news recently, the former because of it's release and the latter because of the recent support it's ganed for Windows 11 and MacOS Big Sur. These days, I run Arch (by the way), more specifically Garuda Linux. This is not out of an over inflated sense of my own abilities or yearning to join the rarified group of people that run Arch (by the way) - I'm running Garuda because it seems to have the best out of the box support for my Lenovo P14s with Nvidia. But that is a whole different blog article.

# LXD and LXC

LXD is unique to Ubuntu and it's derivatives. You can read about it more over [here](https://linuxcontainers.org/lxd/getting-started-cli/) - but it seems to have a few more manners than the centos/fedora version. It got a bit more of a modern CLI and supports remote servers and clustering, etc.

## Getting started

LXD usually comes down as a snap, although can you can build it from source and install it via a deb if you really feel like it.

```
impish:~$ snap install lxd
```

You then have to use a tool to generate a basic config file.

```
impish:~$ lxd init
```

This is the what my config landed up looking like:

```
impish:~$ lxd init --dump
config: {}
networks:
- config:
    ipv4.address: 10.46.237.1/24
    ipv4.nat: "true"
    ipv6.address: none
  description: ""
  name: lxdbr0
  type: bridge
  project: default
storage_pools:
- config:
    source: rpool/lxd
    volatile.initial_source: rpool/lxd
    zfs.pool_name: rpool/lxd
  description: ""
  name: default
  driver: zfs
profiles:
- config: {}
  description: Default LXD profile
  devices:
    eth0:
      name: eth0
      network: lxdbr0
      type: nic
    root:
      path: /
      pool: default
      type: disk
  name: default
projects:
- config:
    features.images: "true"
    features.networks: "true"
    features.profiles: "true"
    features.storage.volumes: "true"
  description: Default LXD project
  name: default

```

## Starting a container

As with the Centos/Fedora LXC and docker, you bootstrap new containers off images (which are different to templates). Images are hosted on servers on the internet. LXD has some built in config that points to a server that has some images pre-baked. To get a list of these images use this command:

```
impish:~$ lxc image list images:

+-------------------------------------------+--------------+--------+---------------------------------------------------+--------------+-----------------+-----------+-------------------------------+
|                   ALIAS                   | FINGERPRINT  | PUBLIC |                    DESCRIPTION                    | ARCHITECTURE |      TYPE       |   SIZE    |          UPLOAD DATE          |
+-------------------------------------------+--------------+--------+---------------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| almalinux/8 (3 more)                      | 1edf6ddaae16 | yes    | Almalinux 8 amd64 (20211026_23:08)                | x86_64       | CONTAINER       | 130.95MB  | Oct 26, 2021 at 12:00am (UTC) |
+-------------------------------------------+--------------+--------+---------------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| almalinux/8 (3 more)                      | 172cdd394515 | yes    | Almalinux 8 amd64 (20211026_23:08)                | x86_64       | VIRTUAL-MACHINE | 572.69MB  | Oct 26, 2021 at 12:00am (UTC) |
+-------------------------------------------+--------------+--------+---------------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| almalinux/8/arm64 (1 more)                | 94879d259dfd | yes    | Almalinux 8 arm64 (20211026_23:08)                | aarch64      | CONTAINER       | 127.48MB  | Oct 26, 2021 at 12:00am (UTC) |
+-------------------------------------------+--------------+--------+---------------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| almalinux/8/cloud (1 more)                | bf9fad8b1614 | yes    | Almalinux 8 amd64 (20211026_23:08)                | x86_64       | VIRTUAL-MACHINE | 595.69MB  | Oct 26, 2021 at 12:00am (UTC) |
+-------------------------------------------+--------------+--------+---------------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| almalinux/8/cloud (1 more)                | d38013aa1a32 | yes    | Almalinux 8 amd64 (20211026_23:08)                | x86_64       | CONTAINER       | 150.17MB  | Oct 26, 2021 at 12:00am (UTC) |
+-------------------------------------------+--------------+--------+---------------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| almalinux/8/cloud/arm64                   | 179ba23fbc71 | yes    | Almalinux 8 arm64 (20211026_23:08)                | aarch64      | CONTAINER       | 146.21MB  | Oct 26, 2021 at 12:00am (UTC) |
+-------------------------------------------+--------------+--------+---------------------------------------------------+--------------+-----------------+-----------+-------------------------------+
| alpine/3.11 (3 more)                      | 4c39ada55656 | yes    | Alpine 3.11 amd64 (20211026_13:00)                | x86_64       | CONTAINER       | 2.86MB    | Oct 26, 2021 at 12:00am (UTC) |

--- snip ---

```


Once you've picked a template you can start it up with this command:

```
lxc launch images:centos/8-Stream container2

```

## Now what

Check to see that your container started:

```
impish:~$ lxc list
+------------+---------+----------------------+------+-----------+-----------+
|    NAME    |  STATE  |         IPV4         | IPV6 |   TYPE    | SNAPSHOTS |
+------------+---------+----------------------+------+-----------+-----------+
| centos     | RUNNING | 10.46.237.105 (eth0) |      | CONTAINER | 0         |
+------------+---------+----------------------+------+-----------+-----------+
| container1 | RUNNING | 10.46.237.72 (eth0)  |      | CONTAINER | 0         |
+------------+---------+----------------------+------+-----------+-----------+
| container2 | RUNNING | 10.46.237.140 (eth0) |      | CONTAINER | 0         |
+------------+---------+----------------------+------+-----------+-----------+

```

Create a console into your container:

```
lxc exec container2 -- /bin/bash
```

## A Tmux bonus


This script sets up a lxc 'work environment':


```
#!/bin/sh
#
# copied from https://gist.github.com/todgru/6224848
#
# Setup a work space called `lxc`
# first window has 3 panes. 
# The first pane set at 65%, split horizontally, with a shell
# pane 2 is vertically split at 50% and running an auto refreshing list of containers 
# pane 3 is set to top
#
session="work"

# set up tmux
tmux start-server

tmux new-session -d -s $session  -n lxc

# Select pane 1, set dir to api, run vim
tmux selectp -t 1 


# Split pane 1 horizontal by 65%, start redis-server
tmux splitw -h -p 35
tmux send-keys "watch -n 10 lxc ls" C-m

# Select pane 2
tmux selectp -t 2
# Split pane 2 vertiacally by 25%
tmux splitw -v -p 50

# select pane 3, set to api root
tmux selectp -t 3
tmux send-keys "top" C-m

tmux attach-session -t $session

```

# A quick reference

| Command         | LXC (Fedora)     | LXD (Ubuntu) |
|--------------|-----------|------------|
| Get a list of available images | ```lxc-create -t download -n container1```      | ```lxc image list images:```        |
| Create a container      | (follow the wizard after the download step)  | ```lxc launch images:Centos/Stream-8 container1```
| List running containers |  ```lxc-ls -f```  | ```lxc list```
| Stop a container | ```lxc-stop --name container1```  | ```lxc stop container1```     
| Start a container |  ```lxc-start -d --name container1```  |   ```lxc start container1```    
| Console |   ```lxc-attach --name container1``` |  ```lxc exec container1 -- /bin/bash``` 


