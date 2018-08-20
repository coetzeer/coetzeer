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

Enter [IOCage](https://doc.freenas.org/11/jails.html#using-iocage).

IOcage, to quote it's [github page](https://github.com/iocage/iocage), is a FreeBSD jail manager written in python3. It has some cool features:
* It's maintained and up to date so I can do port builds jails created with it.
* All the stuff on the github page (which I will not regurgitate here)
* It's got ZFS baked into it. As I am discovering, things are better when they have ZFS inside.

Getting started was remarkably easy. IOCage uses what I call 'git style' command line arguments. There's a master program/ entry point that is easy to remember `iocage`. Everything else is a subcommand, and it's easy to get a list of them if you ever forget them, because you just type `icoage`

```bash
root@freenas:/ # iocage
Usage: iocage [OPTIONS] COMMAND [ARGS]...

  A jail manager.

Options:
  -v, --version  Display iocage's version and exit.
  -f, --force    Allow iocage to rename datasets.
  --help         Show this message and exit.

Commands:
  activate    Set a zpool active for iocage usage.
  chroot      Chroot to a jail.
  clean       Destroy specified dataset types.
  clone       Clone a jail.
  console     Login to a jail.
  create      Create a jail.
  destroy     Destroy specified jail(s).
  df          Show resource usage of all jails.
  exec        Run a command inside a specified jail.
  export      Exports a specified jail.
  fetch       Fetch a version of FreeBSD for jail usage or...
  fstab       Manipulate the specified jails fstab.
  get         Gets the specified property.
  import      Import a specified jail.
  list        List a specified dataset type, by default...
  migrate     Migrate all iocage_legacy develop basejails...
  pkg         Use pkg inside a specified jail.
  rename      Rename a jail.
  restart     Restarts the specified jails or ALL.
  rollback    Rollbacks the specified jail.
  set         Sets the specified property.
  snaplist    Show snapshots of a specified jail.
  snapremove  Remove specified snapshot of a jail.
  snapshot    Snapshots the specified jail.
  start       Starts the specified jails or ALL.
  stop        Stops the specified jails or ALL.
  update      Run freebsd-update to update a specified jail...
  upgrade     Run freebsd-update to upgrade a specified...
```

I took a shortcut on the first set of steps by using the FreeNAS gui (the fance new one). That landed up setting up the zpool and initializing the base images for me. If you were doing it yourself in FreeBSD you'd do something [like this](https://dan.langille.org/2015/03/07/getting-started-with-iocage-for-jails-on-freebsd/):

```bash
# iocage fetch
  please select a pool for iocage jails [system]:

```

This gives you the following ZFS layout:

```bash
root@freenas:/ # zfs list | grep iocage
vol01/iocage                                6.45G  3.65T  2.56M  /mnt/iocage
vol01/iocage/download                        260M  3.65T   141K  /mnt/iocage/download
vol01/iocage/download/11.1-RELEASE           260M  3.65T   260M  /mnt/iocage/download/11.1-RELEASE
vol01/iocage/images                          141K  3.65T   141K  /mnt/iocage/images
vol01/iocage/jails                          4.97G  3.65T   153K  /mnt/iocage/jails
vol01/iocage/jails/grafana                   135M  3.65T   147K  /mnt/iocage/jails/grafana
vol01/iocage/jails/grafana/root              135M  3.65T  1.33G  /mnt/iocage/jails/grafana/root
vol01/iocage/jails/influxdb                 1.58G  3.65T   147K  /mnt/iocage/jails/influxdb
vol01/iocage/jails/influxdb/root            1.58G  3.65T  2.78G  /mnt/iocage/jails/influxdb/root
vol01/iocage/log                             185K  3.65T   185K  /mnt/iocage/log
vol01/iocage/releases                       1.22G  3.65T   141K  /mnt/iocage/releases
vol01/iocage/releases/11.1-RELEASE          1.22G  3.65T   141K  /mnt/iocage/releases/11.1-RELEASE
vol01/iocage/releases/11.1-RELEASE/root     1.22G  3.65T  1.22G  /mnt/iocage/releases/11.1-RELEASE/root
vol01/iocage/templates                       141K  3.65T   141K  /mnt/iocage/templates
root@freenas:/usr/local/etc/netdata #

```

Above is standard layout that includes 2 jails: grafana and influxdb.

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
