---
layout: post
title: "Discovering OpenShift: Part 1"
date: 2021-06-08
description: "Discovering OpenShift: Part 1 - the excitement builds"
img: openshift_part1.jpg 
tags: [openshift, kubernetes,okd] # add tag
toc: true
---

I'm lying in hospital. In the last few hours I've been blown up like a balloon and then stabbed five times by what I assume to be a small, angry doctor. Allegedly they took something out too. But the point is I'm lying in a 6 bed ward at night, having spent most of the day snoring my way through the remains of the anesthetic, and now, at 11pm, I'm wide awake. I don't have a laptop, so I'm left with the not inconsiderable entertainment options available on my phone. I crack open YouTube, and ignore all the suggestions, just out of principle. I figure now is a good time to go and review my 'Watch later' playlist, seeing as how for the first time since I started work in 2004, I have some time to myself. 

{% include figure image_path="/assets/img/farside.jpg" class="image-medium image-right" caption="What I imagine happened after I fell asleep" %}

There, in amongst feature length documentaries on retro gaming, [Die Antwoord](https://www.youtube.com/watch?v=L-wpS49KN00&list=WL&index=59) music videos that I've not been brave enough to watch on my own, and enough photography tutorials to kick start my next career, I see an old nemesis: [Building an OKD 4 Home Lab with special guest Craig Robinson](https://www.youtube.com/watch?v=qh1zYW7BLxE).  So I decide, now is a good time to see what I did wrong, the last time I tried to set up OKD.

I stumbled on the original article [^1] written by Craig Robinson about a year ago. I found it easy to follow, and expressed in terms of primitives that I understand: VMWare homelab, pfSense, centos, fedora, etc. For anyone interested in getting started with OKD (the open source upstream of RedHat's commercial product OpenShift), it's definitely worth a read. Other sources of knowledge worth keeping an eye on are the rest of his blog articles[^4] and the official OKD documentation[^3] on installing OKD on baremetal. I still have the VMs set up, so I checked the date on them: April 29th 2020. 

My first attempt last year failed miserably. I can't tell you why - learning how to introspect and diagnose an OKD cluster is a skill I had not yet learning. I think I got as far as firing up the bootstrap and master nodes, and they didn't come up nicely, and at some point I just gave up. I figured the error must have been in typing that was required for the bootstrap config for the Fedora Core OS machines - I don't really trust it if I can't copy and paste.

To be absolutely clear, this is a good video. I know my tone is slightly tongue in cheek and I am spending a lot of time describing (my) failure, but it's worth pointing out that producing the article, keeping it up to date and then streaming a video about it on the OpenShift channel is a fair amount of effort and all greatly appreciated. 

Anyway, I watched the video with Craig and [Chris Short](https://chrisshort.net/) and a couple of things clicked for me. Then I watched another one that made a whole lot of other things click: [Red Hat OpenShift on VMware vSphere](https://www.youtube.com/watch?v=Be0dRq0wjWE) with Andrew Sullivan. My aha moments where (in no particular order):
- The difference between OpenShift and OKD is less that I thought. The download links are different and they're called different things, but the process of getting bootstrapped with them appears to be the same. 
- Another difference between the two videos was that OKD was paired with Fedora Core OS and OpenShift was paired with the Red Hat Universal Base Image. For some reason I'd never really thought of these two things as being related or equivalent before. But for the purposes of this exercise they're an immutable os image that you bootstrap with some containers, so ultimately should behave similarly. 
- Craig uses a 'services' machine and pfSense to wire up services required to support his cluster (NFS and bind and a convenient place to install okd and the okd installer for the 'services machine' and NAT and DHCP in the PFSense box.) The OpenShift video used the 'OpenShift helper node' [^2] which effectively rolls all required supporting services into 1 (with the exception of NAT, which I discovered later). It adds quite a lot of extra too, most importantly for me was the ability to support PXE booting the OCS nodes (bootstrap, masterX and workerX). Seeing this in action was probably the biggest AHA moment for me.
- Andrew also showed a way to avoid the required typing in the Grub menu, which was what I believed to be downfall previously. He rebuilt the Red Hat Universal Base image isos with the right grub boot options for each particular use case - down to the ip address assignments for each of the nodes. 

This made me excited to try my nemesis project again. For no real reason other than to clear the stench of failure out of my home lab setup. I tried to go to sleep with 'mkisofs' command lines buzzing in my head and dozed off at about 3am. 

At 4am someone else in the ward switched the TV on full blast.

At 5:45, the two old guys next to me discovered that they were both Taxi drivers and spent the rest of the morning comparing life experiences. I felt like I was in a Roddy Doyle play. 


[^1]: Craig Robinson's guide to installing OKD on a VMWare homelab - [https://itnext.io/guide-installing-an-okd-4-5-cluster-508a2631cbee](https://itnext.io/guide-installing-an-okd-4-5-cluster-508a2631cbee)
[^2]: OCS helper node - [https://github.com/RedHatOfficial/ocp4-helpernode](https://github.com/RedHatOfficial/ocp4-helpernode)
[^3]: Official OKD documentation - [https://docs.okd.io/latest/installing/installing_bare_metal/installing-bare-metal.html](https://docs.okd.io/latest/installing/installing_bare_metal/installing-bare-metal.html)
[^4]: Medium posts authored by Craig Robinson - [https://itnext.io/guide-installing-an-okd-4-5-cluster-508a2631cbee](https://itnext.io/guide-installing-an-okd-4-5-cluster-508a2631cbee)