---
layout: post
title: "ZorinOS 15"
date: 2019-10-14
description: Some ordered thoughts on ZorinOS 15
img: lxc_fedora.jpg # Add image post (optional)
tags: [distro,ZorinOS,phoronix] # add tag
toc: true
---

# Finally, ZorinOS

ZorinOS is one of many distros that is on my list to review. It bubbled to the top for 2 reasons: firstly, I met one of the creators Artyom Zorin (well, when I say  *met*, I went to a Dublin Linux Community Meetup and he was in the same room), so there is an Ireland connection there which is fun, and secondly, I listened to the excellent interview that the same man, Artyom Zorin, did with the internet's own Jason Evangelho on his podcast [Linux for Everyone](https://linuxforeveryone.fireside.fm/11). It seemed like the universe was trying to tell me something. Also, I needed to have something to talk about if your man ever comes to another Meetup.

# What is Zorin
ZorinOS 15 is user focused distro based on Ubuntu Bionic. Although there is obvious attempt to hide that fact that it's Ubuntu based, it's not obvious. The usual Ubuntu identifiers have been removed in favour of 'ZorinOS' e.g. ansible's setup module returns the following interesting mix of facts:

```yaml
        "ansible_distribution": "Zorin OS", 
        "ansible_distribution_major_version": "15", 
        "ansible_distribution_release": "bionic", 
        "ansible_distribution_version": "15",
        ...
        "ansible_os_family": "Zorin OS"
``` 
The obvious hint there is the distribution release of 'bionic'. 

But this is good news. It means that if you're used to Ubuntu, ZorinOS will feel very comfortable. Once I'd adjusted my various 'when:' blocks into include 'Zorin OS', all my bootstrapping scripts worked out of the box. It going to have access to the whole of the ubuntu software archive, and good compatibility with third party products like Steam. Additionally, Bionic is Ubuntu 18.04, which is an LTS version, which means that the base of Zorin OS is the 'Volvo' of operating systems. It's not exciting and it's not going to have the latest and greatest kernel and hardware support, but it's going to super stable. Given that Zorin OS is positioning itself as an easy to use, beginner friendly distro, this is probably a good choice.

ZorinOS comes in a couple of different flavours. There is an Ultimate version which which will set you back 39 Euros plus tax. There is a Core edition, which appears to be the guts of the Ultimate version but without some of the goodies that they've written for the desktop, such as their different 'Layouts' (A MacOS layout, Windows Layout, etc ). There is also a 'light' version, which looks like it's a couple of versions behind (being 12.4), and an Education edition. Seeing that was very much in the mode of 'dipping a toe in the water', and decided to be commitment-phobe[^1] and go with the Core version.

# Slipping on the Zorin Slippers

The installation completed without a problem. I opted for a vanilla  setup and accepted the 'Use my whole drive' option during the setup. I'm usually tempted to dive down a BTRFS rabbit hole when it comes to selecting a file system, but seeing as I wanted to benchmark this machine, I decided to try keep things as simple as possible.

The first impression of Zorin OS is a very comfortable, familiar looking desktop with a task bar along the bottom, a start menu at the bottom left, and some suspiciously 'system tray-ish' looking icons and a clock at the bottom left. The start menu works as expected; it contains a list of application categories on the left, and some common locations (Documents, Home, Downloads, etc) on the right. The menu is searchable, so   
    








[^1]: I have revised this decision and I have since purchased the Ultimate version. I don't really want to get into a debate about 'paying for Linux'. It's my opinion that the Ultimate version is worth 45 bucks.