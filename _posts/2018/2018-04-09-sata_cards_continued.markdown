---
layout: post
title: Some SATA success
date: 2018-04-09
description: Some SATA success
img: happy_picard.jpg # Add image post (optional)
tags: [nas,satacard] # add tag
toc: true
---

The depression of sata-gate has taken about a week to dissipate. I couldn't bare the thought of opening up the node-304 box again, once again to fiddle around with sata cables and to peer into the bios wondering where the drives had disappeared to. But my hand was forced. For some reason FreeNAS started slowing down; I couldn't find any obvious culprits via the awesome interface that is netdata, so I blamed the network connection of where I had the box located.

I had temporarily put it close to the drives that was copying data off. This worked ok when the rsyncing was happing, but that netplug is showing a fault, so I reckon there's some slowdown or latency on that segment of my hokey home network.

So I needed to shut it down to move it to it's ultimate home.

Having lived through the debacle of the SiL3114 and it's penchent for fucking with my drive's MBR's, I took the bull by the horns and replace the evil spawn of satan card with the Sodial card (VIA chipset - a whole £5).

And it worked.

I got a shock when it the BIOS didn't show up the two drives connected to the card, but had read from the Amazon review that you couldn't boot from drives connected to the card, so I let the startup proceed.

And nothing. Blissful nothing. It just worked.

The next test will be to see if it lasts longer than the week.

And if it doesn't the Promise card arrived today, so I'll have a spare ready to go.
