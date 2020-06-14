---
layout: post
title: "One Line Wonder: Enable cgroups vs for Docker on Fedora 31+"
date: 2020-06-06
img: oneliner.jpg 
tags: [fedora,docker,cgroups] # add tag
toc: true
---

The fedora project switched for cgroups v2 from version 31. This broke docker. In order to get docker back, you have to tell the kernel to please use cgroups v1. 

```
grubby --update-kernel=ALL --args="systemd.unified_cgroup_hierarchy=0"
```