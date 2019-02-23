---
layout: post
title: "Re-installing software Part V : NextCloud"
date: 2018-10-16
description: Installing the cloud that I probably won't use
img: irish_cloud2.jpg # Add image post (optional)
tags: [owncloud, nextcloud, software install] # add tag
toc: true
---

{% include important.html
    content="This article has been sitting in the drafts folder for a long time now, and it's starting to personally offend me. " %}


I've been down so many rabbit holes installing owncloud and nextcloud on the FreeNAS that I'm almost afraid of attempting to document the experience. Some of that experience is negative, and I don't want to be Mr Negative-pants all the time. Some it is boring. Again - Mr Boring-pants is not a moniker that I aspire to. So what I'm going to attempt to do is briefly describe the journey, and not dwell too much on the end state.

# The end state

A good way to not dwell to much on it is to get out of the way right in the beginning. I got NextCloud installed. I did it manually thinking I'd go back and retrofit an ansible script on it. In that regard I failed.

But I did get a functional installation of NextCloud up and running that is service 4 important purposes:

1. It's receiving photos from my iPhone. The reliability of the Iphone app to awaken itself and reliably execute a sync seems a bit spotty from time to time, but it appears to be working more often than it doesn't work.
2. It's acting as a portal for audiobooks. I have mounted my audiobook hoarding zfs dataset into the NextCloud jail. I've then added this read-only external storage to my wife's NextCloud account. She can navigate into this share and download audiobooks onto her phone, and then use the Mp3 Audiobooks app to play them back. This is MUCH easier than attempting to use Itunes to do the same thing.
3. It's acting as a local dropbox replacement. I didn't think I'd need it, but having 3 desktop workstations in play has meant that I've sometimes found it advantageous to have a downloads or notes directory synced.
4. A convenient photo gallery. Up until now our vast collection of family photos has been stashed on drive only accessible via CIFS or AFP link. I've resisted setting up some an official photo gallery, as the effort involved to gather all the photos into a structure that supports something like CopperMine was just not worth it. My photography workflow involves RAW cameral files and a Lightroom database. Exposing my photography archive in the same way as the audiobooks are exposed means my wife can easily get our holiday snaps or our wedding photos.

TODO: Add some screenshots

# How did I get there: the short version






https://ramsdenj.com/2017/06/05/nextcloud-in-a-jail-on-freebsd.html
