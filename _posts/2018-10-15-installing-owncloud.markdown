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



Why have one when you can have two? Once you set aside the obvious and logical arguments, such as you won't use it, it's just using up resources, and it's not as good as the other one, it's hard to raise objections to the statement that 'it's always better to have two rather that one'. There are some exceptions: tax returns, brain tumours, rotten tomatoes. But you could argue that it's always better to have zero rather than one of those. So the fact remains: two is always better.

{% include callout.html content="Edit: This turned out to be an extremely stupid idea. It's now December, and I started digging into this in October. I've been down so many rabbit holes, and all I have so far is an instance of something that I know I wont' use. So ignore me." 
            type="warning" %}


# Rewind

I've rewritten this blog post so many times at this point, it feels like I'm never going to finish it. I've literally had to redo every step of this setup two or three times.

Iteration number one used a FreeBSD 11.1 jail template - the same one I've used for all the other jails I've described here. However, this is the first time I've had any dependency on the ports system. As you'll see below, I tried to abstract away some knowledge about the packages I need to install by using the port for own cloud. This works well when all 3 bits of package infrastructure are in sync: the OS, the package repo and the ports repo. However, with my 11.1 template, I've got one element that's out of whack: the ports tree. 

So Iteration number one always ended in abject failure because:
1. The php version coming out of the latest ports repo is 7.1. 
2. This gets a core installation working, but none of the pecl packages work (postgres drivers, redis, acpu packages) - so it gets you 80% there and the last 20% is impossible.
3. Installing any pecl package automatically removes the php71 packages, so I lost several installations to sudden php version changes.

With iteration number two, I decided that I needed to try to use a more appropriate ports tree. The ports tree SVN repo seems to be branched every quarter, so given that FreeBSD 11.1 went out of support in September, the 2018Q3 branch is probably the safest.

My goal with these setups is to to make them repeatable, ideally to be able to re-run them in a CI type system. So that makes me aware of external dependencies like the FreeBSD SVN servers. There are mirrors that you get routed to behind the scenes, but I don't want to be 'The guy that killed the Ports sever'. Also, cloning the repo takes forever and seems to be about 2Gigs on disk.

I settled on the read only git mirror of the ports tree SVN in Github [^1]. That suited me fine because I could make a local clone, and then feed that clone into any FreeBSD 11.1 jails. And what turned out to be an efficient way to do that is to make another template with the right ports tree in it. This now gives me 2 jail templates:

```bash
root@freenas:~ # iocage list -t                  
+-----+---------------------+-------+--------------+------+                                       
| JID |        NAME         | STATE |   RELEASE    | IP4  |                                       
+=====+=====================+=======+==============+======+                                       
| -   | ansible_template    | down  | 11.1-RELEASE | DHCP |                                       
+-----+---------------------+-------+--------------+------+                                       
| -   | ansible_template_v2 | down  | 11.1-RELEASE | DHCP |                                       
+-----+---------------------+-------+--------------+------+ 
```
 

# Step 1: Make a jail

So, pretending that we're looking at this fresh, let's get going. The first thing you usually need to do is make a safe place to put this new software: a jail. As with all great software, OwnCloud is going to jail. We will use our fancy new ansible ready, Ports tree included, template and we will externalise the directory that holds the owncloud data (non database data).

```bash
iocage create -n owncloud01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -t ansible_template_v2 boot="on"
iocage fstab -a owncloud01 /mnt/vol01/apps/owncloud /var/www/owncloud/data nullfs rw 0 0
```

# Step 2: Dependency management

The next thing we have to do is install all the pre-requisite packages. There is a trick to this, as OwnCloud (and NextCloud) are large software projects that have a LOT of php dependencies. Some of them are optional and enable plugins ore functionality that is non-core, but some are mandatory for basic operation of the product. My preference would be to derive this list somehow, as I don't want to maintain a list of OwnCloud dependencies that will become out of date at the whim of a developer.

