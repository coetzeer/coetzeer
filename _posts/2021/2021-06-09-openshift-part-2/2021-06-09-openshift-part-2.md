---
layout: post
title: "Discovering OpenShift: Part 2"
date: 2021-06-10
description: "Discovering OpenShift: Part 2 - the helpful helper node"
img: openshift_part2.jpg 
tags: [openshift, kubernetes,okd] # add tag
toc: true
---

My wife gives me a lift home from the Mater Hospital at about 12. My head is spinning with the things I want to do when I get home. However, I had not taken into account that there would be physical side affects from being blown up like a pasty flesh balloon. One of them, apparently, is that when I walk longer than 100m I get hiccups. These are particularly joyful, because whenever I hiccup, the knife wounds from the small angry doctor stretch uncomfortably. 

At home, I open my laptop, and powerup 2 servers in my homelab. The plan is simple:

- I will make 2 clusters: one using OKD and the latest and greatest Fedora Core OS, and one using OpenShift and the RedHat universal base image. I don't really know how I'll do the latter - I'll have to go through the details of the YouTube video where [Andrew Sullivan installs OpenShift](https://www.youtube.com/watch?v=Be0dRq0wjWE) with static IP's. People who know me realise that this is 'Classic Ray' - where there was 1 thing, there are are now 2 things. Almost the same but different. Twice the work and double chance of failure, but I can't fight the OCD. In this case the OKD OCD. 
- I will use the helper node referenced in the above mentioned video. This should allow me to get away with creating a cluster without the pfSense box described in Craig Robinson's approach. pfSense was only fulfilling the role of a DHCP server, which is something I'm comfortable setting up and manipulating by hand (e.g. with DNSMasq). It looks like the 'helper node' ansible scripts include a DHCP server though, so I'm comfortable in binning the pfSense rig.[^2]
- I want to avoid messing around with spinning up nodes and then harvesting mac addresses, so I'm going to use the trick of rebuilding the Fedora Core OS iso images with all the kernel/bootstrap arguments baked into the ISO. 
- Success will be if I can get access to the console and deploy something like Wordpress + MariaDB container to the cluster.

And then the REAL learning starts.

# The Home-lab

I set about creating the various VM's. The 2 servers I have that will be running this project are both Dell R610's - venerable grandfathers in 'server years' at this point, both them dating back to 2010 or 2011. They both have 96 Gigs of RAM, and both have 2x Xeon L5640 CPU's, giving me a total of 12 hyperthreaded CPU's. Each server should have enough RAM and CPU available to host a complete cluster (although, I realise that putting all your eggs in one basket like this in a real world scenario would be fool hardy and inadvisable). 

One of them is running Proxmox. This will host the OKD cluster. It's got 6x600GB SAS drives, one running the OS and the 5 in a RaidZ1 arrangement giving me about 2.7 GB of useful space

```
~# zpool list
NAME    SIZE  ALLOC   FREE  CKPOINT  EXPANDSZ   FRAG    CAP  DEDUP    HEALTH  ALTROOT
vol01  2.72T   175G  2.55T        -         -     4%     6%  1.00x    ONLINE  -

```

The other server is running VMWare Vsphere 6.7. It also has 6x600GB, but these are glued together into one volume by the onboard Perc Raid Controller. This one will host the OpenShift cluster.

# Solving the 'Separate Network' problem

I make 2 Centos Stream 8 helper nodes - one on each server, and immediately I run into my first problem. I take for granted the networking modes available in programs like VirtualBox. Both Proxmox and VMWare have problems. 

<div class="notebox">
     <b>Virtual Box networking</b>
     
     <p>VirtualBox makes networks for VMs really easy. you get a choice of NAT, Bridged or HostOnly (+ some other variations in later release)</p>
     <ul>
       <li>NAT - a separate network with Internet access through the host. DHCP and masquerading comes built in.</li> 
       <li>Bridged - bridged to the host's network card so the guest looks like it's on the same network as the host. </li>
       <li>HostOnly - completely isolated network with a nic on the host and a nic on each guest.</li>
    </ul>
</div>

