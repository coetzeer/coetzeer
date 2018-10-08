---
layout: post
title: "Re-installing software Part I : Jail Templates"
date: 2018-10-08
description: Trials in installing Postgres
img: jail_plant.jpg # Add image post (optional)
tags: [iocage, software install] # add tag
toc: true
---

Starting from scratch on FreeNAS with a few limitations. I want to install an awful lot of software, and given that this is the second time I'm doing a lot of this stuff, I'd like to do it properly.

The list of software I'd like to install:
* Postgres + PGAdmin
* Nextcloud
* Select of jails to host VPN's
* Syncthing
* Seedbox - Deluge or transmission
* Nginx as a single point of contacting all of these things
* Organizr
* Gogs (and ultimately Gitlab)

That's kind of the abridged version. Where possible I want to use Postgres, but I have a sneaky feeling that I might have to relent and install Myssql/Mariadb; whether for curiosity or as a dependency for some other application, I'm not ruling it out. Alos, I know NextCloud has seen more love in the last little while that Owncloud, but I'm curious about Owncloud and what it's done with itself in the mean time.

# Package problems

Having done some preliminary IOCage start-up's to see how the new FreeNAS install is working, I've hit a problem that somewhat troubling: Currently on FreeNAS we're caught in a support no-man's land. The folks over at IX Systems are working very hard on FreeNAS 11.2, which is currently in Beta. There is some new sexiness in that release as they want to deprecate the Warden based jail and plugin system and move over to their new GUI, IOCage and the fancy new github based plugin framework.

Understandably they're running a little late, but in the mean time FreeBSD 11.1 has reached the end of it's support life. Now, on my home NAS, if I have to run on slightly older packages for a few months, I don't really mind, but in the mean time it looks like the package system breaks somewhat.

As part of moving over to 11.2, the mainteners of FreeBSD have obiously moved the 11.2 packages into the 'quarterly' folder of their distribution server (http://pkg.FreeBSD.org/FreeBSD:11:amd64/quarterly), which means that when you install something via PKG you get the following warning:


```bash
root@test:~ # pkg install nano
The package management tool is not yet installed on your system.
Do you want to fetch and install it now? [y/N]: y
Bootstrapping pkg from pkg+http://pkg.FreeBSD.org/FreeBSD:11:amd64/quarterly,
 please wait...
Verifying signature with trusted certificate pkg.freebsd.org.2013102301... done
[test] Installing pkg-1.10.5_3...
Newer FreeBSD version for package pkg:
To ignore this error set IGNORE_OSVERSION=yes
- package: 1102000
- running kernel: 1101001
Allow missmatch now?[Y/n]:
```

Your mileage varies if you simply select Y. Installing __nano__ for example, is probably alright, but installing/updating something with very low level dependencies (kernel or shared libs), like openvpn for example, things start to go awry.

So, we're stuck between FreeNAS being a bit late, and FreeBSD being on time. Short of finding any good advice on this apart from 'you should upgrade', I've find the file that points to the package repo online that seems to have changed, and changed it to get packages from the release 1 location.

```bash
root@test:~ # cat /etc/pkg/FreeBSD.conf
# $FreeBSD: releng/11.1/etc/pkg/FreeBSD.conf 320745 2017-07-06 17:22:33Z gjb $
#
# To disable this repository, instead of modifying or removing this file,
# create a /usr/local/etc/pkg/repos/FreeBSD.conf file:
#
#   mkdir -p /usr/local/etc/pkg/repos
#   echo "FreeBSD: { enabled: no }" > /usr/local/etc/pkg/repos/FreeBSD.conf
#

FreeBSD: {
  url: "pkg+http://pkg.FreeBSD.org/${ABI}/quarterly",
  mirror_type: "srv",
  signature_type: "fingerprints",
  fingerprints: "/usr/share/keys/pkg",
  enabled: yes
}
```

For existing jails, I used a quick sed from the host file system

```bash
sed -i .back "s/quarterly/release_1/" \
    /mnt/vol01/iocage/jails/postgres/root/etc/pkg/FreeBSD.conf

```

In a nutshell, change __quarterly__ to __release_1__ - that should do the business. Now, that is something that will fill us with regret at some point in the future, but it keeps us going for now. I figure that when it comes time to upgrade to 11.2, I'm going to have to formally upgrade the jails anyway, so I just need to remember to revert that change back the __quarterly__. 

```bash
root@test:~ # pkg update
The package management tool is not yet installed on your system.
Do you want to fetch and install it now? [y/N]: y
Bootstrapping pkg from pkg+http://pkg.FreeBSD.org/FreeBSD:11:amd64/release_1,
 please wait...
Verifying signature with trusted certificate pkg.freebsd.org.2013102301... done
[test] Installing pkg-1.10.1...

```

# Templates

Another thing that could be potentially time saving is to make a jail template. Apart from changing the package URL, I also install the same packages all the time:
1. git
2. python27 (for ansible)
3. nano (because I don't have mad vi skills)
4. wget
5. curl

In addition to that, seeding the ports tree will save a bit of time should that be required.

So following the instructions on the iocage docs website [here](https://iocage.readthedocs.io/en/latest/templates.html), once you've got your template jail perfect, exit the jail shell session and stop the jail.

```bash
iocage stop template_jail
```

The docs say it's a good idea to add a note to the jail metadata because we all forget things. Sounds like a good idea:

```bash
iocage set notes="jail that allows us to install stuff with
  ansible, that's also pinned to release_1" ansible_template
```

Set the template property:

```bash
root@freenas:~ # iocage set template=yes ansible_template

root@freenas:~ # iocage list -t
+-----+------------------+-------+--------------+------+
| JID |       NAME       | STATE |   RELEASE    | IP4  |
+=====+==================+=======+==============+======+
| -   | ansible_template | down  | 11.1-RELEASE | DHCP |
+-----+------------------+-------+--------------+------+

```

And now fire up a new jail based on the new template:

```bash
root@freenas:~ # iocage create -n owncloud02 dhcp=on \
    allow_sysvipc=1 bpf=yes vnet=on -t ansible_template boot="on"
owncloud02 successfully created!
* Starting owncloud02
  + Started OK
  + Configuring VNET OK
  + DHCP Address: 192.168.0.214/24
  + Starting services OK
```

# Getting the storage right

The last thing I'd like to do differently this time is to externalise the important storage that was inside the jails last time e.g. the file storage for next cloud, and the db backups and converted files for Plex.

This is easy enough once you know where the data lives inside the jail, which is of course is different for every jail. Step 1 is to create a dataset, which is easy compliments of the freenas gui. Mine is now under __/mnt/vol01/apps__.

Now, using the iocage fstab command, we can mount data into a jail with relative ease:

```bash
iocage fstab -a owncloud01 /mnt/vol01/apps/owncloud /var/www/owncloud/data nullfs rw 0 0
```

# Let's get cooking

So, we have the jails thanks to our handy templates, and a way to set up the storage so that we minimize regret. Next: how to automate the crap out of this. This is the second time I'm doing this for a lot of these installations, so I'd be bad devops-er if I did it a third time. Next time: Ansible on FreeNAS with IOcage jails.


