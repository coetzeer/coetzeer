---
layout: post
title: "Re-installing software Part III : Standing on the shoulders of giants"
date: 2018-10-10
description: Starting from scratch doesn't need to be completely from scratch
img: giant.jpg # Add image post (optional)
tags: [ansible, software install] # add tag
toc: true
---

When looking down the barrel of an awful lot of work, my natural inclination is to hunt around for things that will save me effort: either people who have done it before or some easy path to automation. Here are some if the resources that I've used to help me set up my new NAS box, the giants who's shoulders I've stood on:

# 1. Ansible via ssh jail

The following few paragraphs document my path to discovering a git repository that was the culmination of a about 6 months worth of feverish internet searching. In the end, I stumbled across it completely by accident when looking for something else.

The challenge is: how do I run ansible in a jail? Typically ansible connects to target hosts via ssh, but ssh daemons are really just overhead for jails, especially if you're running something lighter than the ssh daemon in the jail.

## Built in jail plugins

```bash
+------------+                      +-------------+-------------+
|            +--------------------> |             |             |
| ansible-   |                      | SSH Daemon  | Jail command|
| playbook   |                      |             |             |
| foo.yml    |                      +-------------+             |
|            |                      |                           |
+------------+                      | +---------+  +---------+  |
                                    | |         |  |         |  |
 Dev machine                        | |Owncloud |  | Postgres|  |
 ansible installed                  | |         |  |         |  |
 here                               | +---------+  +---------+  |
                                    |                           |
                                    | +---------+  +---------+  |
                                    | |         |  |         |  |
                                    | |Nextcloud|  | GOGs    |  |
                                    | |         |  |         |  |
                                    | +---------+  +---------+  |
                                    |                           |
                                    | +---------+  +---------+  |
                                    | |Plex     |  |Deluge   |  |
                                    | |         |  |         |  |
                                    | +---------+  +---------+  |
                                    +---------------------------+

                                       FreeNAS Jail host

```


