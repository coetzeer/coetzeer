---
layout: post
title: "One Line Wonder: Blacklist the Radeon driver on an old Macbook Pro"
date: 2020-06-21
img: oneliner.jpg 
tags: [ubuntu, radeon, 20.04, blacklist, macbook] # add tag
toc: true
---

I decided to test the daily images during the period leading up to the release of Ubuntu 20.04. When I started, things worked very well on my test laptop, [being a 2011 MacBook Pro](/docs/gear.html#marula). After a few days, an update seemed to break the startup sequence on my aging test rig. The problem with that particular laptop is that one of the 2 built in graphics cards has broken. There's the standard Intel graphics which are still working fine, but the discreet graphics card is a Radeon card which gave up the ghost back in 2018. Up until now, Linux has been perfectly happy to ignore the broken graphics card, but as of this update that behaviour has changed.

The following instructions were adapted from [this](https://askubuntu.com/questions/771562/16-04-power-off-discrete-graphics-ati-amd) askUbuntu page.

# On boot

On the released version of Ubuntu 20.04, the install media fails to boot now too. That means that you need to a way to override the kernel's desire to attach to the Radeon card. You can do this by adding the following to the kernel parameters in the grub menu:

```
radeon.modeset=0
```

# Once you're in

Once you're booted you need to make the following changes to your grub config in ```/etc/default/grub```:

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash radeon.modeset=0"

```

Then run: 

```
sudo update-grub
sudo reboot
```

# Where it gets awkward
