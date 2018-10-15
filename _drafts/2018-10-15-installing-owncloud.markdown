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
