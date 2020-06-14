---
layout: post
title: Wall of NAS
date: 2018-02-05 00:00:00
description: All about the big collection of NAS's on my wall.
img: kq-wall.jpg # Add image post (optional)
tags: [nas] # add tag
---

{% include figure image_path="/assets/img/wall_of_nas.jpg" alt="Nas's as far as the eye can see" class="image-small image-right" caption="The great wall of NAS" %}

So, with the weekend over, and I have to stop messing about with Jails and VMs and ZFS, and think about my day job. One thing I can do in spare time like bus journeys and that pre-bedtime catatonia that strikes anyone who is paid to look at computer monitors all day long, is to document the NAS setup that runs my houses my current data requirements. These are all mounted up on a Skadis to keep them up and out of the way and ventilated.


Up on the Skadis, there's 4 NAS modules:


* 2010 2TB Apple time capsule
* QNAP T20 with two 1.5TB drives
* 2x DNS 320 cases each with 2 2TB drives

That gives me a total of 13TB of disk space.

# A note about the Skadis

For a long time I was looking for a storage solution for game consoles and related computer gear that:

* Was easy to put up
* Was easy to put near a TV
* Easy to hide or obfuscate
* Had good ventilation
* Didn't requiring massive and complicate wiring exercises e.g. sending wires through dry wall.

What caught my eye was these wall mounted units for game consoles - Floating Grip. They looked fab, but were hilariously expensive for what is essentially a piece of string and a nail.

{% include figure image_path="/assets/img/hanging_thing.jpg" alt="Pretty." class="image-small image-right" caption="The Float Grip" %}

Also, I had a lot of other components (raspberry pi's, other game consoles, NAS's, etc) that I wanted to get up on the wall and that means a multiplug, network switches and a whole slew of cables. So I needed something larger, more financially salable, and preferably something that I didn't have to buy online from the other end of the world.


_Why not a shelf?_ Well, shelves stick out into a room i.e. you can't put it behind a door or in a room that already has a lot of furniture in it. Also, you can't stack this hardware and you'd be forced to do that with a traditional shelf. You can't put a Dlink NAS on a Xbox - both will probably melt. So shelves have limited usefulness. However, I do have lots of wall space that can conveniently be hidden from view simply by opening a door.


Enter the IKEA Skadis - essentially a peg board with some handy attachments. It goes up with 2 screws, is plastic and white and is as clinical as a dentist's waiting room chair. You can see for yourself how you have quite a lot of flexibility with the different shelf options, wee baggies, elasticated harnesses and plastic cups. IKEA also has cable management trunking, which combined with liberal use of cable ties makes the cable madness much easier to tame.


Skadis - you can't go wrong. But back to the NAS's.

# Why they suck a little bit

It seems bananas to spend a lot of time and effort to throw together a new NAS if you have all of this storage floating around already. And you're right. It's a first world problem. The problem is that this setup is just a bit disappointing in almost every way. Mostly the basics are well catered before, but anything beyond basic file sharing, and it's all sad face emojis. Let's start with the oldest one.
The Apple Time Capsule

This has been a reliable little bit of kit. It's been running almost non stop since 2010 - so nearly 8

years now. It's been setup mainly as a wifi extender on the router provided by our broadband company. But it needs to replaced, and here's why:

{% include figure image_path="/assets/img/timecap.jpg" alt="Slightly yellowing" class="image-small image-right" caption="The Apple Time Capsule" %}

* it's getting on now. Almost 8 years of non stop running is starting to make me nervous. I know it's Apple and everything, but I've never cracked the box open to see what sort of drive is running inside the white ( now slightly yellow), slime, Apple branded enclosure. My concern is that the drive could go at any day. And that would be tear inducing.
* it's frustratingly unhackable. I've bought 10 Euro TPLink routers that are more fun that this box. It does 1 Windows file share and 1 AFP share. It runs whatever it needs to run for Time Machine, which is not that arduous on the server side from what I understand. There is no way to see the traffic throughput. There is no way to read a system log. There is no way to debug config issues. There is no way to extend it in any way. It does what it does very well, and it's a snorefest.

On the plus side, it has a switch built in, it's reliable, it's kind of slick lookin and does the wifi network extending.

# The QNAP

This is a second hand model that I picked up from friend. Like the time capsule, it does what it does pretty well. It is less of a snorefest than the time capsule, but it's not as pretty. It has some nice out of the box features: a web server, configurable shares, basic plugin infrastructure. All in all a nice solid little box. But:

{% include figure image_path="/assets/img/qnap.jpg" alt="The land rover of NAS's" class="image-small image-right" caption="The QNAP" %}

* It's also getting on in years.
* The plugins are limited and the plugin community has essentially dried up as the CPU architecture for this model is no longer supported by QNAP. So you can have old version of mysql and wordpress, but that's about it.
* The plugins are a bit obscure. I'm not 100% sure where they come from and who made them.
* The box is under powered. When you run more than two transfers, the CPU get maxed out.
* I've never been able to get the multimedia capabilities of this box to work well. The video and audio that it serves up has to be 100% the right format or it just won't work. It also grinds to a halt if you try and server anything bigger than 480p. Built in DLNA server, Twonky Media, is easy to set up and get going, but it doesn't do any realtime conversions, so any thing that not an MP3 simply doesn't play.
* The fan is noisy. I've tried a couple of different fans for this NAS, but the only way to make it quiet to switch the fan off completely.

# The DLink 320

The pros and cons of the DLink 320's are just about the same as the QNAP except that they're even more under powered and they don't really support any plugins or extensions at all. Now, this is not the end of the world because they come pretty feature packed, and most people should be very happy with the services provided by these boxes. But:

{% include figure image_path="/assets/img/dlinks.jpg" alt="Nice pair" class="image-small image-right" caption="The two dlinks" %}

* They's so underpowered that almost any other process running on the box grinds a transfer to a halt e.g. if you try to do an rsync from one box to another, don't even bother trying to connect with AFP
* The user setup for the different sharing mechanisms is broken. There are a couple of users built in, in additional to the users that you create when you set up the unit. The problem is that when you rsync, files get created by a different user. * The file masks for the different protocols are also different. That means you can copy a file in place with rsync, and not be able to rename it or move with CIFS. Or you can add a whole bunch of new files with CIFS and not be able to read then with AFP.  

Those two little anti-features together just make for a confidence sapping experience.


So, for any casual reader, including my wife, I hope I've made the case for the ongoing upgrade activities.
