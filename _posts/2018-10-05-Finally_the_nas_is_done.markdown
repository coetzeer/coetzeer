---
layout: post
title: Finally the NAS is done
date: 2018-10-05
description: I installed postgres
img: success.jpg # Add image post (optional)
tags: [nas,success] # add tag
toc: true
---

So, after 8 months, I've finally finished of messing with the NAS hardware. It's been a long journey, and while I'm very far from finished with FreeNAS, FreeBSD and all of that wonderful permissively licensed goodness, I am at a point where I can stash the Fractal Design case down in the bookcase, and forget that it's there for a while.

{% include figure image_path="/assets/img/success.jpg" alt="Image from https://pixabay.com/en/users/StartupStockPhotos-690514/" class="image-small image-right" caption="Success! (pixabay)" %}

# What happened?

So were we left it back in April, I was looking down the barrel of 'Die Groot Trek Drie' (The Great Move Three), because I had one hard drive out the array of 6 that was not in the zfs Raid pool. In order to get it into the pool, I was going to have to nuke the existing pool, and create a new pool over the larger set of drives, which means lifting all the data that's there, stashing it somewhere, and then copying it back.

At the time of engaging with this, there was roughly 10Tb of data to move twice. The thing that was different about this time was that I had nuked the original copies of all my data that had been lurking on the various D-Links, QNaps and Time capsules that I had lying around. I had also added significantly to it as I've had 3 working torrent jails operational since April, nicely stashed off behind VPN tunnels.

{% include figure image_path="/assets/img/toaster.jpg" alt="No hands were harmed during the creation of this blog post" class="image-small image-left" caption="Lovingly dubbed 'The toaster'" %}

Having been down this road (albeit in one direction), I can safely say that using my ropey home network to transfer 10Tb of data around is a recipe for disaster. I've run out of SATA connections on the FreeNAS. So that leaves USB. I have a SATA dock hanging around that I got after I blew up a harddrive in a ropey enclosure a few years ago (yes - there's a pattern of exploding harddrives in my past), so that seemed like the best solution of presenting the storage to FreeNAS.

Two more burning questions: what storage? And are we going to do rsync again?

In order to try and get enough spare hard drive storage for this lot, I had to beg, borrow and steal hard drives from anything in my house that qualified. That included all the old NAS's that were hanging around. The only spinning disk that was co-opted into this operation was the Time Capsule, which was left in place to act as a backup server for my wife's Mac. If things go bad, at least her computer will be fine.

And what to do about rsync? It's a massively powerful tool, that I have managed to spectacularly misuse. I used it with great success in the first and second moves, making backups of my backups, but having been through that twice, I've reached a life affirming conclusion: enough rsyncing can destroy the soul. I simply don't want to do it anymore.

I remembered reading about moving ZFS datasets, so I looked into that a bit more. Provided the USB drives I use are set up as ZFS volumes, it should be straight forward enough to simply send a dataset from one volume to another using zfs send and receive.

e.g.

```bash
zfs send -v vol01/Photos@tgt | zfs receive -v backup01/Photos
```

So, deconstructing the above:
1. __zfs send -v___ - the '-v' is important as with my USB2.0 hard drives, it takes a day to move a 1TB, so having some sort of sign of life is reassuring.
2. __vol01/Photos@tgt__ - vol01 is the parent dataset, Photos is a child, and 'tgt' is a snapshot that I made manually (tgt - the groot trek). The takeaway here is that you send a SNAPSHOT, rather than 'the dataset', which makes sense as the data in the dataset could be changing while the zfs send is happening.
3. ```|```  - good ol unix pipe. We're sending to zfs receive here, but I guess this means that you could send the output of this command anywere. E.g. a file.
4. __zfs receive -v__ - ask zfs to receive the data coming through the pipe, again with the -v flag to tell us what's going on.
5.  __backup01/Photos__ - backup01 is the dataset that is on the USB disk that I've imported. Photos will be the name of the dataset that the data will be imported into.

# Mounting the drive
So I've gotten a bit ahead of myself here: you first have to go from a disk rescued out of a dodgy NAS to a newly minted ZFS dataset. This, as it happens, is actually quite easy. The way that I usually did it was by using the FreeNAS gui to import new disks.

TODO: get screenshot.

Once you insert the new drive, go to the 'Storage' tab in the gui and click 'Import disk'. Avoid the temptation to add it to an existing dataset (that would defeat the object in the most spectacular way) - import it as a new dataset called 'backup01' for example.

Once the drive has the dataset up and running, on the command line you simply import and export the zfs pools whenever you need to (except for the first time when FreeNAS has looked after you.)

