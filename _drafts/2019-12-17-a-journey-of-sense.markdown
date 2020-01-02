---
layout: post
title: "A journey of sense"
date: 2019-12-16
description: Starting a journey of distro reviews
img: pan_of_reviewers.jpg # Add image post (optional)
tags: [pfsense,opnsense,xenserver] # add tag
toc: true
---

# (OPN)Sense and (PF)Sens-ability

Once you've recovered from the shock of encountering the awkward diptych of puns in the heading, you'll realise that this has nothing to do with Jane Austin and everything to do with a dive into the rabbit hole that is open source firewalls. And, like all the best things in life they come in pairs: pfSense and OPNSense.

The story of pfSense and OPNSense is a common in the open source community. In the beginning there was pfSense which was the community standard in home/small office firewalls. Then came a company, and things got serious. The folks at the company decided to only support processors that have AES-NI, which is a cryptographic instruction set. Folks got mad: this made a common use case of pfSense, taking that old machine that's been a cupboard years and putting it to use as firewall, completely unviable. There was a mighty forking, and so OPNSense was born.

So which is better: the venerable pfSense with it's commercial backing and years of experience? Or OPNSense, the community lovechild. Either way, having two of them made me immensely happy.

<div class="notebox">
     <b>A small sidebar on pairs</b>

 <p>
Pairs of things are wonderful. I'd forgotten this about myself but I have this insatiable desire to have a pair of things, whatever they are. When I was young it was pair of lego aeroplanes, or pair of books about dolphins. The best dolphins were the ones that were two species that were VERY similar, like the Southern Wright Dolphin and the Northern Wright Dolphin. As a teenager growing up in the 90's, I had 2 posters on my wall: Cindy Crawford and Claudia Schaffer. As a person approximating an adult now, I have two Mamiya RZ76's (a Pro and a Pro II) and I walk around with two phones in my pocket (a personal phone and work phone). 
</p>
<p>
There are some obvious limitations to the 'two of everything' desire. It's just not financially possible to have two houses, it's not practical to have two cars and I have this overwhelming feeling that the discussion around doubling up the number of wives would bring my spouse count from 1 down to 0.
</p>
<p>
This inclination is also problematic in that it's a natural complexity multiplier for almost everything that I take on in a personal capacity. And as a corollary, the projects in which I've had some success have always involved picking one 'thing', whether it was a software project, a goal (or even a wife :).
</p>
<p>
And so it was with this project: after an awful lot of churning my goal to have like for like installation of pfSense and OPNSense at the same time always resulted in some sort of failure. This blog documents the journey of my folly.
</p>
</div>


# In the beginning

In the beginning there was just an plane old internet router. It was the standard one supplied by Virgin Media; an upgright white box, that provided the internet connection tinged with the disappointment that is IP6. All attempts at understanding IP6 had failed, and all attempts to circumvent it with VPN connections and elaborate firewall rules were moderately successful, but always the mild smell of IP6 ordure lingered.

There is a way to get around this, and it's quite simple: in Ireland there's a website called Boards.ie, and the Virgin customer relationship reps hang out on there. It's as simple as dropping one of them a private message, and followup to confirm an account number. I'd always resisted because I didn't want to introduce complexity that ran the risk of breaking our home internet connection without some sort of tangible advantage (i.e. one that I could explain and demonstrate to my wife).

Then came various things that eventually pushed me over the edge:
1. The wifi signal coming out of the magical white box was getting weaker and weaker. I was going to have to look into some sort of after market WIFI solution. 
2. I had a discussion with a friend about 'things you buy off the ebay and then connect to your home network'. I realised that I had unwittingly fallen into a trap of compromising our home with cameras, doorbells, TV's and radios that were all internet connected and I had no idea what they were doing. And worse - they were sitting on my home network where the NAS that I had lovelingly crafted and then populated with our family data was living (albeit password protected). 
3. A friend fo mine from the Dublin Linux Community showed me the setup he had with Virgin, IP4 and pfSense. He was able to connect into his home network, look at his PFSense GUI, look at his home assistant information, etc. Best of all he was able to isolate certain devices into a separate network, keeping his servers and personal kit relatively protected from the internet connected thermometers and cameras that make up his home automation system. 

## The plan

The plan, such as it was, was to implement this setup in a series of reversable stages, where each stage had more cost than the stage before. That way, I could create milestones that could provide benefit, and ordering them by cost mean that the I could keep myself busy while keeping an eye out for hardware.

So in a rough order:
- Install pfSense and OPNSense somewhere and start learning their in's and outs.
- Get Virgin Media to switch me over to IP4.
- Get at least one wireless AP to replace the failing Virgin Media wifi.
- Once those three things are in place, I can get a FW in place by putting the Virgin Box into 'Modem Mode', and creating WAN, LAN and WIRELESS networks in the Firewall
- Complexity to follow:
  - Isolating IOT things onto a separate network with lock down rules
  - Expand WIFI for better coverage in the house
  - Other fun networking things like messing about with VLANs?
  - Extend wired solution to a place in the house that is friendly to noisy servers and messy wires.


