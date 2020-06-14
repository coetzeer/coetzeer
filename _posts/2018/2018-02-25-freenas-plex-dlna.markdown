---
layout: post
title: No More DLNA for Plex on FreeNAS
date: 2018-02-25 00:00:00
description: How to get Plex back onto your TV
img: front_broken_tv.jpg # Add image post (optional)
tags: [freenas,dlna,dhcp] # add tag
---

In the whirlwind of happiness that has been my experience with FreeNAS so far, I inadvertently tripped myself up and wrecked the Plex server's ability to advertise itself via DLNA. To fully appreciate how this happened I have to rewind a little.

When I first set up the FreeNAS box, I did what I usually do for IP address allocation: set it to use DHCP, let the Virgin Media router assign an IP address of it's choosing, and then later go into the router's web interface and reserve that IP forever for that MAC address. That is the lazy way of doing it because I couldn't be arsed to write down MAC addresses.

{% include figure image_path="/assets/img/virgin_dns_rule.png" alt="Mac addresses for the lazy." class="image-medium image-centre" caption="Simply select a connected device and PRESTO!" %}

This, however, caused problem that resulted in FreeNAS detecting a duplicate IP address on the network (something else had take it's static one). This resulted in heaps of fun, and I had to do the thing that I didn't want to do in the first place: faff around with MAC addresses while searching log files and googling like a crazy person.

Basically every system log and system console was throwing this out at me:


```
arp: fc:8f:90:ac:3e:d9 is using my IP address 192.168.0.99 on bridge0!
```

And given that the output of ifconfig is this complicated thing below, I didn't really have a good idea of where to start chasing down the bad config (disclaimer the text below is for effect - the problem is solved now).

```
re0: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=82099<RXCSUM,VLAN_MTU,VLAN_HWTAGGING,VLAN_HWCSUM,WOL_MAGIC,LINKSTATE>
	ether fc:aa:14:20:6b:a2
	hwaddr fc:aa:14:20:6b:a2
	inet 192.168.0.9 netmask 0xffffff00 broadcast 192.168.0.255
	inet6 fe80::feaa:14ff:fe20:6ba2%re0 prefixlen 64 scopeid 0x1
	inet6 2a02:8085:e0c0:2a80:feaa:14ff:fe20:6ba2 prefixlen 64 autoconf
	nd6 options=23<PERFORMNUD,ACCEPT_RTADV,AUTO_LINKLOCAL>
	media: Ethernet autoselect (100baseTX <full-duplex>)
	status: active
em0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=209b<RXCSUM,TXCSUM,VLAN_MTU,VLAN_HWTAGGING,VLAN_HWCSUM,WOL_MAGIC>
	ether 00:0e:0c:76:89:ed
	hwaddr 00:0e:0c:76:89:ed
	nd6 options=9<PERFORMNUD,IFDISABLED>
	media: Ethernet autoselect
	status: no carrier
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> metric 0 mtu 16384
	options=600003<RXCSUM,TXCSUM,RXCSUM_IPV6,TXCSUM_IPV6>
	inet6 ::1 prefixlen 128
	inet6 fe80::1%lo0 prefixlen 64 scopeid 0x3
	inet 127.0.0.1 netmask 0xff000000
	nd6 options=21<PERFORMNUD,AUTO_LINKLOCAL>
	groups: lo
bridge0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	ether 02:1f:9c:73:9c:00
	nd6 options=1<PERFORMNUD>
	groups: bridge
	id 00:00:00:00:00:00 priority 32768 hellotime 2 fwddelay 15
	maxage 20 holdcnt 6 proto rstp maxaddr 2000 timeout 1200
	root id 00:00:00:00:00:00 priority 32768 ifcost 0 port 0
	member: epair0a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 5 priority 128 path cost 2000
	member: epair7a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 12 priority 128 path cost 2000
	member: epair9a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 14 priority 128 path cost 2000
	member: epair3a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 8 priority 128 path cost 2000
	member: epair8a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 13 priority 128 path cost 2000
	member: epair6a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 11 priority 128 path cost 2000
	member: epair5a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 10 priority 128 path cost 2000
	member: epair4a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 9 priority 128 path cost 2000
	member: epair2a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 7 priority 128 path cost 2000
	member: epair1a flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 6 priority 128 path cost 2000
	member: re0 flags=143<LEARNING,DISCOVER,AUTOEDGE,AUTOPTP>
	        ifmaxaddr 0 port 1 priority 128 path cost 200000
epair1a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:06:0a
	hwaddr 02:b4:50:00:06:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair
epair2a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:07:0a
	hwaddr 02:b4:50:00:07:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair
epair4a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:09:0a
	hwaddr 02:b4:50:00:09:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair
epair5a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:0a:0a
	hwaddr 02:b4:50:00:0a:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair
epair6a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:0b:0a
	hwaddr 02:b4:50:00:0b:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair
epair8a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:0d:0a
	hwaddr 02:b4:50:00:0d:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair
epair3a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:08:0a
	hwaddr 02:b4:50:00:08:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair
epair9a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:0e:0a
	hwaddr 02:b4:50:00:0e:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair
epair7a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:0c:0a
	hwaddr 02:b4:50:00:0c:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair
epair0a: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=8<VLAN_MTU>
	ether 02:b4:50:00:05:0a
	hwaddr 02:b4:50:00:05:0a
	nd6 options=1<PERFORMNUD>
	media: Ethernet 10Gbase-T (10Gbase-T <full-duplex>)
	status: active
	groups: epair

```

So, what is this? And how is this happening? I know there are jails coming up that make virtual NICs, and I assume the VM's I'd deployed also have virtual NICs that are using some sort of mechanism of piggy-backing off the main network card.

I landed up trying a few things. One of these fixed it, although I'm not sure which it was:

* After some scrutiny I found that I had manually entered a bridge IP Address when had set up my DNSMasq jail. This was the only jail that had this setting, and given that I was not 100% sure exactly what it did, and my GOGs jail, which was working perfectly, I killed the DNSMasq jail and recreated it. That jail was also special because it's the only one with a static IP.

{% include figure image_path="/assets/img/jail_setup.png" alt="Cue jail pun." class="image-small image-centre" caption="Where I messed up: Adding a bridge address." %}

> Bridge addresses: According to the [FreeNAS documentation](http://doc.freenas.org/11/jails.html#id16), you only need to use this setting when you want the jail to be on a different network. Not sure if this was my problem, as I had set the bridge address to the same IP and netmask as the main NIC.

* I tried a couple of different things with the config of the FreeNAS NIC (re0), the only actual NIC in the massive list. During one of these wild sessions of check box ticking, I must have checked the IPv6 autoconfiguration button - and set it to off. At the time it would have made sense to me: I don't use IPv6, so why let it autoconfigure itself? It's probably going to break something...?

{% include figure image_path="/assets/img/ip6_auto_config.png" alt="IPv6. WTF?" class="image-small image-centre" caption="Just let IPv6 do it's own thing." %}

Well it did. For some reason this broke plex's ability to advertise itself.


This blog post is bascially a testament to what a noob I am.
* FreeNAS seems to have good defaults. Just leave them be.
* Find out what settings mean before you select them.
* I know nothing about IPv6.
* I know nothing about DLNA or how to debug it.

So even if it's a list of things I don't know, I feel like I've eaten in that elusive elephant of the 'Unknown unknowns', which is, if nothing, a start.
