---
layout: post
title: "Where has 2019 gone?"
date: 2019-05-09
description: The year has zoomed by.
img: clock.jpg # Add image post (optional)
tags: [catchup,btrfs,opinion] # add tag
toc: true
---

One of the side effects of getting older seems to be that time goes by much faster. I like to blame social networking, computers and the break neck speed of modern life that makes it feel like weeks and months just zoom by, but in the end, I think it's just life: having a job and having responsibilities. If you're not careful, life becomes a series of milestones that you have no control over.

So where I have been since February when I was happily hacking around on a squid server and dipping my toes into Ubuntu Linux:

# Discovering BTRFS

I'm a big ZFS fan, so imagine my surprise when I installed Linux and found that by default you get the very stable but very boring ext4 filesystem installed on the boot and root partitions. This is all every well and good, but it wasn't long before I started hankering after things like __snapshots__ and atomic, volume level backups. 

{% include figure image_path="/assets/img/zfslinux.png" alt="Where all the magic happens" class="image-small image-right" caption="ZFS on Linux. It's safe, you can come out now." %}

I started looking into ZFS on Ubuntu, and there are some hopeful signs. Roughly from 18.04 Ubuntu has distributed ZFS as part of the operating system, and since then the ZFS on Linux (ZoL) project has become the defacto place where ZFS development converges: even FreeBSD [^1] and Solaris-variants of Unix will use this ZoL, so all in all ZFS on Ubuntu sounds like a real solid option.

However, it's a still a bit premature for native support and integration options e.g. zfs doesn't come up as option for the root filesystem in the installer. That means that you have to carve up your filesystem into ZFS partitions after you install it on something else. I'm not comfortable doing that.

That leaves the much maligned BTRFS: the grumpy teenager of the 'File system family'. It's there in the drop down when you install any flavour of ubuntu, and after doing a bit of reading, it seemed like it would scratch my 'snapshot and send' itch, so I took the plunge. It's been a steep learning curve, but as with most things that are worth while, the work has been worth it.

To do my BTRFS journey the justice it deserves I'll be unpacking some of the tings I've learned in a series of articles. Suffice to say: I think BTRFS gets a bad rap. It's hard work, and it has a name that I don't think helps it's cause, but it's miles better than anything else that's not ZFS. 


# Audio on linux

A friend of mine starting producing audio content for work, which started him on a rabbit hole of home-studio style audio production. That, as it turns out, is predominantly the world of podcasters and youtubers, and it's hard to not get pulled into the orbit of 'content creators'. A couple of interesting lunch time conversations later, I was the proud owner of a Behringer UMC404HD, and a few cheap condenser mics. 

{% include figure image_path="/assets/img/behringer404.png" alt="A very optimistic 4 inputs" class="image-large image-centre" caption="Behringer UMC404HD - online content creators watchout." %}


And that was only the beginning. After some messing about with Audacity, I did some googling and discovered JACK. And that was the end of many evenings for me.

Again, JACK is much maligned and deserves much more than paragraph in a summary blog post, so I'll do a deep dive at a later stage. If I get it right, it might even be in an audio format!


# A brief sojourn into networking


{% include figure image_path="/assets/img/angrycat.jpg" alt="The Angry cat does not trust the IOT devices" class="image-small image-right" caption="You expect me to trust THAT?" %}

I have recently gotten a bee in my bonnet about all the things you get these days that 'connect to wifi'. The worst offenders are things like TVs, mostly because they aren't new: people have had TV's in their houses for decades now, and in this internet connected age, the ol' goggle box is not internet connected, in your living room and sending who knows what back to the mothership.

I would like very much to bung a firewall appliance in between these devices (TVs, IP cams, wireless plugs, etc). And that desire has sent me down a rabbit hole of firewall appliance distributions (pfsense, opensense, smoothwall, monowall, etc). It also sent me into analysis and planning mode with respect to overhauling and upgrading my home network. And after many agonizing evenings looking at hardware specs and drawing pictures, this project was put into the freezer. The biggest problem is that this was heading to the realms of being quite expensive: a good wireless AP, some hardware for the firewall, a good switch that supports VLAN's, a good couple of meters of cat6 - all of sudden I was well beyond the WAF, especially for a project based on vague feeling of mistrust rather that a definite increase in capabilities. 

# Looking forward

Before I sat down to write this catchup piece, I made a list of all the things in my head that I'd like to get out. There is a lot. Now that I've caught up on the last couple of months, we can do a deep dive into meat and potatoes, and the nitty gritty, the good stuff.

[^1]: https://lists.freebsd.org/pipermail/freebsd-current/2018-December/072422.html