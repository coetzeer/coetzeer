---
layout: post
title: "LXC on Fedora and Centos 7"
date: 2019-06-26
description: How to setup and start working with LXC on Fedora and Centos 7
img: lxc_fedora.jpg # Add image post (optional)
tags: [lxc,containers] # add tag
toc: true
---

I thought I'd write these instructions down, along with a followup article on setting up lxc/lxd for ubuntu based distros, as lxc is slightly different there.

# Why Bother with LXC at all?

It's a good question. LXC is not like docker or snaps. When you start an LXC container you get a blank page, almost like a newly installed Virtual Machine. It supports the idea of snapshots and clones, but the it's missing the bias towards packaging a particular app or process that docker has.

LXC seems to have trod a delicate line between **_using_** container technology (like docker and snaps) to achieve a runtime / operational experience that is closer to a traditional VM. That makes it extremely un-opinionated, which in the strongly opinionated world that grown up around technologies like Kubernetes and Docker Swarm, the 'blank page' approach to containers might leave one shaking one's head thinking 'but what good is it?'

Well, at the risk of repeating myself for a third time, it's for circumstances where the blank page is what you require. By definition, those are hard enumerate, but I'll recount some experiences that I've had in the last few years where these containers could have come in handy.

1. Anywhere where you can't use Docker. Yes - there are places where usage of the Docker or Containerd runtime is in violation of the standing security policies of the organisation. In this case, LXC, presenting a more traditional and consumable operator experience might a better technology choice, and perhaps even bridge the gap into more ephemeral container tech.
2. Anywhere where you need to act like a VM. We had some resource constraints in a project where we needed 2 environments, but only had enough compute for 1. They could only run one at a time i.e. when you switched on Env A, you had to switch off Env B. Orchestrating this at an application level was a nightmare. Also, everything used systemd natively, which made the docker option awkward. The installation scripts where all setup to run against a VM like target. So splitting each server into 2 lxc containers made perfect sense.
3. I have a jenkins build cluster that has some builds that use some things that are peculiar to RHEL6. RHEL 6 is long dead and buried from an application runtime perspective, but these builds have some tool chains that rely on particular paths and environments that means you can't just lift them up and put them on a new server. Could they ported? Probably, but they don't belong to me, and I my solution of just creating a RHEL6 jenkins executor cluster off the back of some of the RHEL7 ones means that I don't have to beg people to port builds that they don't want to. In fact, as long as I keep a copy of the RHEL6 container somewhere, the chances are that it will carry on working into foreseeable future.

So there - 3 situations where I've used LXC to make my life easier.

# Getting started with Fedora

Fortunately with Fedora (Fedora 30 in this case) you don't need any additional repos setup. Doing something like the following should be enough to get you started:

```bash
dnf install lxc lxc-templates
```

Check to see that the lxc daemon came up a cleanly:

```bash
[root@combretum ~]# systemctl status lxc

● lxc.service - LXC Container Initialization and Autoboot Code
   Loaded: loaded (/usr/lib/systemd/system/lxc.service; enabled; vendor preset: disable>
   Active: active (exited) since Sun 2019-06-30 18:29:35 IST; 4h 37min ago
     Docs: man:lxc-autostart
           man:lxc
  Process: 1188 ExecStartPre=/usr/libexec/lxc/lxc-apparmor-load (code=exited, status=0/>
  Process: 1191 ExecStart=/usr/libexec/lxc/lxc-containers start (code=exited, status=0/>
 Main PID: 1191 (code=exited, status=0/SUCCESS)

```

Now comes some undocumented black magic: you need to download and start a container image. But where do you get them from? You could make one yourself, but that's a pain. If you do something like this, however, you'll get a neat wizzard that steps you through some downloadable options:


```sh
[root@combretum ~]# lxc-create -t download -n container1

```

The magic 'type' option here of 'download' kicks off a nice set of self explanatory listings and questions that firstly show you what's available, and then let you select from the list in a sane way.

```
Setting up the GPG keyring
Downloading the image index

---
DIST	RELEASE	ARCH	VARIANT	BUILD
---
alpine	3.10	amd64	default	20190630_13:00
alpine	3.10	arm64	default	20190630_13:00
alpine	3.10	armhf	default	20190630_13:01
alpine	3.10	i386	default	20190630_13:00
alpine	3.10	ppc64el	default	20190630_13:00

...etc

```

When prompted, put in your distribution name, version and CPU architecture, and sit back and relax as your new container starts itself up.

```
Distribution: 
centos
Release: 
7
Architecture: 
amd64

```

# Some useful commands

Start your new container with '-d' to detach stdin:

```
lxc-start -d -n container1
```

To see a list of running containers:

```
lxc-ls -f

NAME	   STATE   AUTOSTART GROUPS IPV4           IPV6 UNPRIVILEGED
container1 RUNNING 0         -      192.168.122.53 -    false
ftp        STOPPED 0         -      -              -    false
  
```

To make it a bit more persistent, wrap it in _watch_

```
watch -n 5 lxc-ls -f
```
More useful info can be found in lxc-top

```
Container                   CPU          CPU          CPU                                BlkIO        Mem      MemSw       KMem
Name                       Used          Sys         User                    Total(Read/Write)       Used       Used       Used
container1                 0.93         0.35         0.57         8.00 KiB(  0.00   /8.00 KiB)  27.58 MiB  27.58 MiB   7.24 MiB
TOTAL 1 of 1               0.93         0.35         0.57         8.00 KiB(  0.00   /8.00 KiB)  27.58 MiB  27.58 MiB   7.24 MiB
```


# A tmux bonus

```
#!/bin/sh
#
# copied from https://gist.github.com/todgru/6224848
#
# Setup a work space called `lxc`
# first window has 3 panes. 
# The first pane set at 65%, split horizontally, with a shell
# pane 2 is vertically split at 50% and running an auto refreshing list of containers 
# pane 3 is set to lxc-top
#
session="work"

# set up tmux
tmux start-server

tmux new-session -d -s $session  -n lxc

# Select pane 1, set dir to api, run vim
tmux selectp -t 1 


# Split pane 1 horizontal by 65%, start redis-server
tmux splitw -h -p 35
tmux send-keys "watch -n 10 lxc-ls -f" C-m

# Select pane 2
tmux selectp -t 2
# Split pane 2 vertiacally by 25%
tmux splitw -v -p 50

# select pane 3, set to api root
tmux selectp -t 3
tmux send-keys "lxc-top" C-m

tmux attach-session -t $session

```

