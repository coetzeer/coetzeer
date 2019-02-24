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

A good way to not dwell to much on the 'Mr BoringPants McFailure' syndrome is to get out of the way right in the beginning. I got NextCloud installed. I did it manually thinking I'd go back and retrofit an ansible script on it. In that regard I failed.

But I did get a functional installation of NextCloud up and running that is servicing 4 important purposes:

- It's receiving photos from my iPhone. The reliability of the Iphone app to awaken itself and reliably execute a sync seems a bit spotty from time to time, but it appears to be working more often than it doesn't work.

{% include figure image_path="/assets/img/nextcloud_phone01.png" alt="Mostly photos of my dog." class="image-small image-centre" caption="Photos going up onto my cloud." %}

- It's acting as a portal for audiobooks. I have mounted my audiobook hoarding zfs dataset into the NextCloud jail. I've then added this read-only external storage to my wife's NextCloud account. She can navigate into this share and download audiobooks onto her phone, and then use the Mp3 Audiobooks app to play them back. This is MUCH easier than attempting to use Itunes to do the same thing.

{% include figure image_path="/assets/img/nextcloud_phone02.png" alt="David Eddings - some of his finest work." class="image-small image-centre" caption="Audiobooks coming down off my cloud." %}

- It's acting as a local dropbox replacement. I didn't think I'd need it, but having 3 desktop workstations in play has meant that I've sometimes found it advantageous to have a downloads or notes directory synced.
- A convenient photo gallery. Up until now our vast collection of family photos has been stashed on drive only accessible via CIFS or AFP link. I've resisted setting up some an official photo gallery, as the effort involved to gather all the photos into a structure that supports something like CopperMine was just not worth it. My photography workflow involves RAW cameral files and a Lightroom database. Exposing my photography archive in the same way as the audiobooks are exposed means my wife can easily get our holiday snaps or our wedding photos.

{% include figure image_path="/assets/img/nextcloud_gallery.png" alt="Mostly photos of my wife." class="image-small image-centre" caption="The NextCloud gallery plugin." %}

# How did I get there: the short version

The REALLY short version is that I followed these excellent instructions: [https://ramsdenj.com/2017/06/05/nextcloud-in-a-jail-on-freebsd.html](https://ramsdenj.com/2017/06/05/nextcloud-in-a-jail-on-freebsd.html)

Of course a did a few things differently:
1. I used Nginx, as per the instructions on the NextCloud Website [^1]
2. I put redis in another jail, as I'd like to use the same instance for other things (e.g. owncloud). This is achieved my telling NextCloud config to use the non-default database (1 in this case) in it's config e.g. [^2]
  ```
  'memcache.local' => '\OC\Memcache\Redis',
  'redis' => array(
       'host' => 'redis.home',
       'port' => 6379,
       'dbindex' => 1,
       'timeout' => 1.5,
        ),
  ```
3. Went down a serious rabbit hole trying to get 'pecl-redis' to install on my hamstrung FreeBSD 11.1 jail. This kept on forcing me back to php 5, which limited me to NextCloud 13 [^3]. Resolved this by sticking to NextCloud 14, not using Redis or ACPU and promising to do a proper job with ansible once I upgrade FreeNAS.

# Conclusion
All that's a long winded way of saying that I'm using it more and more, and that I couldn't get it 100% perfect now. It feels tainted for me, and for now it's stuck behind the FreeNAS upgrade before I can do anything about getting the latest version, using php7 and putting the whole lot into an ansible role.



[^1]: <a href="https://docs.nextcloud.com/server/stable/admin_manual/installation/nginx.html">Latest NextCloud docs for Nginx</a>
[^2]: <a href="https://docs.nextcloud.com/server/stable/admin_manual/configuration_server/caching_configuration.html#id2">NextCloud docs for Memory caching</a>
[^3]: <a href="https://docs.nextcloud.com/server/13/admin_manual/installation/system_requirements.html">NextCloud 13 still supported php5<a/>. From <a href="https://docs.nextcloud.com/server/14/admin_manual/installation/system_requirements.html">NextCloud 14</a> onwards, only php 7+ is supported.
