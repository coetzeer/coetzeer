---
layout: post
title: "Penguins everywhere: getting cozy with Linux"
date: 2019-01-16
description: Installing the cloud that I probably won't use
img: penguins.jpg # Add image post (optional)
tags: [linux, Ubuntu, Mate, Audio] # add tag
toc: true
---


We're a month into the new year, and the Christmas break feels like a memory from my dim and distant past. There was a Turkey. There was a lovely day with my friends. There was even a night out at my local pub. But all that is behind me now. Boo.

# Getting to grips with a distro

It's been a long standing desire of mine to put together a Linux or FreeBSD desktop. I talk a lot of big talk about being a FOSS supporter, but I've made the pragmatic choice in the past to use Windows so I could play games and use the Adobe products that I pay money for each month. There's also a piece of me that's scared to let go of something that I know is stable - or at least boring enough that it never find it compelling enough to mess with. My thought process was: I work with computers (linux servers) all day long, the last thing I want to do when I get home is peer into 'top' output or 'grep' something. So due to this faulty logic, I've remained shackled to Windows for what feel like a very long time.

So what changed? Well, a few things.

{% include figure image_path="/assets/img/badmac.jpg" alt="Wretched. The definition of." class="image-small image-left" caption="If your mac book pro does this, put the kettle on, it's going to be a long night." %}

During the course of 2017, my wife's MacBook Pro died. It is a 2011 model that has a AMD Radeon discreet graphics card that is known to become faulty as the device get's a bit older. I was able to resurrect OSX but I had to perform a VERY strange and arcane set of steps that allows you to remove the Radeon drivers, and this forced the laptop and OS to boot with the embedded Intel graphics. This is MUCH harder that the previous sentence would lead you to believe. It took me 2 days and involved an Arch Linux rescue USB stick followed by more restarts that a single person's sanity would allow. AND then when OSX gets updates you have to do the same thing all over again. [^1]

The other thing that's happened is that a friend told me about 'bargainhardware.co.uk'. This nugget of information has turned out to be good for bargainhardware, but bad for my wallet. In the space of a few months, I'd purchased not one but 2 new desktop machines. One was their bottom of the line models, which came with a quad core AMD phenom, 8 gigs of ram and a 500Gb hard drive. The best thing of all was that it cost £100. I am always a bit distrustful of sites on the internet that seem to be offering a good deal, so I thought that dipping a toe in the water with a £100 machine wasn't taking too much of a risk.

{% include figure image_path="/assets/img/hp_sff.png" alt="The test case." class="image-small image-right" caption="The test case: a SFF machine from BargainHardware.co.uk" %}

My test case worked out well, and in a week I was a proud owner of an ex-office Small Form Factor HP machine. I added a 128Gb SSD to it, and threw in an old AMD Radeon card that I had lying around, and all of a sudden I had a VERY serviceable machine with which I could experiment.

So the configuration that I have now is: KDE Neon developers edition (git stable) running on the 2011 macbook pro, and Ubuntu Mate 18.10 running on the HP Compaq 6005.

# Experimenting

