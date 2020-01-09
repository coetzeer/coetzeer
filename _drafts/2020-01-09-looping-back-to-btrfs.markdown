---
layout: post
title: "Looping back to BTRFS"
date: 2020-01-09
description: 'Revisiting BTRFS after a some time off'
img: btrfs.jpg 
tags: [btrfs] # add tag
toc: true
---

Back in the beginning of 2019 I bought a new computer and in fit of enthusiasm installed a BTRFS based Ubuntu installation on it. After a couple of months of use, I got a bit disillusioned and gave it up. After listening to lads over at [Linux Unplugged](https://linuxunplugged.com/331) use BTRFS on their root partition to give them a reasonable rollback, the thought occurred that I should try it again.

There are a couple of reason I gave up back in the day. 

1. I couldn't reliably replicate the subvolumes and partitions required to enable the Ubuntu BTRFS toolchain. There are details below, but I grew a bit disillusioned when I couldn't make 'apt-btrfs-snapshot' work on subsequent Ubuntu installations. 
2. I tried a dual boot Fedora/Ubuntu installation on Laptop, and having a BTRFS partition broke the Fedora installer. 
3. Ubuntu seemed to have it's own peculiar tools and patterns for BTRFS which didn't apply to other distros such as Fedora. For example, Ubuntu used 'apt-btrfs-snapshot', but Fedora used snapper with a yum plugin. Now, given that latter has been dropped from Ubuntu in 18.04 and up, using snapper seems to have been the right way to go. None the less, there is additional cognitive load required for switching between distros, and that put me off.
4. I got caught out once or twice with the BTRFS problem of running out of space, and then being completely out of options in terms of getting back to a working state. I did get around them, now that I have the luxury of multiple working machines at my disposal, but this would have been a killer back in the day when I only had 1 laptop. 
5. BTRFS (and ZFS) are like having Gym subscriptions: you only really get the benefit out of them when you put in the work to understand them. It's just a fact that they are more complicated that ext4, ntfs or even afs. And I got tired of putting in the work.
6. I got stuck writing a blog article trying to make a case for BTRFS (which I susbsequently nuked without taking backup) where I tried to explain that BTRFS was a good fit for desktop use, if you ruled out raid configurations and other things that BTRDS is known to be bad at. It's still a good COW filesystem, with cheap snapshots, reasonable tooling, and reasonable performance, and it doesn't come with the memory overhead of ZFS. I couldn't quite wrestle with the details well enough to make a compelling case. Since then, while researching the best way to lay out a file system for BTRFS and how to fix a volume layout that wasn't ideal, I came across this article on the Debian Wiki, that has a really good, uptodate and detailed set of explanations: https://wiki.debian.org/Btrfs. It's not particularly positive, but does lay the truth out, especially the details around how ZFS uses ARC (and hence memory) as an optimisation to get around fragmentation. BTRFS just assumes there will be some fragmentation, so your memory overhead isn't so high, but your maintenance overhead goes up.

# Partitioning as an art form

I somehow managed to stumble upon a working partition and subvolume layout when installing 18:10, but whatever the magical combination of installer options was that allowed it to happen, eluded me for subsequent installations. The 'standard' subvolume pattern, which has an empty base (first) volume, a root volume called '@' and a home volume called '@home', is requirement for the ubuntu based toolchain to work correctly, two of which apt-btrfs-snapshot and timeshift. Also, BTRFS cannot be installed on your /boot partition, which means you need a special /boot partition formatted with ext4. In additional for uefi support you need a /boot/efi partition formatted with fat32. 

```

sda
  - /boot        -  ext4
  - /boot/efi    -  fat32
  - swap
  - root         -  btrfs
     - /      subvolume  @
     - /home  subvolume  @home

```

# Fixing a broken or non-standard partition layout

# Installing apt-btrfs-snapshot

# Installing apt-btrfs-snapper