For Proxmox, there really isn't much available in terms of networking config. In the configuration dialogues, there's a few places to put in VLAN id's but that's about it. I assume that's because in an enterprise setup, you'll have a couple of nic's in each host, and each one will be assigned a purpose (management, storage, data, etc). VM's assigned to an interface will take advantage of the services on the VLAN e.g. 200 is DEV network, and this network will have it's own DNS, ldap, etc. There is no out of the box way to make an 'isolated' virtual network. A bit of googling shows a few worked examples on the Proxmox wiki [^1]. The example that have also includes ip masquerading and NAT, which will be handy because we need a way for the VM's on this network to reach the internet.


```
auto vmbr0
#private sub network
iface vmbr0 inet static
        address  10.10.10.1
        netmask  255.255.255.0
        bridge-ports none
        bridge-stp off
        bridge-fd 0

        post-up   echo 1 > /proc/sys/net/ipv4/ip_forward
        post-up   iptables -t nat -A POSTROUTING -s '10.10.10.0/24' -o eno1 -j MASQUERADE
        post-down iptables -t nat -D POSTROUTING -s '10.10.10.0/24' -o eno1 -j MASQUERADE
```


This brings me swiftly onto the VMWare problem (which, to be true to the exact timeline, I will only discover in a few days time when setting up the OpenShift cluster). The problem here is that VMWare doesn't have any built in NAT support. You can build up virtual networks and virtual switches, but you don't get anything for free in terms of services. This means another box (for example the pfSense box that was originally helpfully suggested) or the helper node is going to have to act as a network gateway for the OpenShift network and handle all the NATting.

This was a fun return to lab that I had in third year university. I remember enjoying it, but I can't remember much else. Not that remembering exact commands would have been helpful - I think we were running SuSE 6.2 or something. Anyway, a bit of light googling shows how we can enable masquerading and NAT on a Centos 8 (stream) box:

```
/bin/bash
# Assuming that your Linux box has two NICs; ens224 attached to WAN and eth1 attached to LAN
# eth0 = outside = ens224
# eth1 = inside = ens192
# [LAN]----> ens192[GATEWAY]ens224 ---->WAN
# Run the following commands on LINUX box that will act as a firewall or NAT gateway
firewall-cmd --query-interface=ens224
firewall-cmd --query-interface=ens192
firewall-cmd --get-active-zone 
# in my case both nic's where in a zone called public - as this is a lab I didn't waste time moving one to an 'internal' zone
firewall-cmd --zone=public --add-masquerade --permanent 
firewall-cmd --reload 
# ip_forward is activated automatically if masquerading is enabled.
# To verify:
cat /proc/sys/net/ipv4/ip_forward 
# set masquerading to internal zone
firewall-cmd --reload 
firewall-cmd --direct --add-rule ipv4 nat POSTROUTING 0 -o ens224 -j MASQUERADE
firewall-cmd --direct --add-rule ipv4 filter FORWARD 0 -i ens224 -o ens192 -j ACCEPT
firewall-cmd --direct --add-rule ipv4 filter FORWARD 0 -i ens224 -o ens192 -m state --state RELATED,ESTABLISHED -j ACCEPT
firewall-cmd --reload

```

# The Helper node

The 'OCS helper node' has been elevated to official status and is available on github: [https://github.com/RedHatOfficial/ocp4-helpernode](https://github.com/RedHatOfficial/ocp4-helpernode). If you follow the link at the top of the github Readme, it takes you to HelperNode V2, which is a single downloadable executable that helps you set up the configuration, and then renders all the services (DNS, DHCP, webservice for ignition and BIOS files) as podman containers. It seems elegant and easy to use. I couldn't get it to work though.

So I checked out V1 implemented as series of Ansible scripts, and set that up. 

# Bed time

By the time I'd done all that, it was time for bed. I had been putting off the process of making the custom ISO's and now, with a few minutes to go before sleeping became a critical requirement, I couldn't go any further without unpacking, configuring and then re-rolling the ISO's. This happy job will have to wait until tomorrow.


[^1]:  Proxmox networking wiki: [https://pve.proxmox.com/wiki/Network_Configuration](https://pve.proxmox.com/wiki/Network_Configuration)
[^2]:  Mistake number 1: pfSense also does NAT for the private network. 