The laptop installation is pretty stable (even though it's on the developer edition). Every now and then strange things happen e.g. this week, for some strange reason, the laptop randomly restarted itself a few times. I updated the packages, and the problem went away. This leads me believe that there might have been a power management bug or some other similar low level foible that snuck into the dev packages. This is not the first time that it's happened, and the problems seem to iron themselves out within a few days. All in all, the experience using that edition of KDE is very positive.

On the desktop machine, wanted to experiment with Ubuntu Mate. I had plans for turning that into an audio work station for podcast recording and editing, so I wanted a DE that would get out of my way. On route to experimenting with audio packages, I had some disasters: some of my own making and some stemming from some extreme ignorance of around Pulse Audio and ALSA (I'm not going to describe this learning process right now. Too soon. The embarrassment... too soon.)

I had tried installing Timeshift [^2] after hearing the folks on the Linux Mint podcast talk about it, and also after reading an article from Jason Evangelho [^3]. Of course, if you're not prepared for how timeshift works, and you're not rocking BTRFS on your root device, then you're going to have live with the rsync capabilities that timeshift offers. Having spent the last few months playing with ZFS, I was not happy with rsync, so for one last time I formatted everything and re-installed with a root device on BTRFS, and a separate hard drive for my home partition, also running BTRFS.

This worked well. Until it didn't. There are some bugs with timeshift. One of which is that loopback mounts such as those created by docker and snapd cause the cleanup process to go bananas. Instead of just removing snapshots outside of your configured retention period, it removes everything. But, while using Timeshift I discovered apt-btrfs-snapshot [^4]. This works like an absolute charm: in the event of a package causing an irreparable mess, boot into rescue mode and select the snapshot that was taken when you hit enter on 'apt install thing_that_kills_pcs'.

```
Get:1 http://ie.archive.ubuntu.com/ubuntu cosmic-updates/main amd64  25-1ubuntu1.2 [91.2 kB]
Get:2 https://dl.winehq.org/wine-builds/ubuntu cosmic/main amd64  4.2~cosmic [1,932 B]
Get:3 http://ie.archive.ubuntu.com/ubuntu cosmic-updates/main amd64  25-1ubuntu1.2 [41.7 kB]
Get:4 https://dl.winehq.org/wine-builds/ubuntu cosmic/main amd64  4.2~cosmic [2,823 kB]
Get:5 https://dl.winehq.org/wine-builds/ubuntu cosmic/main amd64  4.2~cosmic [25.2 MB]
Get:6 https://dl.winehq.org/wine-builds/ubuntu cosmic/main i386  4.2~cosmic [23.8 MB]
Fetched 52.0 MB in 35s (1,495 kB/s)
Supported
Create a snapshot of '/tmp/apt-btrfs-snapshot-mp-n13smprt/@' in
 '/tmp/apt-btrfs-snapshot-mp-n13smprt/@apt-snapshot-2019-02-24_22:18:30'

```

Get a list of of snapshots:
```
btrfs subvolume list -s /

ID 572  2019-02-24 16:03:19 path @apt-snapshot-2019-02-24_16:03:19
ID 573  2019-02-24 16:03:27 path @apt-snapshot-2019-02-24_16:03:27
ID 574  2019-02-24 16:03:41 path @apt-snapshot-2019-02-24_16:03:41
ID 576  2019-02-24 22:18:30 path @apt-snapshot-2019-02-24_22:18:30
```

This functionality, while it took a little while to get used to, is awesome for experimenting. My fingers are itching to re-install the KDE Neon laptop with this BTRFS configuration.

# Conclusion

Yay for linux. Yay for Ubuntu. Yay for BTRFS. Yay for bargainhardware. Boo for my wallet. Coming soon: travels in Linux Audio: how to navigate the pitfalls of PulseAudio and Jack.

[^1]: Read all about the trials and tribulations of trying to fix a 2011 mac book pro  <a href="https://forums.macrumors.com/threads/force-2011-macbook-pro-8-2-with-failed-amd-gpu-to-always-use-intel-integrated-gpu-efi-variable-fix.2037591/">here</a>. Some bright folks have wrapped up the fix into a nice bootable image - find them <a href="https://realmacmods.com/macbook-2011-radeon-gpu-disable/">here</a>
[^2]: Some <a href="https://itsfoss.com/backup-restore-linux-timeshift/">good instructions</a> on how to install Timeshift.
[^3]: Can't seem to find the exact article right now, but you can find Jason's excellent articles <a href="https://www.forbes.com/sites/jasonevangelho/#517e84d7d36f">here</a>.
[^4]: For all your snapshotting needs: <a href="https://www.howtoforge.com/rollback-to-a-working-state-with-btrfs-plus-apt-btrfs-snapshot-on-ubuntu-12.10">apt-btrfs-snapshot</a>
