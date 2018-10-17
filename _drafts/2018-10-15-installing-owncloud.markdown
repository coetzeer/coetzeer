---
layout: post
title: "Re-installing software Part IV : OwnCloud"
date: 2018-10-15
description: Installing the cloud that I probably won't use
img: irish_cloud1.jpg # Add image post (optional)
tags: [owncloud, nextcloud, software install] # add tag
toc: true
---
# Why Ray? Why?

Why install OwnCloud? That is a very good question. All the evidence online suggests that NextCloud, the recent fork of the OwnCloud, is seeing all of the development love right now. I'm going to be honest and say that I have an almost of pathological love of things that come in two's. I'm sure there's a name for it, in some dictionary of mild phycological disorders - monophobia - fear of not having a spare.

{% include figure image_path="/assets/img/irish_cloud1.jpg" alt="https://pixabay.com/en/users/Christian_Birkholz-76800/" class="image-small image-right" caption="A hut. And a cloud! See what I did there?" %}

Why have one when you can have two? Once you set aside the obvious and logical arguments, such as you won't use it, it's just using up resources, and it's not as good as the other one, it's hard to raise objections to the statement that 'it's always better to have two rather that one'. There are some exceptions: tax returns, brain tumours, rotten tomatoes. But you could argue that it's always better to have zero rather than one of those. So the fact remains: two is always better.

# Step 1: Make a jail

So, with all objections skilfully repelled, let's get going. The first thing you usually need to do is make a safe place to put this new software: a jail. As will all great software, OwnCloud is going to jail. We will use our fancy new ansible ready template and we will externalise some the directory that holds the owncloud data (non database data).

```bash
iocage create -n owncloud01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -t ansible_template boot="on"
iocage fstab -a owncloud01 /mnt/vol01/apps/owncloud /var/www/owncloud/data nullfs rw 0 0
```

# Dependency management

The next thing we have to do is install all the pre-requisite packages. There is a trick to this, as OwnCloud (and NextCloud) are large software projects that have a LOT of php dependencies. Some of them are optional and enable plugins ore functionality that is non-core, but some are mandatory for basic operation of the product. My preference would be to derive this list somehow, as I don't want to maintain a list of OwnCloud dependencies that will become out of date at the whim of a developer.

Vermaden over his [his blog](https://vermaden.wordpress.com/2018/04/04/nextcloud-13-on-freebsd/) describes how to use the ports system to list out the dependencies for any FreeBSD package using 'make' and the ports system.

```yaml

TODO: show how to get this list of dependencies

```
The listing above does 2 things:
1. Make sure the ports tree is checked out and up to date
2. Gets a list of package names out of the command **make -C /usr/ports/www/owncloud run-depends-list**

{% include important.html content="This couples us to the owncloud port. Is that a bad thing? I don't know right now but I think it's still better than keeping my own list of packages" %}


# Rabbit holes

This playbook gives you a very vanilla install of OwnCloud. Without a lot of digging, it's very easy to get sucked into some topics that are distracting.

{% include figure image_path="/assets/img/rabbithole.jpg" alt="Sorry, what?" class="image-small image-right" caption="A rabbit. Down a hole." %}

## 1. OCC

OwnCloud has a command line utility, that this playbook includes a small wrapper script for. This has immediatly proved useful as it's about the only way that you can do an import into an owncloud account if you don't want to sync everything from a remote location (e.g. importing data from an old account)

But with OCC you can do an awful lot more than that, including doing the initial bootstrap (database and file system config), upgrades, set properties like where the log file lives, and a whole heap of other things.

The problem is that if you run OCC on FreeBSD installation you get a snarky message telling you to use Linux, and I can't find a means of getting around it (apart from looking into the source code). This has already cost me 2 evenings of flootin' with Google and my test jails trying almost everything under the sun, but to no avail. OCC from ansible will just have to wait.  

{% include figure image_path="/assets/img/owncloud_no_freebsd.jpg" alt="FreeBSD no thank you!" class="image-large image-centre" caption="No FreeBSD for you." %}

{% include tip.html content="A tantalising feature of OCC is that you can choose to have the output produced in yaml, json, formatted text or raw text. This is just BEGGING to be made into an ansible module." %}

## 2. Redis and other caches

https://doc.owncloud.org/server/10.0/admin_manual/configuration/server/caching_configuration.html


https://github.com/danb35/freenas-iocage-nextcloud/blob/master/nextcloud-jail.sh
https://ramsdenj.com/2017/06/05/nextcloud-in-a-jail-on-freebsd.html#opcache



## 3. Other Tuning
