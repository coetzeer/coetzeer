---
layout: post
title: Getting the hang of iocage
date: 2018-05-19
description: Making jails with iocage just got easier. For me anyway...
img: i_got_the_hang_of_iocage.png # Add image post (optional)
tags: [iocage, warden, jails ] # add tag
toc: true
---

I've had what I like to think of an impedance mismatch between warden and iocage. Warden jails and the corresponding functionality in the FreeNAS GUI are super for beginners, a label that I own with pride (and as a massive disclaimer for any thing I say or write). It's really inviting and easy to make a jail if you want to experiment with something like postgres, pgadmin, remote logging services, jira, etc.

{% include figure image_path="/assets/img/openzfs.png" alt="Life withouth ZFS would be like a melted box of chocolates." class="image-small image-right" caption="Things are better with ZFS inside." %}

However, you soon find out that with the current state of FreeNAS and FreeBSD, installing packages can be somewhat hit and miss in the Warden managed jails. Both package mechanisms (ports and pkg) warn about the kernel version not matching the distribution version, which you can either ignore or accept, and in most cases it's fine. For example I can install nano into a Warden managed jail, hit Y a bunch of times and achieve my goal of not having to us vi.

Enter [IOCage](https://doc.freenas.org/11/jails.html#using-iocage).\\

IOcage, to quote it's [github page](https://github.com/iocage/iocage), is a FreeBSD jail manager written in python3. It has some cool features:
* It's maintained and up to date so I can do port builds jails created with it.
* All the stuff on the github page (which I will not regurgitate here)
* It's got ZFS baked into it. As I am discovering, things are better when they have ZFS inside.

Getting started was remarkably easy. IOCage uses what I call 'git style' command line arguments. There's a master program/ entry point that is easy to remember `iocage`. Everything else is a subcommand, and it's easy to get a list of them if you ever forget them, because you just type `ioage`

```
TODO: add in some iocage example ouput
```

I took a shortcut on the first set of steps by using the FreeNAS gui (the fance new one). That landed up setting up the zpool and initializing the base images for me. If you were doing it yourself in FreeBSD you'd do something [like this](https://dan.langille.org/2015/03/07/getting-started-with-iocage-for-jails-on-freebsd/):

```
# iocage fetch
  please select a pool for iocage jails [system]:

```

This gives you the following ZFS layout:

```
TODO: insert ZFS layout
```

 I had to do some digging around for the right command line options for creating a jail outside of the FreeNAS gui - all the examples I found were using static IP addresses, and I really wanted a DHCP enabled jail. This landed up working for me:

 ```
iocage create -n postgres dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE
iocage start postgres
 ```

Breaking that down:
* Create me a jail with name postgres
* Switch on dhcp
* For postgress you need the allow_sysvipc tunable set.
* Enable BPF - berkley packate filter (does the natting?)
* Enable VNET - gives the jail it's own network stack
* use release 11.1-RELEASE

Once those two commands are issued, then you should land up with jail that has an IP address from your DHCP server. If you're like me, and you're using a [PiHole](https://pi-hole.net/) - then the jail will also be available on your home network by it's host name. That is some awesome network magic right there.

I landed up doing what I usually do and taking this to the n'th degree. I thought: this is like having my own little VM lab. If can get this onto my laptop somehow then I can experiment with jails on the bus! And the hour going into and coming back from work needn't be a time that I spend doing wasteful things like writing blog posts!

So I installed the FreeBSD VM image (the official one on https://www.freebsd.org/where.html) onto my laptop and ran in the magic commands to setup IOCage. And it failed miserably, because the FreeBSD Kernel in the offical VM Image didn't have VNET enabled (makes sense? Why would you virtualize your network stack in a virtual machine? You've never been my head...). Back to the Google based drawing board: how do you recompile your FreeBSD kernel.

Well, it's easier than you think. The basics are documented helpfully [here in the FreeBSD manual](https://www.freebsd.org/doc/handbook/kernelconfig-building.html) and a VNET specific example was done [here](https://gist.github.com/sdebnath/086874c5df8b68e0df69). I won't go into the gory details - the wrap up here is that it was underwhelmingly easy, and I learned a great deal.

On with the IOCage projects now!
