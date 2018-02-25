---
layout: post
title: So many rabbit holes. So little time.
date: 2018-02-04 00:00:00
description: The first post of the blog
img: kq-hole.jpg # Add image post (optional)
tags: [nas] # add tag
---

{% include figure image_path="/assets/img/asterix2.jpg" alt="All the asterix you can eat" class="image-small image-right" caption="Asterix Omnibus: one of about 6." %}


My plan was to approach this nas building project methodically and document each of the steps thoroughly, allowing me to retrace my footsteps in 6 months time when I've made a complete balls up everything. But I completely ran out of patience, installed a spare drive into the new chassis yesterday, and fired it up. There's only some much virtual box one can take.

And boy, was it worth it. I have seriously not felt so excited since... well, since I got the complete set of Asterix Omnibuses for Christmas one year. So many books, so many opportunities, such a limited time before school starts and the humdrum routine of life squishes the excitement out of you like air out of a whoopie cushion.

And that was me this weekend: *Freenas. Check. Drives. Check. Voooom! Look! VM's! Look Jails! Look plugins! What is a ZVOL? Is it better than a dataset? Too many ip addresses. I hate my router. Bring on dnsmasq. What?*

So let's start at the beginning.

The setup that is running currently is the Fractal design chassis is 3 hard drives, 2 x 2TB Samsung's and 1 x 1.5Tb Toshiba. The machine has 16Gb Ram, with a 4 core Intel i5 processor.

## FreeNAS installation.

I installed FreeNAS onto the 1.5TB drive, and set up the two 2TB drives in a  RAID0 pattern. This is a temporary arrangement because I am still deciding on what drives to get for the final layout. It feels like a lack of frustration tolerance, but really, having used it for a weekend, I feel a bit vindicated. I know a lot more about the setup, the capabilities of the hardware and FreeNAS, and as you might have guessed, I'm really excited about this project now.

{% include figure image_path="/assets/img/myphoto1.jpg" alt="Ahh... a text based installer." class="image-small image-left" caption="FreeNAS going onto my new kit." %}

So what I have done with the last 36 hours? Where did the time go?

## Landscape

I spun up a small Ubuntu 16.04 VM using Bhyve. Apart from 3 false starts, it went really well. My 3 user errors were:

1. Telling ubuntu not to use UEFI when in actual fact Bhyve seems to support UEFI quite well
2. I messed up the cardinality for zvol that provides the storage for the vm's hard drive. I thought that I'd make a zvol for more than one VM hard drive, but given that you have to allocate space when you create the zvol (i.e. you have to do the thinking about what you want to do with your new VM at that stage), it seems to make more sense to have a 1 to 1 allocation of zvol's to disks. I guess I'll figure out more as I go along.
3. The AHCI network didn't connect first time round. I had to try again with the VirtIO setting and that worked.


{% include figure image_path="/assets/img/landscape.png" alt="Look at the ubuntu purpleness!!" class="image-small image-right" caption="Looking at Landscape installing via web vnc" %}

A quick note about things that I really liked about the VM installation process:

* There's no fancy wizard but it's easy enough to create a VM, add memory, CPU, a hard drive and installation media. The lack of wizard actually makes it really quick.
* The networking stack just worked. There's not a lot of options - you can pick which real nic to bind to, and Bob's your slightly lecherous uncle.
* The VNC access makes the fact that it's on server far away (potentially) really easy to overcome. And if you remember to click the 'vnc web' option on the vnc device, then you don't even have to install client software. 10 out of 10 gents.

The landscape installation process is simple enough:  add the PPA and do an apt install.

|If you're sensitive about systemd close your eyes now: I know this is heresy to even mention systemd in the same sentence as FreeBSD, but one of the great things I love about systemd is that all system daemons work the same way, and there's single registry of everything running as a daemon. One quick ```'systemctl -a | grep landscape'``` and I could see that the landscape process was up and running. In addition I could see that it wasn't only one process - there were a couple of things running that seemed to be related to landscape. Hmmm... interesting.
{: .notice}

Ok, you can open your eyes now.

