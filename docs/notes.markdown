---
layout: post
title: "Notes"
toc: True
img: notes.png # Add image post (optional)

---

# What's here:
Small ideas and notes that aren't really worth writing a whole huge post about.

# Bargain hardware vs Apple Mac price comparison.

Wouldn't it be cool if someone did a price comparison of what's available on Bargain Hardware and the apple mac store?
https://www.bargainhardware.co.uk/

# Take the Brakes off time capsule backups
sysctl debug.lowpri_throttle_enabled=0

# Monitoring a FreeBSD machine
* setting up influx DB
* remote graphite server in FreeBSD - https://www.rudimartinsen.com/2018/04/12/monitoring-freenas-with-influxdb-and-grafana/

# Make a port!
* make a port for Telegraf and Chronograf to get the ticstack running on FreeBSD!

# Installing packages directly on FreeNS

```

# by accident I enabled the freebsd repos on the freenas server.
/usr/local/etc/pkg/repos/local.conf
/usr/local/etc/pkg/repos/freenas.conf

```

# Bhyve - Centos EUFI woes.
The only Centos 7 ISO image that works with Bhyve is the 'Everything'

![Picture description](/assets/img/centos_everything.png)

# Bhyve ubuntu MBR
On FreeNAS 11 Update 2 you run into a problem whereby Bhyve looks for the boot image in the wrong place. Well - when I say wrong, it's just wrong right now given that every distro seems to be different and the UEFI spec is a moving target. So no disrespect to the Bhyve devs.

```bash
grub-install --efi-directory=/boot/efi --boot-directory=/boot --removable
```

Actually, I think this is post worthy.
Details:
* [here](https://forums.freenas.org/index.php?threads/ubuntu-vm-reboots-to-limited-shell.62558/#post-446382)
* [and here](https://forums.freenas.org/index.php?threads/howto-how-to-boot-linux-vms-using-uefi.54039/)
* [youtube video of it here](https://www.youtube.com/watch?v=GjMIgkC5REM&t=603s)

# Format and Mount a USB drive with UFS

from [dan.me.uk](https://www.dan.me.uk/blog/2010/09/26/format-external-disks-in-freebsd/)

Wipe:

    dd if=/dev/zero of=/dev/da1 bs=1m count=128

Create fs:

    newfs -L usbdisk -O2 -U -m 6 /dev/da0

Mount:

    mount /dev/ufs/usbdisk /mnt

# Installing stuff on FreeNAS with ansible

Nice example using the sshjail connection plugin - https://github.com/andsens/freenas-jailconfig

