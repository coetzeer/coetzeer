---
layout: post
title: "Project Backlog"
img: backlog.jpg
tags: [jekyll] # add tag
toc: True
---


## Projects

## Project firewall - home networking overkill
This is in a state of evolution. I wanted to create a safe place on the network for 3rd party devices that I didn't trust such as a Blink or Ring.com camera. So far this has included
- upgrading the house wireless lan setup to Unifi gear (AP + Switches)
- Evaluate and install firewalls
- switch the virgin media router over to IP4

Still left on the todo list:
- figure out to architect the network, including getting the router to work in 'modem mode'.
- Set up OpenVPN ingress options, to allow me to connect remotely
- Consider installing some 'infrastructure' type services e.g.   
    * Landscape - because Ubuntu. I've had a first stab at this and the problem with landscape is that it's a bit of a resource beast. It's a Java based program that slowly fills up it's max heap size, to the point where running it in a small VM has eventually lead to death via OOM.
    * Spacewalk and open idm - because Fedora. It seems to be a bit more of a complete solution for managing a large fleet of servers, and it looks like you have use the upstream component projects (Forman, Katello, etc) if you want to run it free. e.g. see Katello install instructions [here](https://theforeman.org/plugins/katello/nightly/installation/index.html).
    * AWX for ansible runs
    * Reasonable CA management - everything with a web ui needs SSL these days. So doing this properly seems like something that would pay off. I keep on losing the root CA's I generate and then I have to start again.
    * lets encrypt
	  * ansible module
	  * [http://docs.ansible.com/ansible/latest/letsencrypt_module.html](http://docs.ansible.com/ansible/latest/letsencrypt_module.html). [an example using freenas/NextCloud/letsencrupt] (https://forums.freenas.org/index.php?threads/freenas-11-NextCloud-owncloud-letsencrypt-ssl-tls.57797/)

## Circling back to freenas

I had a bad hardware failure on the NAS back in March, I need to document that. I have some new kit in the Freenas server now and I need to circle back onto that. I need to have a better look at what I did right and what I did wrong. There are some things I could do better, such as running a more reliable postgres installation (backups + a cname for example). 

As I was stuck on 11.1 on Freenas for so long, the services I've been hosting there are woefully out of date - Owncloud + nextcloud, syncthing, grafana and InfluxDB. There's a backlog of old projects that I wanted to get done such as a remote logging server. I've been really reliant on InfluxDB and Grafana and I need to put some thought into how I use that data and how to make it more resistant to failure. I have a second NAS box that's running older disks that I'm a bit less worried about failing. I need to move all the IO heavy operations over to that machine leaving the freenas box with a ligher IO workload. This will hopefully prolong the life of the drives a bit. I need a better backup solution in general. In terms monitoring, I need to finish off and package the hdd drive temp scripts that I made for capture drive temperatures in freenas. 

* monero mining
* Add drive temp to netdata

* Use consul to keep tabs on services in my house.

## The next evolution of the blog

Consider an upgrade to Huge - especially the Academic plugin. This is at once exciting and painful as the thought of porting over all the content I've created on the Jekyll site makes me want to cry. But the blog format is 2 years old a this point and needs some love. Also, I'd like to make one of these for myself [https://github.com/RyanFitzgerald/devportfolio](https://github.com/RyanFitzgerald/devportfolio). I need to re-evaluate how I start writing blog articles as I've stalled for the last couple of months (almost years at this point). I've created a bar to article writing that is too high.

## Pi project backlog

The Magic mirror project has been dragging on for over a year now, and it needs some love. I have a cut down version that I'd like to implement just using a spare monitor in my study - just a dashboard with weather and news and a nice picture. I'd like to figure out how to use a pi a BMC over serial connection - successful serial connections still elude me. I would like to finish off whatever I had originally though of for retro games on the pi. Looking back I've always gotten stuck on the games that needed a PC interface like old DOS games or old Amiga and AtariST games. I think I should focus in a particular form factor such as Mame or old gaming consoles, just to get something that's complete over the line. I think one of the things that I've battled with the most in the retro-gaming builds is reproducability - how do it over and over again, without copying gigs of content over the network, getting the interface to look nice with all the game art etc, and preserving any tweaks that I might introduce such as customised menues, tweaks to OS, etc. 

## IOT and Home Assistant

I've started messing around with ESP8366, with a view to creating some remote temperature sensors around the house. So far this has stalled.

