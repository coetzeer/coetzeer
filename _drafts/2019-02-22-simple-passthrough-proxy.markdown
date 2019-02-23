---
layout: post
title: "A simple passthrough proxy for RPM and DEB packages"
date: 2019-02-22
description: A targeted policy of unholiness.
img: books.jpg # Add image post (optional)
tags: [squid, packages, caching, jails] # add tag
toc: true
---

Like many Linux and BSD nerds everywhere, I occasionally have the need to download a package or two. Often times this is a one off: An ubuntu 18:10 desktop that is a relative snowflake amongst the fleet of Ubuntu 18:04 laptops and Centos 7 servers, or the odd experimental deviation into Solus OS. These things happen, and in those scenarios, there is no optimisation to downloading a package apart from finding a speedy or geographically relevant mirror.

{% include figure image_path="/assets/img/ubuntuflavors.jpg" alt="Willie Wonker would have a field day" class="image-small image-right" caption="Any flavour you can think of." %}

But sometimes you find your self attempting (or planning) to download the same package over and over again. It may be that kubernetes cluster you're experimenting with - 12vm's running a bog standard version of your favourite host OS, or sometimes even the frivolous download and update of slightly different Ubuntu flavour (because we find that sort of thing fun). In that case, having a copy of a package available on your local network will greatly increase the download time, and save some bandwidth on an internet connection already groaning under the weight of Netflix and a couple of remote Citrix sessions.

So the question is, how do you find an easy and transparent way of 'caching' a package locally, such that a machine needing it will find it without a massive configuration overhead. There are a couple of options:
1. An enterprise type solution like Satellite (or SpaceWalk, the upstream open source project). A problem with approaches like this is that they tend to want to synchronise the entire upstream repository. This could be several hundreds of gigs, that you'll have to somehow keep up to date. This is slow, requires a lot of storage and is quite inefficient, as you're downloading and storing packages that you'll never need.
2. An 'off the shelf' pass through caching solution e.g. apt-cache or Nexus OSS set up with some YUM proxy repos. These are better. They are more lightweight in terms of storage as they will only download a source package once. The overhead that these options have is around configuration: you're inserting these caches in between YUM or APT and the server that has the packages. This means
  - you've got to set up the caching server to aware of a particular upstream e.g. ie.ubuntu.com. If you add another repository, you've got to go back to your cachnig server, and make sure it knows about that repository.
  - you've got to update your client config to reference the caching server. Your sources.list can't reference  ie.ubuntu.com anymore - it has to go to your caching server to request packages. Again, once it's set up, there's no problem. But you won't realise any benefit from your caching solution until you've got this config in place on all your potential clients. And - irritatingly, if you're setting this up on a laptop that might operate away from your home caching server, you might find yourself switching between a proxied config and a non-proxied config, and the more complex this config change is, the more error prone this will be.


  <div class="notebox">

There are heaps of tools that fall into this category:
<ul>
<li><a href="https://www.sonatype.com/nexus-repository-oss"><b>Nexus OSS</b></a> - has built in support for RPM and a community plugin for APT. Runs as a java process, so it's a little more memory hungry. </li>
<li><a href="https://help.ubuntu.com/community/Apt-Cacher-Server"><b>Apt cacher</b></a> and <a href="https://wiki.debian.org/AptCacherNg"><b>Apt Cacher NG</b></a> - These seem to be smart enough to prevent a rewrite of your repo definitions. </li>
<li><a href="https://launchpad.net/squid-deb-proxy"><b>Squid deb proxy</b></a> - seems to be some prepackaged config for squid to achieve a reasonable caching strategy." </li>
</ul>
  </div>

