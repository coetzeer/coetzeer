---
layout: post
title: "One Line Wonder: Blacklist the Radeon driver on an old Macbook Pro"
date: 2020-06-21
img: stock01.jpg 
tags: [ubuntu, radeon, 20.04, blacklist, macbook] # add tag
toc: true
---

I decided to test the daily images released in the leadup to the GA of Ubuntu 20.04. When I started, things worked very well on my test laptop, [being a 2011 MacBook Pro](/docs/gear.html#marula). After a few days, an update seemed to break the startup sequence on my aging test rig. The problem with that particular laptop is that one of the 2 built in graphics cards has broken. The standard Intel graphics accelerator is still working fine, but the discreet graphics card is a Radeon effort which gave up the ghost back in 2018. Up until now, Linux has been perfectly happy to ignore the broken graphics card, but as of this update that behaviour has changed.

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

Under normal circumstances, if the changes to `/etc/default/grub` don't work or you haven't had a chance to make them (e.g. like on the boot after the install), you can just use the method of changing the linux kernel parameters in the grub menu. However, on this particular Mac, the grub menu doesn't present itself.

This really only leaves you with one option: try and boot something that gets you to a command line and then make changes to the grub config directly. I managed to do this with the ubuntu install media. 

Step 1 is mount the local drive that contains your /boot directory:

``` mount /dev/sda1 /mnt ```

Step 2 is edit the grub configs. There can be 2: `/boot/grub/grub.cfg` and `/boot/efi/grub/grub.cfg` There are warnings at the top of these files telling you not to edit them by hand, but this is a temporary change. Look for instances of 'quiet splash' and add '**radeon.modeset=0**'

For example:

```bash
menuentry 'Ubuntu' --class ubuntu --class gnu-linux --class gnu --class os $menuentry_id_option 'gnulinux-simple-d4ac9aad-3e0e-417b-9684-22182d6d1f62' {
        recordfail
        load_video
        gfxmode $linux_gfx_mode
        insmod gzio
        if [ x$grub_platform = xxen ]; then insmod xzio; insmod lzopio; fi
        insmod part_gpt
        insmod ext2
        set root='hd0,gpt2'
        if [ x$feature_platform_search_hint = xy ]; then
          search --no-floppy --fs-uuid --set=root --hint-bios=hd0,gpt2 --hint-efi=hd0,gpt2 --hint-baremetal=ahci0,gpt2 d4ac9aad-3e0e-417b-9684-22182d6d1f62
        else
          search --no-floppy --fs-uuid --set=root d4ac9aad-3e0e-417b-9684-22182d6d1f62
        fi
        linux   /boot/vmlinuz-5.4.0-37-generic root=UUID=d4ac9aad-3e0e-417b-9684-22182d6d1f62 ro  quiet splash radeon.modeset=0 $vt_handoff
        initrd  /boot/initrd.img-5.4.0-37-generic


```

Step 3 is to reboot and hope that it works. If it does, loop back to making an edit in `/etc/default/grub`