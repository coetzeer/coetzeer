---
layout: post
title: "Installing redis to make Owncloud faster"
date: 2018-10-09
description: Trials in installing Postgres
img: foo.jpg # Add image post (optional)
tags: [redis] # add tag
toc: true
---

{% include note.html content="This is my note. All the content I type here is treated as a single paragraph." %}

{% include tip.html content="This is my tip. All the content I type here is treated as a single paragraph." %}

{% include important.html content="This is my important thing. All the content I type here is treated as a single paragraph." %}

{% include warning.html content="This is my warning thing. All the content I type here is treated as a single paragraph." %}


{% include callout.html content="**Important information**: This is my callout. It has a border on the left whose color you define by passing a type parameter. I typically use this style of callout when I have more information that I want to share, often spanning multiple paragraphs. <br/><br/>Here I am starting a new paragraph, because I have lots of information to share. You may wonder why I'm using line breaks instead of paragraph tags. This is because Kramdown processes the Markdown here as a span rather than a div (for whatever reason). Be grateful that you can be using Markdown at all inside of HTML. That's usually not allowed in Markdown syntax, but it's allowed here." type="primary" %}


{% include callout.html content="Primary" type="primary" %}

{% include callout.html content="Default" type="default" %}

{% include callout.html content="Danger" type="danger" %}

{% include callout.html content="Success" type="success" %}

{% include callout.html content="Info" type="info" %}

{% include callout.html content="Warning" type="warning" %}








Rutorrent
----------------------------------------------------

iocage create -n rutorrent dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/rutorrent/root/etc/pkg/FreeBSD.conf
iocage fstab -a rutorrent /mnt/vol01/Downloads/whatbox /mnt/downloads/bittorrent/incomplete nullfs rw 0 0
iocage exec torrent1 'pkg install -y python27'



Torrent1
------------------------------------------
iocage create -n torrent1 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/torrent1/root/etc/pkg/FreeBSD.conf
iocage fstab -a torrent1 /mnt/vol01/Downloads/general /mnt/downloads nullfs rw 0 0
iocage exec torrent1 'pkg install -y python27'


Torrent2
------------------------------------------
iocage create -n torrent2 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/torrent2/root/etc/pkg/FreeBSD.conf
iocage fstab -a torrent2 /mnt/vol01/Downloads/movies /mnt/downloads nullfs rw 0 0
iocage exec torrent2 'pkg install -y python27'


Torrent3
------------------------------------------
iocage create -n torrent3 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/torrent3/root/etc/pkg/FreeBSD.conf
iocage exec torrent3 'pkg install -y python27'
iocage fstab -a torrent3 /mnt/vol01/Downloads/series /mnt/downloads nullfs rw 0 0


Torrent4
------------------------------------------
iocage create -n torrent4 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/torrent4/root/etc/pkg/FreeBSD.conf
iocage exec torrent4 'pkg install -y python27'
iocage fstab -a torrent4 /mnt/vol01/Downloads/music /mnt/downloads nullfs rw 0 0


Postgres
----------------------
/mnt/vol01/apps/postgres/ /mnt/vol01/iocage/jails/postgres/root/var/db/postgres nullfs rw 0 0
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/postgres/root/etc/pkg/FreeBSD.conf



Redis
------------------
iocage create -n redis01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/redis01/root/etc/pkg/FreeBSD.conf



OwnCloud
---------------------
iocage create -n owncloud01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
iocage fstab -a owncloud01 /mnt/vol01/apps/owncloud /var/www/owncloud/data nullfs rw 0 0
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/owncloud01/root/etc/pkg/FreeBSD.conf


https://linoxide.com/storage/setup-owncloud-nginx-ssl-freebsd-11/

pkg install nginx
pkg install php70-pgsql php70-pdo_pgsql
pkg install php70 php70-mysqli php70-xml php70-gd php70-curl php70-zlib php70-zip php70-hash php70-tokenizer php70-extensions php70-pdo_mysql php70-openssl php70-gmp php70-ldap php70-exif php70-fileinfo php70-mbstring php70-bcmath php70-bz2 php70-mcrypt php70-pecl-APCu php70-intl
sudo cp /usr/local/etc/php.ini-production /usr/local/etc/php.ini
cgi.fix_pathinfo=0

rehash
sysrc nginx_enable=yes php_fpm_enable=yes

nano /usr/local/etc/php-fpm.d/www.conf

make -C /usr/ports/www/nextcloud run-depends-list | while read I; do echo pkg install -y $( basename $I );done


Plex
-----------------------------
iocage create -n plex01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on

iocage fstab -a plex01 /mnt/vol01/Downloads /mnt/downloads nullfs ro 0 0
iocage fstab -a plex01 /mnt/vol01/Media/Movies /mnt/media/Movies nullfs ro 0 0
iocage fstab -a plex01 /mnt/vol01/Media/Series /mnt/media/Series nullfs ro 0 0
iocage fstab -a plex01 /mnt/vol01/Media/Audiobooks /mnt/media/Audiobooks nullfs ro 0 0
iocage fstab -a plex01 /mnt/vol01/Media/Music /mnt/media/Music nullfs ro 0 0

iocage fstab -a plex01 /mnt/vol01/apps/plex /mnt/working nullfs rw 0 0
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/plex01/root/etc/pkg/FreeBSD.conf



Gogs
-----------------------------
iocage create -n gogs dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/gogs/root/etc/pkg/FreeBSD.conf
iocage fstab -a gogs /mnt/vol01/apps/gogs /var/db/gogs/repositories nullfs rw 0 0


NextCloud
----------------------------------------

iocage create -n nextcloud01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
iocage fstab -a nextcloud01 /mnt/vol01/apps/nextcloud /var/www/nextcloud/data nullfs rw 0 0
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/nextcloud01/root/etc/pkg/FreeBSD.conf

https://medium.com/@vermaden/nextcloud-13-on-freebsd-95cef1fad291


influxdb
----------------------------------------

iocage create -n influxdb dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
iocage fstab -a influxdb /mnt/vol01/apps/influxdb /var/db/influxdb nullfs rw 0 0
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/influxdb/root/etc/pkg/FreeBSD.conf



grafana
----------------------------------------

iocage create -n grafana01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/grafana01/root/etc/pkg/FreeBSD.conf


nginx
----------------------------------------

iocage create -n nginx01 dhcp=on allow_sysvipc=1 bpf=yes vnet=on -r11.1-RELEASE boot=on
sed -i .back "s/quarterly/release_1/" /mnt/vol01/iocage/jails/nginx01/root/etc/pkg/FreeBSD.conf