3. A more generic network proxy configured to hold onto packages via a specifically configured proxy. This is a little harder to get right as the configuration of a proxy package such as squid is deliberately high level -  it's set in terms of networks, acls and a language to describe any HTTP or FTP (or Gopher?) traffic. But the payoff is great: you can tell it to store things ending .rpm or .apt with a special policy that keeps these files hanging around for a long time. This is generic enough that you don't need to call out specific upstreams. Client side configuration is limited to 2 steps:
- Enable a generic http or https proxy
- Switch off dynamic mirror selection - this is important as the cache uses the hostname, the path and the name of the file as a cache key. A file downloaded from ie.ubuntu.com won't be cache if you try and subsequently download it from uk.ubuntu.com.

So with a bit of google I found a couple of good steers. I ultimately went for a mix of the two solutions described below:
- [midnightfreddie.com](https://www.midnightfreddie.com/using-squid-to-cache-apt-updates-for-debian-and-ubuntu.html) - Sets up a good cache for Debian and Ubuntu repos
- [Lazy distro mirrors](https://ma.ttwagner.com/lazy-distro-mirrors-with-squid/) - similar approach for RPM, but also experiments with web accelerator mode and treats upstreams as cache-peers. The downside of this is that you're back to nominating specific upstreams.

# As always, start in jail

This will be hosted on the FreeNAS box, and will take advantage of the fact that it's on most of the time and has storage to spare.

```
iocage create -n proxy dhcp=on allow_sysvipc=1 bpf=yes vnet=on -t ansible_template_v2 boot="on"
iocage fstab -a proxy /mnt/vol01/apps/proxy/cache /var/squid/cache nullfs rw 0 0
iocage fstab -a proxy /mnt/vol01/apps/proxy/logs /var/log/squid nullfs rw 0 0

```

Note there that I have externalized the logs directory and the cache directory into my generica 'apps' dataset.

The above configuration will also allow me to reference the proxy as 'proxy' or 'proxy.home' thanks to the DHCP config and the PiHole

# Install squid

Super easy:

```
pkg install squid3
```

# Get configuring

Crack open __/usr/local/etc/squid.conf__:

```
TODO: dump squid config
```

The important bits there:
- cache_replacement_policy heap LFUDA - Least frequently used with Dynamic again. This is a deviation from LRU (Least recently used), which keeps objects based on how recently they've been hit. LFUDA optimizes for byte hits rather than hits by keeping bigger (potentially less popular) objects around.
- maximum_object_size 4096 MB - increase the maximum size of the objects in the cache. Import given the above change, and also as package sizes can vary dramatically, and a great many of them will be above the 4mb limit that's the default.
- refresh_pattern: (https://ma.ttwagner.com/lazy-distro-mirrors-with-squid/)[Matt Wagner] made me laugh with his discription of 'do unholy things to the refresh patterns'.  Yes - you have to break all sorts of 'rules', possible ones that would make proxy admins everywhere have convulsions. But this proxy is being tailored for a VERY specific purpose
  - __-i .rpm$__ - Case insensitive match to things ending in .rpm
  - __129600 100% 129600__  - min, percent, max - i.e. keep objects around for 90 days. These usually govern objects that don't have an explicit expiry time set.
  - __refresh-ims override-expire__ -  refresh-ims makes squid check with the backend server when someone does a conditional get, and override-expire enforces min age even if the server sent an explicit expiry time. According to the squid docs, the latter violates an HTTP standard, but again, this is not a general purpose proxy server.


# Client side config

On Ubuntu:

```
TODO: show ubuntu apt.conf
```

On Fedora:

```
TODO: show fedora yum.conf

```

# Seeing it action

```
TODO: Do a dump of the logs.
```

# In conclusion

We've had to do some unholy things, but those have been limited in scope. So it's more a 'targeted policy of unholiness' for packages, while the rest of the internet skates by fairly unscathed. So far, the only thing that I've found that doesn't like making connections through the proxy is the Linux Discord app. I'm not sure if that's related to the LFUDA pattern, or some sort of leakage through to one of the more aggressive refresh patterns.

This is far from a proxy-panacea, but it keeps the Netflix stream running and my wife happily watching 'Say yes to the dress'. This is a good thing.