# Where to run *Sense?   
So my immediate problem was deciding where to host and test a new firewall solution. There some nice OOTB (Out of the box) options that cost anywhere between €100 to €2000, and while I was sorely tempted, €100 is still too much money for something that could turn out to be a massive flop a this point. 
Enter the HP Compaq Pro 6300 (TODO INSERT PICTURE), a stalwart of commercial PC deployments for the last 15 years. The one I purchased from bargainhardware.come came with a quad core AMD Phenom and 8 Gigs of ram. It sported one PCI slot and 4 PCIe4 slots - plenty to extend into a multi-port firewall. One massive hitch: I could not install any *BSD based system on to it. The BIOS would simply refuse to boot any USB or CD that had FreeBSD, FreeNAS, OPNSense or PFSense installed. Linux booted fine, but I was shit out of luck for my firewall booting plans.
I struck on the bright idea of running pfSense and/or OPNSense in a VM (virtual machine). It made infinite sense: the specs for a a home network deployment of pfsense were well withing the range of the HP, and would then be able to run something else, such as a Ubiquity Unifi controller in another little VM on the same kit. Score. 
## Enter Bionic Beaver

So I rammed Ubuntu 18.04 server onto the HP, and it installed like a dream. Qemu and KVM installed just fine, and I was able to get remote Virtmanager sessions to the headless hypervisor installation in no time. So far so good. OPNSense and pfSense install just fine. And that's where I left it for a little while as life got in the way.
When I got back to my 'evaluation', I setup another two little VM's on a private network, and I was able to get a NATed network connection with OPNSense with no problem. pfSense however refused to budge. Many hours of firewall state checking, debugging, packet captureing and general frustration followed. I eventually gave up, thinking that it was something to do with the network setup (I had experimented with both macvtap and bridged options) in the hypervisor, and without seriously rolling my sleeves up a I wasn't going to make any headway.

```
Interweb

+
|    +----------------+        +---------------+
|    |                |        |               |
|    |  pfSense       |  NAT   | pfSense       |
|    |                |        | Client        |
+----+                +<-------+               |
|    |                |        |               |
|    |                |        |               |
|    +----------------+        +---------------+
|
|
|    +----------------+        +---------------+
|    |                |        |               |
|    |  OPNSense      |  NAT   | OPNSense      |
|    |                |        | client        |
+----+                +<-------+               |
     |                |        |               |
     |                |        |               |
     +----------------+        +---------------+

```

## Xen and XCP-NG
Iteration 2 was Citrix Xen server, which installed without a hitch. This product however came with several rabbit holes: firstly getting a 'private network' between a firewall and a client was impossible without installing their [vSwitch Controller](https://docs.citrix.com/en-us/citrix-hypervisor/vswitch-controller.html), which is another VM running on the same hypervisor, adding load and complexity to my supposedly simple firewall installation. The second rabbit hole was a gui: the XenCenter manager application only runs in windows. There is a linux compatible client based written in python called [OpenXenMAnager](https://github.com/OpenXenManager/openxenmanager), but it relies on a library called 'gtkvnc', which appears to have fallen off the radar [after 18.04](https://packages.ubuntu.com/search?keywords=python-gtk-vnc) - and I was using POP!_os 19.10. A last resort was to use ~~windows~~ [Xen-Orchestra](https://xen-orchestra.com/#!/xo-home), which either you run as a separate appliance on my poor little HP Compaq, or you set it up in a container with the docker-compose snippet on [this docker hub page](https://hub.docker.com/r/ezka77/xen-orchestra-ce).

Once I was able to interact with it, pfSense installed like a dream. The NAT worked OOTB. I was flabbergasted. OPNSense, however, just refused to boot up at all. No error message - just some incomprehensible shite in the hypervisor log, and then it died.
I tried a lot of things:
- The error log specifically called out the cpu, so I tried different combinations of CPU configs: 1, 2, 1 socket two core, etc. Didn't help.
- I installed XCP-ng which is an open source version of XenServer, and tried again. No Joy.
- It seemed to die when it was trying to mount the root file system on the cd, so I thought it might have been something to do with the ISO being on an NFS share. So, I made a local ISO SR (storage) on a usb stick and tried with that, no joy.
- I searched the OPNSense forums and spent hours adding in different combinations of kernel parameters. No joy.

# Letting go on the 2

So now I'm in a position that I find myself in fairly frequently: I have one out of the two working, and considering the hoops I had to jump through just to get either one of them to boot, this is not a bad outcome. PFSense seems to be supported on Xen and even comes with some [tuning advice](https://xcp-ng.org/blog/2019/08/20/how-to-install-pfsense-in-a-vm/).

But living with the status quo means leaving OPNSense behind, and after all the fiddling I've done, I feel super invested in it. 

Bah. 

What will he do? Not even he knows folks. We'll have to wait and see.
