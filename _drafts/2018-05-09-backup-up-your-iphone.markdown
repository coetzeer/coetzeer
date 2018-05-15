---
layout: post
title: Backing up your iphone with NextCloud
date: 2018-05-09
description: How to back up your precious photos and files from your iphone to a NextCloud instance.
img: nextcloud.png # Add image post (optional)
tags: [nas,nextcloud,iphone] # add tag
toc: true
---

While strolling through a typical Irish garden on a typical Irish afternoon, with typical Japanese drums going bananas in the background and typical Japanese outfits on the not so typical people wandering around in our vicinity, a friend of mine posed me the question: so you've got this awesome NAS - can you use it to backup your iphone? His question was specific to pictures, which is a function that iphones do particularly well, as much as it galls me to admit it. He has limited space on his phone, and limited space on his laptop, and spends far too much time in the nuclear stockpile/ Chinese puzzle game of moving data around various USB drives, his laptop and his phone to try and fit everything in. This always leads to 2 things:
* First, the photo that you want is always on a device that you can't find, or don't have the cable for.
* Second, you're probably going to lose something.

{% include figure image_path="/assets/img/there_is_no_spoon.jpg" alt="There is no spoon." class="image-small image-right" caption="There is no spoon" %}

So I took this question away with me, away from the eclectic [Japanese Culture explosion](http://experiencejapan.ie/) and my friend, who was by that stage tired of me talking about old computer games and even older cameras, and cogitated on it.

Iphones are notoriously locked down. When it comes to file systems and iphones, 'there is no spoon'. There is no filesystem. There are only 'photos' and 'documents' You can't just say 'where are my files' and setup a job to copy them off at a convenient time, as you would be able to do with anyone one of [these 40 android apps](https://play.google.com/store/search?q=cifs%20ftp%20backup&c=apps). Iphones want you to use Itunes, which is program that is the enemy of anyone that used a computer for longer that 5 minutes. Itunes and iphones abstract the details away from you so destructively that it's impossible to apply the usual logic of safe file handling to these products.

What is safe file handling? Well, seeing as how I'm being opinionated (this is MY blog after all), lets define safe file handling as:
* Simple: you, the user, should be easily able to understand what is happening.
* Repeatable: you should be able to perform the same operation again and again. Every time you take a picture, or plugin your phone or join your wifi network, a 'backup' operation should happen against a set of files. This operation should have the same effect, always.
* Safe: You should have some guarantees that name clashes and connections failures don't result in lost data.

Itunes, by definition, can't give these characteristics. It's not running on your phone. It's not a server application, so it's not ubiquitously around. It's so simple it's not useful. Itunes' main use case seems to be to make you so frustrated at badly it manages your own media that you just say 'fuckit' and use the apple cloud services.

So, where does that leave us?

Well, by design, on an Apple hardware product, once you've shunned the Apple software, you're out in the cold.

# What about the cloud?

{% include figure image_path="/assets/img/nocloud.png" alt="There is no cloud." class="image-right" caption="They lied" %}

Something that might be suggested is a cloud service like Dropbox or Apple i-Cloud. That is ruled out for 3 reasons:
* Cloud services are just another place. One of the things we want to do is consolidate storage and not fragment it more.
* Cloud services are expensive. If you want to consolidate storage in a cloud service, it's going to be expensive.
* There is no such thing as the cloud. There's only other people's computers. Just ask [Jennifer Lawrence](https://www.independent.co.uk/news/people/icloud-celebrity-nude-leak-jennifer-lawrence-kate-upton-man-pleads-guilty-a7334031.html).

So no public cloud. No Google, no Dropbox, no iCloud, no Amazon, no Facebook.

So what does that leave? We'll we're back to the NAS. But how do you get stuff onto the NAS?

# Enter NextCloud!

{% include figure image_path="/assets/img/enterthedragon.jpg" alt="NextCloud is like storage for ninjas." class="image-right image-medium" caption="NextCloud: storage for ninjas" %}

NextCloud is like dropbox, but the bit that goes in the cloud. You still need the bit that goes on your computer or phone: the nexcloud desktop client or app. But the magic is in the NextCloud server.

If you're like me, and you've made great personal choices in life including running FreeNAS as your repository of data and happiness, then you get NextCloud for free. Simply install the NextCloud plugin and you're 99% of the way there.

{% include figure image_path="/assets/img/nextcloud_plugin.png" alt="." class="image-right image-medium" caption="Hooray for freenas." %}

If you're not like me, and you've yet to discover the unbridled joy that is FreeNAS, then you're going to have to look for a few ways to host NextCloud.
* You could install it on your laptop. If you're running out of space on your laptop then running a mysql and apache server on your laptop probably wont help that a lot, but there is a really good use case for getting your photos off your phone and onto your laptop easily, provided you've got somewhere to put them after that.
* You could get a raspberry pi and cheap hard drive and setup a next cloud server on the cheap. Something like [syncloud](https://syncloud.org/why.html) or the [NextCloud Box](https://nextcloud.com/box/), or something you put together yourself like [this guy](https://www.youtube.com/watch?v=RNehg6AKCiM)
* You could buy a NAS that supports NextCloud either directly via a plugin or via a Docker Image e.g. the [Qnap Containter Station](https://www.qnap.com/solution/container_station/en/).

I'm not going to lie, it's sometimes not 100% straight forward to setup a NextCloud instance. Depending on what you get, and the level of support, and your access to nerdy friends that do this stuff for fun, your mileage may vary. The most cost effective is probably raspberry pi option, but it is the ugliest, and it is probably the most manual to setup. The NAS option is probably the most expensive, just because you're paying for hard drives and special magic Qnap or Synlogy sauce.