So how do you connect? There are some cryptic mentions of jail connection plugins in the ansible documentation here: [https://docs.ansible.com/ansible/2.5/plugins/connection/jail.html](https://docs.ansible.com/ansible/2.5/plugins/connection/jail.html). That documentation might as well be written in Japanese for all the good it did me. [Ollivier Robert](https://www.keltia.net/howtos/jail-mgmt-with-ansible/) seemed to have a bit more joy, but attempting to use this method reveals 2 problems: firstly it's meant for execution on the host i.e. you run *ansible-playbook* on the same machine that's hosting the jails and secondly, it's using *jls* and *jexec* - two commands that don't work with newer iocage system. Digging into the [source](https://github.com/ansible/ansible/blob/afd8b97fb1a6196e04a302ef74300d3cad6518f3/lib/ansible/plugins/connection/jail.py) confirms this.

But wait - just next to this file in the github repo is a file called [*iocage.py*](https://github.com/ansible/ansible/blob/a8d4bf86421d151d8df7132e8e87d04b6662f45a/lib/ansible/plugins/connection/iocage.py) - and it looks like prayers have been answered. But alas, the plague of completely undescriptive documentation continues as does the requirement that playbook be executed on the jail host. The jail connection command is *iocage* so we're a step closer, but still not really able to use any of it.

## ssh-jail
Some more digging throws up seemingly exactly what we want: [ssh-jail](https://github.com/austinhyde/ansible-sshjail). This seems not to suffer from the issues that the other connections plugins suffer from:
1. It works over ssh
2. It seems to use commands that work with IOCage (at least for the time being)
3. Is being maintained
4. Has some reasonable examples

I spent an evening setting up a playbook according to the instructions in the Readme.md, but to no avail. Ansible seemed to die before making any kind of connection, and as it tends to do in it's own frustrating way, didn't offer up much in terms of error messages.

It was late, so I gave up.

{% include figure image_path="/assets/img/tired.jpg" alt="It's tired and I'm late." class="image-medium image-centre" caption="No more ansible. Nap time." %}


## Enter Mr Ingemann

About 6 months after I threw in the towel, I stumbled across this repo: https://github.com/andsens/freenas-jailconfig. Pure. Gold.

Not only does it have good roles for setting up software on FreeBSD (specifically FreeNAS) jails, it is using ssh-jail. So I took that as a queue to give things another go.

It failed at the same point:

```
TODO: show failure example.
```

A trick to try to get ansible to offer up some of it's secrets is to run it with the *-vvv* flag. Doing this shows this:

```
TODO: show more fail  
```

Frustrating no? With a bit of digging, I found that the jail names returned but the command *jls -q jid name host.hostname path* returns something like this:

```bash
root@freenas:~ # jls -q jid name host.hostname path
1 ioc-owncloud01 owncloud01 /mnt/vol01/iocage/jails/owncloud01/root
2 ioc-postgres postgres /mnt/vol01/iocage/jails/postgres/root
3 ioc-grafana01 grafana01 /mnt/vol01/iocage/jails/grafana01/root
```

So iocage jails need a special name in the inventory file: *ioc-postgres@freenas* rather than *postgres@freenas*

The ansible.cfg file needed a few modifications too:

```ini
[defaults]
# convenience so you do don't have to specify these over and over again.
inventory = inventories/hosts
private_key_file = /home/raymondcoetzee/.ssh/id_rsa
remote_user = coetzeer

# set this up bundle sshjail in the connection_plugins folder in your repo.
connection_plugins = connection_plugins

[ssh_connection]
ssh_args=-o ControlMaster=auto -o ControlPersist=60s -o ForwardAgent=yes
pipelining=True

[privilege_escalation]
# again - mostly for convenience so you don't have to specify these repeatedly at the command line
become=True
become_method=su
become_user=root
become_ask_pass=True
```

Once I figured out the naming and the various config options, I was 'suckin diesel'.

{% include figure image_path="/assets/img/suckingdiesel.webp" alt="In the words of the immortal Adrian Dunbar." class="image-medium image-centre" caption="What he said." %}

# 2. New FreeNAS plugin infrastructure

A quick browse of the github repo that contains all the new iocage github plugin descriptors shows just how easy it is to install the software provided by the jails.

{% include note.html
    content="I wasn't able to use these plugins as they stand as they needed the FreeBSD 11.2 jail, and I'm stuck on 11.1 until the FreeNAS version based on FreeBSD 11.2 gets out of Beta" %}

E.g. for Plex, the plugin file is here: [__https://github.com/freenas/iocage-ix-plugins/blob/master/plexmediaserver.json__](https://github.com/freenas/iocage-ix-plugins/blob/master/plexmediaserver.json). This file instructs IO cage to install the package *multimedia/plexmediaserver* and then delegate further installation steps described in [__https://github.com/freenas/iocage-plugin-plexmediaserver.git__](https://github.com/freenas/iocage-plugin-plexmediaserver.git)

These descriptors and accompanying files give you good instructions on how to install the software that would otherwise be installed as plugins.

# 3. The FreeNAS forums

The FreeNAS forums are full of good instructions, for example this post: [__https://forums.freenas.org/index.php?resources/fn11-1-iocage-jails-plex-tautulli-sonarr-radarr-lidarr-jackett-ombi-transmission-organizr.58/__](https://forums.freenas.org/index.php?resources/fn11-1-iocage-jails-plex-tautulli-sonarr-radarr-lidarr-jackett-ombi-transmission-organizr.58/). It goes into detail on installing software that I'm interested in: transmission with openvpn with an ipfw 'kill switch'

A bonus from this post was discovering 'Organizr' (previously know as tautulli). This immediately went onto the software wish list as I never remember random ports that things come up on.


{% include figure image_path="/assets/img/freenas_forum.png" alt="Where all the knowledge is." class="image-medium image-centre" caption="The FreeNAS forums: go there" %}


# 4. This blog

Of course, part of the reason I post content on this site is that it servers as an 'aide memoire' for all the random jobs that I do on computers. Being a 'devops-er', it shall of course go beyond simple documenting: all of the steps on the path of the 'Great Reinstall' will hopefully all end up as ansible playbooks and roles, up on github.

Watch this space.
