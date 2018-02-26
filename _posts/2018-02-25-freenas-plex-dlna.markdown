---
layout: post
title: Plex stopped talking DLNA on the FreeNAS
date: 2018-02-25 00:00:00
description: How to get Plex back onto your TV
img: kq-death.jpg # Add image post (optional)
tags: [freenas,dlna,dhcp] # add tag
---

In the whirlwind of happiness that has been my experience with FreeNAS so far, I inadvertently tripped myself up and wrecked the Plex server's ability to advertise itself via DLNA. To fully how this happened I have to rewind a little.

When I first set up the FreeNAS box, I did what I usually do for IP address allocation: set it to use DHCP, let the Virgin Media router assign an IP address of it's choosing, and then later go into the router's web interface and reserve that IP forever for that MAC address. That is the lazy way of doing it because I couldn't be arsed to write down MAC addresses.

This, however, caused problem that resulted in FreeNAS detecting a duplicate IP address on the network (something else had take it's static one). This resulted in heaps of fun, and I had to do the thing that I didn't want to do in the first place: faff around with MAC addresses while searching log files and googling like a crazy person.

Basically every system log and system console was throwing this out at me:


``` arp: fc:8f:90:ac:3e:d9 is using my IP address 192.168.0.99 on bridge0! ```

> TODO: drop a ifconfig contents here

So, what is this? And how is this happening? I know there are jails coming up that make virtual NICs, and I assume the VM's I'd deployed also have virtual NICs that are using some sort of mechanism of piggy-backing off the main network card.

I landed up trying a few things. One of these fixed it, although I'm not sure which it was:
* After some scrutiny I found that I had selected a 'pair' IP Address when had set up my DNSMasq jail. This was the only jail that had this setting, and given that I was not 100% sure exactly what it did, and my GOGs jail, which was working perfectly, I killed the DNSMasq jail and recreated it. That jail was also special because it's the only one with a static IP.

> TODO: get screen shot of bind check boxes

> TODO: find out what that bind means?

* I tried a couple of different things with the config of the FreeNAS NIC (re0), the only actual NIC in the massive list. During one of these wild sessions of check box ticking, I must have checked the IPv6 autoconfiguration button - and set it to off. At the time it would have made sense to me: I don't use IPv6, so let it autoconfigure itself? It's probably going to break something...?

Well it did. For some reason Plex uses the IPv6 stack to broadcast it's presence to available clients.
> TODO: find out why this is?

This blog post is bascially a testament to what a noob I am.
* FreeNAS seems to have good defaults. Just leave them be.
* Find out what settings mean before you select them.
* I know nothing about IPv6.
* I know nothing about DLNA or how to debug it.

So even if it's a list of things I don't know, I feel like I've eaten in that elusive elephant of the 'Unknown unknowns', which is, if nothing, a start.
