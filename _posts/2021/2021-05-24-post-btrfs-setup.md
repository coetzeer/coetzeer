---
layout: post
title: "BTRFS goodies for Fedora"
date: 2021-05-24
description: "Set up btrfsmaintenance and snapper to get the most out of BTRFS on Fedora 34"
img: butter-1449453_1920.jpg
tags: [fedora,btrfs,btrfsmaintenance,snapper] # add tag
toc: true
---

Since Fedora 33 [^1], BTRFS is the filesystem you get out of the box when you install Fedora. From Fedora 34 [^2] we've got block level compression out of the box too. So, with BTRFS getting lots of attention and use, I went looking through my previous investigations and optimizations to make the most out of the file system.

# Snapper

Snapper is mature and well documented [^3] so I'm not going to go into much detail about what it is and what it does. As a summary, it wraps functionality to create, introspect, revert and remove BTRFS snapshots. In combination with *python3-dnf-plugin-snapper*, it makes snapshots before and after the installation of new packages. 

Install the packages

```
dnf install snapper python3-dnf-plugin-snapper
```

Create a snapper config:

```
snapper -c root create-config /

```

Make adjustments to the config at ```/etc/snapper/configs/root```. The ones I made are:

```
TIMELINE_MIN_AGE="1800"
TIMELINE_LIMIT_HOURLY="5"
TIMELINE_LIMIT_DAILY="7"
TIMELINE_LIMIT_WEEKLY="2"
TIMELINE_LIMIT_MONTHLY="1"
TIMELINE_LIMIT_YEARLY="0"

```

I'm not using cron, so we need to setup the systemd timers. To adjust when the timers run, you can use dropins:

```
mkdir /etc/systemd/system/snapper-cleanup.timer.d
cat << EOF > /etc/systemd/system/snapper-cleanup.timer.d/local.conf
[Timer]
OnUnitActiveSec=1h
EOF

mkdir /etc/systemd/system/snapper-timeline.timer.d
cat << EOF > /etc/systemd/system/snapper-timeline.timer.d/local.conf
[Timer]
OnCalendar=
OnCalendar=*:0/5
EOF

```


Enable the systemd timers:

```
systemctl enable snapper-cleanup.timer
systemctl enable snapper-timeline.timer

systemctl start snapper-cleanup.timer
systemctl start snapper-timeline.timer
```

Verify that they're running:


```
list-timers | grep snapper

Wed 2021-05-26 00:10:00 IST 1min 36s left Wed 2021-05-26 00:05:09 IST 3min 13s ago  snapper-timeline.timer       snapper-timeline.service
Wed 2021-05-26 01:00:09 IST 51min left    Wed 2021-05-26 00:00:09 IST 8min ago      snapper-cleanup.timer        snapper-cleanup.service


```

__Note:__  There is another timer called snapper-boot that tells snapper to take a snapshot on every boot. I didn't enable it as I felt it didn't suit my use case. 

# btrfsmaintenance

Btrfs maintenance [^4] is a little more obscure than snapper is. It's well known enough that it's been packaged into Fedora. It sets up timers (or cron entries) for 4 functions:
- balance - moves data around BTRFS extents to make sure it's distributed evenly around physical volumes
- defrag - an excerpt from [here](https://kernelnewbies.org/Linux_3.0#head-3e596e03408e1d32a7cc381d6f54e87feee22ee4) - _Btrfs lays out the data sequentially when files are written to the disk for first time, but a COW design implies that any subsequent modification to the file must not be written on top of the old data, but be placed in a free block, which will cause fragmentation (RPM databases are a common case of this problem). Aditionally, it suffers the fragmentation problems common to all filesystems._ This defragmentation routine addresses this problem
- scrub - Scrub operation reads all data and metadata from the devices and verifies the checksums. On a volument with only 1 disk, it will only highlight bitrot early. On a system with disk redundancy it will repair the affected blocks.
- trim - The TRIM operation (aka. discard) can instruct the underlying device to optimize blocks that are not used by the filesystem. Off by default so it does not collide with _fstrim.timer_ - I've left it disabled as I have the fstrim.timer running out of the box on Fedora

Install the package with the following:

```
dnf install btrfsmaintenance

```

This will put the relevant scripts into ```/usr/share/btrfsmaintenance``` and puts a config file in ```/etc/sysconfig/btrfsmaintenance```. Once you've adjusted the config file, run the following to install and kick off the timers:

```
cd /usr/share/btrfsmaintenance
btrfsmaintenance-refresh-cron.sh timer

```

Verify that they've been installed:


```
list-timers | grep btrfs
Mon 2021-05-31 00:00:00 IST 4 days left   n/a                         n/a           btrfs-balance.timer          btrfs-balance.service
Tue 2021-06-01 00:00:00 IST 5 days left   n/a                         n/a           btrfs-defrag.timer           btrfs-defrag.service
Tue 2021-06-01 00:00:00 IST 5 days left   n/a                         n/a           btrfs-scrub.timer            btrfs-scrub.service


```


[^1]: Fedora 33 release notes: [https://fedoraproject.org/wiki/Releases/33/ChangeSet#Make_btrfs_the_default_file_system_for_desktop_variants](https://fedoraproject.org/wiki/Releases/33/ChangeSet#Make_btrfs_the_default_file_system_for_desktop_variants)
[^2]: Fedora 34 release notes: [https://fedoraproject.org/wiki/Releases/34/ChangeSet#Enable_btrfs_transparent_zstd_compression_by_default](https://fedoraproject.org/wiki/Releases/34/ChangeSet#Enable_btrfs_transparent_zstd_compression_by_default)
[^3]: [http://snapper.io/](http://snapper.io/) and [https://wiki.archlinux.org/title/Snapper](https://wiki.archlinux.org/title/Snapper)
[^4]: [https://github.com/kdave/btrfsmaintenance](https://github.com/kdave/btrfsmaintenance)