```bash
zpool import backup01 #after you've plugged it in
#do amazing things
zpool export backup01 #then you can unplug

```

# Neat Datasets

The next job was to break up some of the larger datasets that I had into ones that would fit onto the selection of 1.5TB and 2TB drives that I had my disposal. This was interesting as it was the first time that I'd thought about the structure of my datasets since the very first time I'd set up the NAS.

I landed up making some nested datasets e.g. breaking up 'Media' into 'Media/Movies', 'Media/Music', etc. This landed up giving me the granularity that I needed, but 2TB seems to be a reasonable sweet spot for home-data-hoarding volumes.

So, to send nested datasets around is just as easy:

```bash
zfs send -v vol01/Media/Movies@tgt | zfs receive -v  backup05/Movies
```


# Incremental backups

So while I've rejigging my datasets and copying the eligible ones around, life goes on. In this case I needed to clear up some space on my laptop, and that involved backing up some scanned photos that I hadn't put onto the NAS yet.

But I'd already backed up the Photos volume (being the most important). Well, as it turns out this is not a problem, because sending an incremental backup to an existing drive is really easy. Remember how we had to send a SNAPSHOT, rather than just the volume? Well this makes sense now as we can tell zfs exactly what the increment size is.

```bash
zfs send -v -i vol01/Photos@tgt vol01/Photos@tgt2 | zfs receive -v backup01/photos
```

1. Make a new snapshot - in this case it's tgt2 (the great trek 2)
2. Now the zfs send part of the command specifies the increment - __vol01/Photos@tgt__ to __vol01/Photos@tgt2__.
3. Once that completes, you should land up with __tgt__ and __tgt2__ on backup01. You can obviously clean that up later should you need to. For incremental updates, the most recent snapshot is probably the most important one.

# Hardware changes

So once all the data had been copied across, I had to take bull by the horns and nuke the vdev. I had some other small hardware updates that I wanted to make before then too.

The current setup booted off a Sandisk Cruzer Blade usb flash drive. There are various reasons why this wasn't ideal: flash drives work until they they don't, and that transition is sudden and jarring. Also, it didn't play well with smartctl - I landed up having to hack a file on the FreeNAS appliance to suppress a warning that was showing up. This lasted about a week until I did a software upgrade, whereupon my hack was reverted and I landed up with the warning again.

So, my plan was to use the ISA slot hard drive caddy that I bought back in March, but with all the faffing about with SATA cards, I landed up pulling out and not using.

{% include figure image_path="/assets/img/amazon_caddy.png" alt="" class="image-medium image-centre" caption="ISA slot caddy." %}

The problem with that was that I have officially exhausted my SATA ports: the Sodial SATA is working: I don't want to mess with it, and although it has 3 SATA ports (bringing me up to a total of 7), only 2 ever work. This fills me with rage, but it did say it clearly on amazon when I bought it.

So that leaves me with the only option being USB - maybe to route a cable outside the box so I can plug it in, which would look weird and be unsafe. I have two unused USB headers on my motherboard, so I bought a header to USB cable from amazon to keep the connection for that drive purely internal.

{% include figure image_path="/assets/img/amazon_usb_header.png" alt="" class="image-medium image-centre" caption="USB header cable." %}

The last 2 things I needed was a SATA to USB converted for the drive in the caddy, and a molex to SATA power adapter. Both of these off amazon were cheap enough.

{% include figure image_path="/assets/img/amazon_molex.png" alt="" class="image-medium image-centre" caption="Molex to SATA power connector" %}

{% include figure image_path="/assets/img/amazon_usb_sata.png" alt="" class="image-medium image-centre" caption="SATA to USB converter" %}

The installation of these components was actually pretty straight forward. The hardest part was fitting the internal usb cable into the header as there wasn't much room between the enormous heatsink and the pci card. Also, the cheap cable from Amazon didn't have the blocked off pin in the header plug, so I didn't know which direction to insert it. Of course, I got it wrong the first time, so the awkward battle for USB header supremacy with long nosed pliers had to happen twice, but I was victorious in the end.

# Moving back in

So, with the data moved off, and a fresh copy of FreeNAS installed on the 2.5' drive stashed in the ISA caddy, I was ready to do the nuke and pave, and then the great copy back.

Being the sort of individual that I am, I put it off for 2 weeks. And then when the self loathing kicked in, started the the process at 2 oclock in the morning, tired and angry with myself. Which is exactly when you __shouldn't__ do operations like this.

But, everything went surprisingly smoothly. The only bad thing was one of the drives failed, but I had feeling that would happen. The data that was on it wasn't serious (Games), and it will provide me with some interesting learning opportunities to see if I can recover at least some of that data.
