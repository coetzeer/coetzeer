---
layout: post
title: So you want to build a nas
date: 2018-02-02 00:00:00
description: The first post of the blog
img: Quest_for_Glory_So_You_Want_to_Be_a_Hero_-_Gameplay_Commentary.jpg # Add image post (optional)
tags: [nas] # add tag
---

Well, yes. I'd like to build a NAS. And like the intrepid hero entering the small and slightly frightening village of Spielburg in the 1989 Sierra game 'So you want to be a hero?', I am wondering around slightly aimlessly, talking to strangers, eating questionable fruit, and occasionally being killed by monsters (that list bit is an exaggeration - in real life one takes out insurance against monsters and hopes they never happen).

{% include figure image_path="/assets/img/entroware.png" alt="Entroware - feel the awesomeness" class="image-small image-right" caption="This is a figure caption." %}


But I am, right now genuinely lost. I have a lot of cheap kit. It does the job, but it's just riddled with disappointment. Every project I start gets bogged down in slowness or space constraints. So I need better kit.

I am an IT professional that works in a place that has pretty fancy kit. That kind of hardware is complete overkill for a home server, but it does somewhat lead to an inferiority complex when I look at my collection of wheezing hard drives. I can't afford enterprise grade stuff, nor would I like to actually run enterprise grade stuff given that it sucks power and generally is accompanied by loud fans and complicated rack requirements (and a staggering low WAF) .

So truth is somewhere in the middle. Somewhere like the Entroware ES01 or the mighty FreeNAS Mini. There are, of course other options; a veritable smorgasbord of QNAPs, Synologies, refurbished DELLs and HPs, Dlinks, Buffaloes - the list really goes on and on. But those are inevitably freedom hating. And one of my requirements for this project is to minimize the freedom hate as much as possible.

Speaking of requirements, as I am software developer at heart, I think the time has come to list a few:

* To be freedom loving. Yes, a QNAP probably runs a version of Linux down in the deep dark belly of it's firmware, but that's not good enough for me. I want a GPL2, preferably GPL3 product. I want something that I wouldn't be ashamed to show to RMS himself.

{% include figure image_path="/assets/img/rms.jpg" alt="Facebook is not your friend, it is a surveillance engine. Richard Stallman" class="image-small image-right" caption="RMS. The man. The myth." %}

* I want to run FreeNas. Yes, that is very specific and about as arbitrary as 'I don't want to run QNAP'. What can I say. It's my NAS.

* No seriously, I want to run FreeNAS. I ran FreeNAS for a couple of years back in the day on a AMD 700 with a couple of hard drives jammed into the chassis. It was super. It was the most reliable and fasted home storage I've ever had. It just worked. I also have the somewhat guilty pleasure of listening to the excellent podcast BSDNow presented by Alan Jude and Benedict Reuschling, and I REALLY REALLY want to dip my toes into some BSD goodness.

* That brings us nicely onto: I would like to get some hands on experience with BSD, ZFS and Jails. I feel I might have programmed byself into this situation by listening to the dulcet tones of Alan and Benedict, but it's really easy to make the case that an extra platform and storage option in your IT toolkit is always a good thing. Netflix uses BSD, a lot of the world's serious storage vendors use ZFS, and jails are kind of like 'proper docker'. Bring it on.

{% include figure image_path="/assets/img/freenas.jpg" alt="Freenas is a shark. Watch your fingers." class="image-small image-right" caption="Freenas: cue the jaws music." %}

* I want to run a few services in my house. I generally change my mind about these a lot, so it would help if these services could run on a virtualization platform. Now, I hear the sharp intake of breath form the ZFS experts - I know ZFS will want to eat all the RAM and CPU available on the NAS that is ultimately created. But, whatever spec the box turns out to be,  my home network will probably not be fast enough to deal with a saturated 100Mbit nic, let alone 1000 or something aggregated. So I feel like I will have CPU and memory to spare. Types of things I want to run:


  * A git server. Maybe more than one. See previous reference to changing my mind a lot. Gogs is lovely and simple, and a great candidate for a jail, if I can get a Go runtime onto it. But there is also Gitlab, which is feature packed, and comes with a built in CI option. Why not use Github, you foolish foolish man, I hear you say? Well, I want a safe place for the things that I don't want to put on the internet. What are those things? Well, I'm not going to tell you,

  * A CI server. Maybe. Mostly for playing. Because I'm software nerd. Nothing serious.
  * An Ubuntu Landscape server to manage my fleet of aging Apple Mac book pros running Ubuntu. Overkill, yes. Maybe. Will I get bored? Probably.

{% include figure image_path="/assets/img/freeipa.jpg" alt="Free IPA" class="image-small image-right" caption="FreeIPA: not as in beer." %}

  * Fedora VMs for running IPA and Satellite labs (or whatever the free versions of the those are - Free IPA and Spacewalk). Why? Well, work is a RedHat shop, and most of the juicy bits of RHEV management tools are out of my purview. So it's nice to have the facility to spin these tools up, break them and then spin them up again.

{% include figure image_path="/assets/img/spacewalk.png" alt="Spacewalk." class="image-small image-right" caption="Spacewalk. Not 100% sure what it does." %}

  * These will hopefully also manage authentication on and cache packages for the other weird thing I like to do: install every flavour of Korora Linux the moment it comes out. Also I do lots of work on Centos for work POC's, so having any kind of help speeding up template building is great for me.

* Storage. Yes, funnily I actually want it for storage. I have vast and ever growing storage requirements. I have a fleet of laptops that require constant backups. I have a growing collection of Raspberry Pi images full of wonderful things to discover. I have terrabytes of photographs that need safe, resilient, fast storage. I have the world's largest collection of music and audiobooks, that needs constant grooming. I have 20 projects in various states of disrepair. And I'm afraid to delete any of it.


* Storage. Safe Storage. Currently I have 13TB of disk, in various states of disrepair, scattered over 4 NAS machines, varying speeds, varying age and varying reliability. There is no redundancy and there has been some disappointing but not catastrophic data loss. As someone who is supposed to 'know about this stuff', it's a little embarrassing to not really have a good back up solution. It doesn't do anything for my IT cred when I have to explain to people that there is no RAID, no redundancy. I can feel the judgement.

So, simple enough. Lots of safe, freedom loving storage. How hard could it be?
