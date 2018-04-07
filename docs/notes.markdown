---
layout: post
title: "Notes"
---
![Picture description](/assets/img/notes.png)

# What's here:
Small ideas and notes that aren't really worth writing a whole huge post about.

# Bhyve - Centos EUFI woes.
The only Centos 7 ISO image that works with Bhyve is the 'Everything'

![Picture description](/assets/img/centos_everything.png)

# Bhyve ubuntu MBR
On FreeNAS 11 Update 2 you run into a problem whereby Bhyve looks for the boot image in the wrong place. Well - when I say wrong, it's just wrong right now given that every distro seems to be different and the UEFI spec is a moving target. So no disrespect to the Bhyve devs.

```grub-install --efi-directory=/boot/efi --boot-directory=/boot --removable```

Actually, I think this is post worthy.
Details:
* [here](https://forums.freenas.org/index.php?threads/ubuntu-vm-reboots-to-limited-shell.62558/#post-446382)
* [and here](https://forums.freenas.org/index.php?threads/howto-how-to-boot-linux-vms-using-uefi.54039/)
* [youtube video of it here](https://www.youtube.com/watch?v=GjMIgkC5REM&t=603s)
