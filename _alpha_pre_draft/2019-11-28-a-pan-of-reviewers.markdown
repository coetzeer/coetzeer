---
layout: post
title: "A Pan of Reviewers"
date: 2019-11-28
description: Starting a journey of distro reviews
img: pan_of_reviewers.jpg # Add image post (optional)
tags: [distro] # add tag
toc: true
---

# The silence is deafening

This year has been a tough year. It's a tough feeling to pin down: I don't think 2019 has been harder than 2018 from an objective point of view, but I can put my hand my heart and say that I had less free time this year. Last year, I had regular Saturday morning trips to Clontarf in the morning, where my wife would go off and do her thing for an hour or so, I would sit in [Ebb and Flow](https://www.ebbandflow.ie/) and watch all the dog walkers and Saturday morning bootcampers get their weekend treats and caffiene injections. Occasionally I would take a break from my bemused watching of people to heave the contents of my mind into a markdown editor for the world in general to consume. 

The realization I've had is that there just hasn't the be space for 'mind heaving'. There has been free time, but it's been used up by other stuff. There hasn't been a concerted effort to push other things to one side. So, now, it is with renewed vigor that I am clawing back some writing space from the x-box, the dogs and piles of crap in my office. I have brainstormed a list of lovely things to write about, and I am going to sit down and write about them. Hold onto your hat.

# A break in the drought

I've decided to write a series of articles that are reviews of distro's that I've been meaning to try out but have never had reason or opportunity to do so.  This would include the likes of Deepin, ZorinOS, PeppermintOS and MX Linux. I typically stick to upstream distros: Fedora and Ubuntu. "Why?" I hear you ask. Well, for no other reason that the upstreams probably have more sticking power than smaller, more boutique downstream distros.

Why does the world need another distro review? Well, it doesn't really. Reviews are opinions [^1], and the Internet is a planet sized dumpster fire of opinions. The world right now really doesn't need another opinion. Rather, as with purpose of the other articles on this blog, this is less of a review and more evidence of evening spent doing something other than  tinkering randomly. It's an attempt to order my thoughts, and lend some structure to something that I just felt like doing: installing Zorin OS and playing with it. I'm not being paid. I doing this because I  want to exercise my curiosity, my brain, and finally, my writing skills. 

In doing so I've played with other things: phoronix test suite, Evolution, Brasero, and a heap of other things that I normally don't go near. This has been an open source software adventure,  a rabbit hole that Alice would  be proud of.

# The  methodology

There are 2 aspects to this adventure:
1. Walk a mile in the moccasins. This is the easier of the two aspects. The goal is to embrace the desktop and see how much  it enables a 'normal'  day's work. To do this I've got a couple of tasks
  
    1. Install it on the test hardware, in this case a pair of old Macbook Pro's.
    2. Try  and produce a blog article on it. That means  installing PyCharm Community Edition, a ruby build chain (for Jekyll), Gimp (to make  the header graphics)
    3. Because writing takes a  long time, test the media playback abilities (e.g. right now I'm listening  to Beck MellowGold via Bluetooth)
    4. Some light web  browsing and Youtubing because internets. 
    
2. Some hard figures and facts. Lies, damned lies  and benchmarks. This is an interesting one, as because I this is the first time I'm doing this, I don't really have a baseline. Benchmarking software is great at spitting  out numbers  but a  lot of the time they are only valuable  when in comparison to something e.g. the same test run on a different platform. 

# The hardware

There are 4 machines at play:

Two baseline machines:
1. Combretum - probably the best hardware I own. A 2015 Macbook pro, with 16 Gigs of ram and 4th generation Core-I7 processor running at 3.40GHz. It was one of the last models of MacBook Pros before Apple decided USB ports were too 90's, and instead of function keys you needed a mini touch screen on top of your keyboard. 
2. Ouhout - a second machine purchased from BargainHardware.co.uk. It's a also a 4 Generation Core I7 quad core, with 16 Gigs of ram. It has a GeForce 1030 in it, which means that this  machines wins anything that requires any graphics processing.

Two test rigs:
3. A 2011 Macbook  Pro with the dodgy graphics card. When it was new it was sleek and sexy slice of aluminium joy. Now, compared to a modern Mac, it's a wheezing silver gorilla. The graphics card has died and the battery has exploded (slowly). The latter has been replaced and the former has been ignored. With all that said it's still a quad core machine with 8Gigs of ram, which is still above entry level for a 2019 laptop. 
4. Another more ancient Macbook Pro, this time dating from 2008. It's a Core-2 Duo, with 4 Gigs of ram. It overheats in about 4 minutes after startup, and is a stark reminder that back in 2008 we were much happier waiting for software to think about things. Again, this was a beast in it's day. I remember running a windows VM on it and processing photographs using Adobe Lightroom, which is itself not a slouch. It took a long time; there was much coffee drunk and television watched, but I did it.


| Metric | Combretum_Fedora | Mopane_ZorinOS | Ouhout_PopOS |
| ----  | ----  |   ----    | ----   |
| Processor | Intel Core i7-4770HQ @ 3.40GHz (4 Cores / 8 Threads) | Intel Core i7-2635QM @ 2.90GHz (8 Cores) | Intel Core i7-3770 @ 3.90GHz (4 Cores / 8 Threads)
| Motherboard| Apple Mac-06F11FD93F0323C5 (187.0.0.0.0 BIOS) | Apple Mac-94245A3940C91C80 | Dell 0GXM1W (A29 BIOS)
| Chipset | Intel Crystal Well DRAM | Intel 2nd Generation Core Family DRAM | Intel Xeon E3-1200 v2/3rd
| Memory| 16384MB | 8192MB | 16384MB
| Disk | 251GB APPLE SSD SM0256 | 120GB KingDian S280 12 | 120GB KINGSTON SA400S3 + 500GB Hitachi HDS72105 + 240GB KingDian S280 24
| Graphics | Intel Haswell Mobile 2GB (1200MHz) | Intel 2nd Generation Core Family IGP | Gigabyte NVIDIA GeForce GT 1030 2GB (1354/3003MHz)
| Audio | Intel Crystal Well HD Audio | Cirrus Logic CS4206 | Realtek ALC269VB
| Monitor | Color LCD | - | 2 x DELL 2408WFP
| Network | Broadcom BCM43602 802.11ac LAN SoC | Broadcom and subsidiaries NetXtreme BCM57765 Gigabit PCIe + Broadcom and subsidiaries BCM4331 802.11a/b/g/n | Intel 82579LM
| OS | Fedora 305.2.18-200.fc30.x86_64 (x86_64) | Zorin 155.0.0-31-generic (x86_64) | Ubuntu 19.04
| Kernel | 5.0.0-31-generic (x86_64) | 5.0.0-31-generic (x86_64) | 5.0.0-31-generic (x86_64)
| Desktop | GNOME Shell 3.32.2 | GNOME Shell 3.30.2 | GNOME Shell 3.32.2
| Display Server | X Server + Wayland | - | X Server 1.20.4
| OpenGL | 4.5 Mesa 19.1.7 | 3.3 Mesa 19.0.8 | 4.6.0
| File-System | xfs | ext4 | btrfs
| Screen Resolution | 2880x1800 | 1440x900 | 3840x1200



# Anchors up

Having already some some preliminary research into the first distro, I'm excited about this journey. It marks an entry into something I've been meaning to do for a long time (play with different linux distros in a way that is a bit more that simpley distro-hopping), and it marks a return to something that I believe I've lost a bit during the course of this year: writing and more importantly, taking the time to write.

With all that said, let's be off. First up: Zorin OS.


[^1]: And opinions are like arseholes. Everyone has one.

