---
layout: post
title: "Penguins everywhere: getting cozy with Linux"
date: 2019-10-16
description: Installing the cloud that I probably won't use
img: penguins.jpg.jpg # Add image post (optional)
tags: [linux, Ubuntu, Mate, Audio] # add tag
toc: true
---

We're a month into the new year, and the Christmas break feel like a memory from my dim and distant past. There was a Turkey. There was a lovely day with my friends. There was even a night out at my local pub. But all that is behind me now. Boo. 

#Getting to grips with Linux Audio

It's been a long standing desire of mine to put together a Linux or FreeBSD desktop. I talk a lot of big talk about being a FOSS supporter, but I've made the pragmatic choice in the past to use Windows so I could play games and use the Adobe products that I pay money for each month. There's also a piece of me that's scared to let go of something that I know is stable - or at least boring enough that it never find it compelling enough to mess with. My thought process was: I work with computers (linux servers) all day long, the last thing I want to do when I get home is peer into 'top' output or 'grep' something. So due to this faulty logic, I've remained shackled to windows for what feel like a very long time.

So what changed? Well, a few things. 

During the course of 2017, my wife's MacBook Pro died. It is a 2011 model that is know to have a AMD Radeon discreet graphics card that is known to become faulty as the device get's a bit older. I was able to resurrect OSX but I had to perform a VERY strange and arcane set of steps that allows you to remove the Radeon drivers, and this force the laptop and OS to boot with the embedded Intel graphics. This is MUCH harder that the previous sentence would lead you to believe. It took me 2 days and involved an Arch Linux rescue USB stick followed by more restarts that a single person's sanity would allow. AND then when you get updates you have to do the same thing all over again.

It was easier to just install Linux on it: so for a about a year it ran stock Ubuntu 17.10. At some point last ear I swapped over to KDE Neon, and never looked back. So one thing that changed is that I got a second computer to run Linux on.

The second thing that changed was that I threw my toys out of the cot at work regarding remote access on my own hardware. I had a super uncomfortable production update in March last year where the MacBookPro I was using would get progressively slower and slower for the Citrix session that was giving me a window into our office systems. I spent ages running diagnostics and google my little heart out for a reason as to why Citrix was just turning into a dog. So once the snow cleared and I got back to work, I requested some work hardware