Vermaden over his [his blog](https://vermaden.wordpress.com/2018/04/04/nextcloud-13-on-freebsd/) describes how to use the ports system to list out the dependencies for any FreeBSD package using 'make' and the ports system. [^2]

```yaml

TASK [owncloud : debug] *****************************************
ok: [ioc-owncloud02@freenas.home] => {
    "packages_output.stdout_lines": [
        "mysql56-client", 
        "pecl-smbclient", 
        "php56", 
        "php56-bz2", 
        "php56-ctype", 
        "php56-curl", 
        "php56-dom", 
        "php56-fileinfo", 
        "php56-filter", 
        "php56-gd", 
        "php56-hash", 
        "php56-iconv", 
        "pecl-intl", 
        "php56-json", 
        "php56-mbstring", 
        "php56-pdo", 
        "php56-posix", 
        "php56-session", 
        "php56-simplexml", 
        "php56-xml", 
        "php56-xmlreader", 
        "php56-xmlwriter", 
        "php56-xsl", 
        "php56-wddx", 
        "php56-zip", 
        "php56-zlib", 
        "php56-exif", 
        "php56-ldap", 
        "php56-openssl", 
        "php56-pdo_mysql"
    ]
}


```

The listing above does 2 things:
1. Make sure the ports tree is checked out and up to date
2. Gets a list of package names out of the command **make -C /usr/ports/www/owncloud run-depends-list**

{% include important.html content="This couples us to the owncloud port. Is that a bad thing? I don't know right now but I think it's still better than keeping my own list of packages" %}

# Step 3 

The obligatory enablement of services:

```yaml
- name: Set SYSRC config
  shell: "sysrc {%raw%}{{item.key}}={{item.value}}{%endraw%}"
  with_dict: "{%raw%}{{ sysrc_config }}{%endraw%}"
  notify:
  - restart nginx
  - restart php-fpm

```

# Step 4 - Tune php and PHP-FPM

All of these steps are described on the owncloud 10 site [^3]. The summary steps for this installation:

1. Setup the php to nginx socket - /var/run/php-fpm.sock - in ```/usr/local/etc/php-fpm.conf```
2. Setup the ```env[PATH]``` and other in the __www__ section of ```/usr/local/etc/php-fpm.conf```. Following the instructions in  [^3], we are told to use a different file (```/usr/local/etc/php-fpm.d/www.conf```), but this file is not included by the php-fpm.conf file by default in FreeBSD.
3. Copy ```php.ini-production``` to ```php.ini```
4. Set the php opcache values as per [ramsdenj](https://ramsdenj.com/2017/06/05/nextcloud-in-a-jail-on-freebsd.html#opcache) [^4]
5. Set cgi.fix_pathinfo to 0 in the php.ini

# Step 5 - Download and install owncloud

```yaml

- name: download owncloud
  get_url: url=https://download.owncloud.org/community/owncloud-10.0.10.tar.bz2
           dest=/root/owncloud-10.0.10.tar.bz2
           sha256sum=a2efe484678c1659b9640ea247746a2174d77870d29c7d60abd565c20eb5aa84
  register: owncloud_downloaded

- name: install owncloud
  unarchive:
    src: /root/owncloud-10.0.10.tar.bz2
    dest: /usr/local/www
    remote_src: yes
  when: owncloud_downloaded is changed
  notify:
  - restart nginx
  - restart php-fpm

- name: set perms on owncloud dir
  file: path="{{item}}" state=directory recurse="true" owner=www group=www # mode='u=rwX,go=rX'
  with_items:
    - "/usr/local/www"
    - "/usr/local/etc/nginx/cert"

```

# Step 6 - Wire up the occ command

There are a few small things we can do to in order to make ```occ``` easier to access:

```yaml
- name: create the occ command
  template: src=occ.j2 dest=/usr/local/sbin/occ owner=root group=wheel mode='a+x'
```

Where this is what occ.j2 would look like:
```bash
#!/bin/sh
sudo -u www /usr/local/bin/php /usr/local/www/owncloud/occ "$@" 
```

# Step 7 - Configure Nginx

Much like the nextcloud config, the owncloud nginx config is pretty complicated. It is 168 lines long, and is mostly taken verbatim from the owncloud site [^5].

My ansible role modifies this slightly for my own SSL certs.

# Step 8 - Add in the cron script for ownclouds internal scheduling

```yaml

- name: Cron for cron.php
  cron:
    name: "Cron for cron.php"
    minute: "*/15"
    job: "sudo -u www /usr/local/bin/php /usr/local/www/owncloud/cron.php"
```


# Step 9 - Setup up redis and apcu

A production ready ownlcoud installation needs caching set up as per [these instructions](https://doc.owncloud.org/server/10.0/admin_manual/configuration/server/caching_configuration.html). [^6] These instructions advise you to use Redis as a distributed file lock and APCu as a memory cache. I landed up using that configuration, but mainly because I couldn't get redis working as a memory cache.

Ideally, I'd like to install one redis instance and have it server multiple owncloud/nextcloud instances. This means using a non-default db index. See this example in your config/config.php

```
'memcache.local' => '\OC\Memcache\APCu',

  'redis' => [
    'host' => 'redis01.home',
    'port' => 6379,
    'timeout' => 0,
    'password' => '',
    'dbindex' => 3
  ],
  'memcache.locking' => '\OC\Memcache\Redis',
#  'memcache.local' => '\\OC\\Memcache\\Redis', <-- couldn't get this working
  'filelocking.enabled' => 'true',


```



# Rabbit holes

This playbook gives you a very vanilla install of OwnCloud. Without a lot of digging, it's very easy to get sucked into some topics that are distracting.

{% include figure image_path="/assets/img/rabbithole.jpg" alt="Sorry, what?" class="image-small image-right" caption="A rabbit. Down a hole." %}

## 1. OCC

OwnCloud has a command line utility, that this playbook includes a small wrapper script for. This has immediately proved useful as it's about the only way that you can do an import into an owncloud account if you don't want to sync everything from a remote location (e.g. importing data from an old account)

But with OCC you can do an awful lot more than that, including doing the initial bootstrap (database and file system config), upgrades, set properties like where the log file lives, and a whole heap of other things.

The problem is that if you run OCC on FreeBSD installation you get a snarky message telling you to use Linux, and I can't find a means of getting around it (apart from looking into the source code). This has already cost me 2 evenings of flootin' with Google and my test jails trying almost everything under the sun, but to no avail. OCC from ansible will just have to wait.  

{% include figure image_path="/assets/img/owncloud_no_freebsd.jpg" alt="FreeBSD no thank you!" class="image-large image-centre" caption="No FreeBSD for you." %}

{% include tip.html content="A tantalising feature of OCC is that you can choose to have the output produced in yaml, json, formatted text or raw text. This is just BEGGING to be made into an ansible module." %}

## 2. OCC - more automation

You can set up more than just the initial database and user creation with OCC - you can do small jobs like set the background task scheduler to be cron e.g.

``` yaml
- name: Set backround cron
  shell: "/usr/local/sbin/occ background:cron"
```

However, this only works once the ownCloud instance is initialized. So although it's tempting to wire up some level of automation, it just won't work (not the first time at least).

## 3. PHP and Port version nightmares

As alluded to above, I spent a lot of time see-sawing between php7.1 and php5.6. This is mainly attributable to the way in which I told ansible to install the dependencies, which came from using a mismatching ports tree version.

## 4. Other Tuning

Thee is probably an endless about of tuning you could do to tune all the components of this setup e.g Postgres tuning, scaling out the redis instance and tuning it, tuning php and php-fpm, making sure all the various caching layers are functioning optimally, etc. 

I'm not going to delve into that now - I feel like this post is probably long enough aready, and there is scope in each aspect for some dedicated investigation.

## 5. Interesting reading

Danb35 is a very interesting all in one next cloud shell script that bootstraps the entire setup on one go: [https://github.com/danb35/freenas-iocage-nextcloud/blob/master/nextcloud-jail.sh](https://github.com/danb35/freenas-iocage-nextcloud/blob/master/nextcloud-jail.sh)


# Conclusion


This whole project took a long time: iteration one and two took several evenings of trial error. Looking back on them, there are some questionable things for example, perhaps building what amounts to a CI pipeline for a deprecated FreeBSD version. A wiser choice would have been to delay putting the effort in until I have access to a version of FreeBSD that is in support (out of the box). Another example would be to revisit the fixation getting the depencies out of the ports tree. That landed up sending me down a very frustrating rabbit hole of wrong versions.

But for the time being we have a working owncloud instance a working redis instance and I've learned an aweful lot. Bring on NextCloud!!!



[^1]: https://github.com/freebsd/freebsd-ports/tree/branches/2018Q3
[^2]: https://vermaden.wordpress.com/2018/04/04/nextcloud-13-on-freebsd/
[^3]: https://doc.owncloud.org/server/10.0/admin_manual/installation/configuration_notes_and_tips.html#php-fpm-tips-label
[^4]: https://ramsdenj.com/2017/06/05/nextcloud-in-a-jail-on-freebsd.html#opcache
[^5]: https://doc.owncloud.org/server/10.0/admin_manual/installation/nginx_configuration.html
[^6]: https://doc.owncloud.org/server/10.0/admin_manual/configuration/server/caching_configuration.html