So far so good. I used my browser to hit the IP address assigned by my router, and we land up in the first rabbit hole. The first form you fill in redirects you to a dead page because the there is something in the logic of the form submission that sends you to the domain name that doesn't exist (happens to be the hostname that you setup when you installed the server - details). If, like me, you've never got around to setting up DNS in your home because you've never had a server running all the time, then this is a slightly irritating, old and not readily soluble problem. BUT - we have a server now, right? RIGHT? So it's time to stop kicking the DNS can down the road.

Parking the DNS issue, I hacked a config file (can't remember which one), restarted landscape with contentious but predictable systemd, and presto, I'm can submit the form and don't get redirected to a non-existent host name. The next step is to install the landscape client and register it with the server - and that's where the second rabbit hole presents itself.

The landscape server, like many servers these days doesn't really do http. Justifiably, I suppose, it redirects to https, and out of the box comes with self signed certs. Irritatingly, the client doesn't like the self signed cert, and doesn't provide an option to ignore certs that can't be validated. I tried removing the redirect in the apache code for landscape - that broke everything so I put it back. I tried fishing the cert and root key off the landscape server and referencing them in the client. No joy. So a quick hack job is not going to work here. Bother, said poo.

## DNSMasq

After a brief flirt with avahi (works really well with OSMC and OpenElec), I decided that I should try a jail running dnsmasq. After a bit of reading it appeared to be quite easy to setup, with the killer feature being that it delegates to the servers hosts file by default, thereby completely removing the pain of setting up a DNS server. DNSMasq is now my new favourite open source project, and I don't know why I didn't set it up ages ago.

But just when I congratulating myself with a cup of instant Jacob's coffee all the way from home, the third rabbit hole came into view: setting up dns on clients over different platforms is frustrating. On a Mac it's ok - you can override the DNS settings that came from the DHCP server, and add a domain search suffix, so 'ping landscape' works. On ubuntu however, the pain was greater. Setting the DNS server was fine, but between systemd and the gnome network manager, it's almost impossible to set a search suffix. You can set it in resolve.conf, but there's a giant warning at the top of the file saying 'managed by systemd', so that will probably not be a good move.

The lovely router provided by Virgin media also doesn't allow me to override the DNS server being sent to clients. So no joy there.

In the end I had to use the obscure 'nmcli' command  force it to be set on the network connection directly. Ultimately, the most elegant solution would be to use dnsmasq to dish out IP addresses too. Something to look into later, but for now I have dns server and that feels like a step forward.


{% include figure image_path="/assets/img/gogs.png" alt="Go, Git server, Go" class="image-small image-right" caption="Gogs: doin' it's thang" %}

## GOGS

Installed in a jail out of the standard BSD package repo. About the easiest thing in the world to set up.

## Plugins

Oh the plugins. The marvelous, marvelous plugins. There are a lot of good plugins. And what's more I really like they way they work.

Usually plugins are packaged and installed in a custom format that requires dark arts to extend or alter in any way (yes, that's a jab at qpkg). The great thing about FreeNAS plugins is that once they're up and running, they're just jails (not 'just jails' in a disappointed way - 'just jails' as in marvel at the simplicity and flexibility of the jails!). I installed nextcloud, transmission, transmission again to fiddle with open vpn clients, plex server, bacula_sd and xmrig. The only one that didn't work out of the box was bacula_sd.

So after a busy weekend, it's Sunday night, and I'm feeling more optimistic than ever about this project. I'm going to sign off now and spend the last few minutes of this evening looking over hard drives on komplett.ie.
Rabbit holes on the backlog:

{% include figure image_path="/assets/img/rabbithole.jpg" alt="Sorry, what?" class="image-small image-right" caption="A rabbit. Down a hole." %}

* I have to do this all again, when the new drives arrive
* DNS without DHCP is only half a job.
* Let's encrypt - or the slightly less nuclear option of setting up my own CA
* Once both of those work, I can have another crack at landscape.
* Secure nginx reverse proxies would make everything that much cooler.
* How would you write your own FreeNAS plugin?
* OpenVPN client in Transmission
* Monero Mining - is it a thing?
* Avahi - how come it works so well on a raspberry pi???
