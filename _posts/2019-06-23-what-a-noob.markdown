---
layout: post
title: "Noob error. No backups"
date: 2019-06-23
description: I can believe I didn't make a backup
img: stupid.jpg # Add image post (optional)
tags: [backups,whoops] # add tag
toc: true
---

# What a noob

I have lost some blog posts. In May this year I tooks a couple of days off to go to London with my wife. We visited friends, and managed to spend a day or two having some quality time together. One of the days she had to go a conference, and I took the opportunity write up some  thoughts that had been rattling around my head. I was experimenting with Ubuntu 19.04 on my MacBook Pro at the time (an experiment which had limited success for me as one of my deal breaker apps wasn't available: Citrix Receiver - only available on the LTS versions of Ubuntu).


{% include figure image_path="/assets/img/london_illy.jpg" class="image-medium image-right" caption="A glorious coffee emporium" %}


For whatever reason, I didn't do one of the frenetic '__git commit  -a && git push__'  operations that I normally do when I'm writing  blog posts. I usually use the '_drafts' folder when I'm writing so it's 100% safe to do a frequent push without accidentally posting something that isn't ready for reading. It might have been the fantastic Italian coffee, or the less than fantastic muddy London air, or the fact that I had to stop halfway through to go find a place that would document scans for me (charging me £15 for scanning 10 pages - ouch). It could have been all of the above. The fact of the matter is that I didn't save the article I was  writing and now, with  an apocolyptic installation of Ubuntu 18.04, the fantastic series of BTRFS articles that I  had drafted are all gone.


The irony of all this is that part of what I was writing about was a way to send snapshots of BTRFS to the Freenas Box (ZFS), and I was quite close to getting that working quite nicely (either using an ISCI export or some fancy footwork on the client side to make an S3/Minio bucket useful as a snapshot receiver. )

And WORSE than that still, I usually set up DejaDup to run on my home directory to guard against disasters like this. And for whatever reason, this time I didn't.

So, woe is me. 

I can't bring myself to dive back into BTRFS right now. I'll have to come up  with something else to write about...

