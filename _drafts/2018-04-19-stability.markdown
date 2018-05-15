---
layout: post
title: Stability
date: 2018-04-19
description: Stability
img: stability.jpg # Add image post (optional)
tags: [nas,satacard] # add tag
toc: true
---

Our Merriam Webster word of the day today is stability: things have stayed up and non broken now for just over a week now. No sign if imminent failure. A couple of restarts have been survived.

Don't look now, but I think this NAS project could be drawing itself to a close.

Before we begin the process of a self flagellating retrospective of what I did wrong, what I did right and how much I spent trying to save money, there's one last job that has to happen.

Right now I have 6 drives: 5 configured in a RAIDZ array and 1 hanging on as a separate dataset (see previous blog posts for how this random setup organically came into being). I'd like to have a 6 drive array to have the largest possible size of fault tolerant storage. From what I have read, I need to basically start from scratch.

<figure class="image-large image-centre border">
<pre>
          +----------------+
          | +--+ +--+ +--+ |
          | |  | |  | |  | |
          | |4 | |4 | |4 | |
          | |  | |  | |  | |
          | +--+ +--+ +--+ |
          |                |
          | +--+ +--+      |      +--+
          | |  | |  |      |      |  |
          | |4 | |4 |      |      |4 |
          | |  | |  |      |      |  |
          | +--+ +--+      |      +--+
          +----------------+   /mnt/vol02
            /mnt/vol01

</pre>
<figcaption>The status quo</figcaption>
</figure>

<figure class="image-large image-centre border">
<pre>
            +----------------+
            | +--+ +--+ +--+ |
            | |  | |  | |  | |
            | |4 | |4 | |4 | |
            | |  | |  | |  | |
            | +--+ +--+ +--+ |
            |                |
            | +--+ +--+ +--+ |
            | |  | |  | |  | |
            | |4 | |4 | |4 | |
            | |  | |  | |  | |
            | +--+ +--+ +--+ |
            +----------------+
              /mnt/vol01

</pre>
<figcaption>The Desired final state</figcaption>
</figure>

So the great move iteration 3 needs to start. It makes me feel a little ill because I've already watched this movie before. Twice. I think this time I'm going to NOT do it over my excruciatingly slow network, even though I feel like I've learned a few tricks to take the pain out of the endless rsyncing. This time I'm going to use my hard drive toaster and the relatively blistering speed of USB.

Another aspect of this is that for the first time ever I can see how bad my digital hoarding actually is. For example, I always suspected that I had around 1TB of photos, but I always had something confusing it a bit - either an archive of really old stuff on a drive in drawer, or
