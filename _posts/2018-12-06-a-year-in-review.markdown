---
layout: post
title: "2018 - A year in review"
date: 2018-12-06
description: Looking back on the projects of 2018
img: 2018_journey.jpg # Add image post (optional)
tags: [philosophy, retrospective, opinion] # add tag
toc: true
---

Looking back on 2018: where do you even start? It feels like it's been a hard year and despite that, we've done a lot. This is not a blog about my personal life and not even my work life, so there's a big mask over scope of this article that precludes any ventures into soft, fuzzy emotional stuff, as well as any cold prickly real life stuff. So if you read the title thinking tha this would be a rambling outpouring of feelings, you can breathe a sigh of relief!

So let's keep it light.

# Here. Is a good place to start.

Right here, this blog. The first post was on 2 Feb 2018, and there are 5 pages of sleep inducing articles on (mostly) FreeNAS. I feel like that has been an accomplishment. Looking back on the goals of the blog, I don't think I've done a bad job although as usual it's easy to be critical with myself.

The [original goals were]({{ site.url }}{{ site.baseurl }}/docs/post_ideas.html):

1. Document the projects that I spend time on.  
2. Write something.  
3. Get stuff out of my head.  
4. Keep notes.  

The blog has done it's job for all of those points. There are some areas that I think I can improve on:


  {% include figure image_path="/assets/img/notepad-691250_1920.jpg" alt="https://pixabay.com/en/users/Free-Photos-242387/" class="image-small image-right" caption="Make writing interesting again" %}

* __Write more often__  
I get rusty if weeks go by without writing anything. Then I have to start at the beginning and re-learn the small, soft skills that go into making an article more than just word vomit. Even small articles with simple thoughts or learnings would help keep the juices flowing.
* __Review articles__  
  I really hate reading things that I've just written. Which is ironic. Partially, though, because I don't really expect anyone, least of all myself to read all the stuff I write. The problem is that a freshly dropped article is full of spelling mistakes, atrocious sentences, and rambly thoughts that don't go anywhere. There is a direct correlation between quality and the time spent on reviewing and fine tuning. I need to find a better balance of time/quality.
* __Make things more interesting__  
 I feel myself slipping into a pattern. I make an effort to put in code snippets and pictures, and I've recently added callouts to highlight big ideas, but these all get very same-ey after a while. I need to find a mechanism (apart from sparkling prose of course) to make articles easy to consume.

# The NAS is up and running

My little FreeNAS box is up and running. It has done stellar work in serving Plex content, downloading stuff, and, it should go without saying, being a safe place to stash all our important data.

There was a very recent catastrophe where the hard drive that I used as the OS drive died, and I had to a quick replacement job, but that wasn't FreeNAS's fault - that was me scrimping on the components. 

{% include figure image_path="/assets/img/alan_jude.jpg" alt="The man has a point" class="image-medium image-right" caption="Alan Jude says: Your disks are plotting to destroy you." %}

I think we can declare the NAS project a success. It has replaced all the old crufty NAS hardware that's lying around. It has taken several attempts - my wife would say that I've made a meal out of it - but it's up and running. 

Now - it IS on older hardware. So there is logical limit to how long it will carry on being useful. But I suspect that the most imminent danger is running out of disk space again. What can I say: Mame ROMS take up a lot space.

# I've become BSD convert

In the process of using and getting my head around FreeNAS, I've become a complete advocate of FreeBSD and it's super sane and useful jail system. I love it it so much now that I have plans in my head for a second FreeBSD only server to remove some of the non-NAS load from my FreeNAS, and perhaps a FreeBSD based desktop.

Watch out [Bargain Hardware](http://www.bargainhardware.co.uk/) - there will be some orders coming your way soon... [^1]

# Learning - Linux Academy and Pluralsight

{% include figure image_path="/assets/img/linuxacademy.jpg" alt="This is a smart looking penguin." class="image-small image-right" caption="Linux Academy - learn things." %}

One of my guilty pleasures is that I listen to a great many prodcasts about the Linux desktop - as opposed to linux as an entity in an enterprise. I'm a silent FOSS nerd. I voraciously consume all of the Jupiter Broadcasting shows, Late Night Linux, The Ubuntu Podcast, The Bug Report, you name it - I probably listen to it.

One spin off from this is that the advertising on the Jupiter Broadcasting shows has worked on me. I purchased a discounted Linux Academy subscription recently and have been working through the vast amount of content that they have. 

So far so good.

# I joined a LUG!

A new podcast kicked off this year, hosted and run by the folks from the Dublin Linux User Group! It's called Linux Lads and is awesome, you should listen to it, and the LUG visits so far have been enjoyable. I'm looking forward to getting to know the Lads (and the occasional Lass) better in 2019! 

{% include note.html 
    content="
    
    Linux Lads - [https://linuxlads.com/](https://linuxlads.com/)
    
    The Dublin Lug - [https://dublinlinux.org/](https://dublinlinux.org/)
    " %}
    

# Onto 2019. What next.

Looking at my mini [projects list]({{ site.url }}{{ site.baseurl }}/docs/post_ideas.html) I have an ever growing list of things that demand investigation and attention. I have a habit of changing tack frequently and taking violent swerves down previously undocumented rabbit holes, so making a list of things to achieve for 2019 might be an exercise in futility. But at the very least it makes for interesting reading in 12 months.

* __Finish off the NAS services__  
There are two main things there: 
1. Finish off the series of articles that I have waiting in the drafts folder here that describe all the work I did in setting up the jails to be just so. I've done all the work, I really just need to document it it all. 
2. The second thing is to be critical about the things that irritate me about the current setup e.g. the way I have IPFW set up, there's a race condition when I start an OpenVPN jail. I also have to log onto a VPN client jail to see if the VPN is actually working. I'd like to setup consul to do some reporting on the state of these services, and to act as a supervisor/ config repo for them.

{% include figure image_path="/assets/img/kubernetes.png" alt="Get used to shipping metaphores." class="image-small image-right" caption="Whooaa there sailer!" %}

* __The big K__  
I haven't documented it here yet, but 'The Great Project for 2019' [^2] has already kicked off. I was lucky enough to go to [O'Really Velocity in London](https://conferences.oreilly.com/velocity/vl-eu-2018) this year, and the experience kind of set my head on fire. So I have this itch to become a Kubernetes expert now. The reason why this has turned into project is that I have scooped up some reasonably priced Dell R610's and I now have a mini-datacentre humming away in my study. Some sub-goals of this initiative:
  * Become au fait with installing kubernetes clusters. At first it seems like a bewildering set of components that go into setting up this infrastructure that's meant to keep things simple, but there's really a finite set of core components and a cottage industry of things that plug in around the edges. 
  
  * Run all the things - including awkard things that need storage like zookeeper and kafka. Figure out how to do that easily and repeatably. 

* __The big clean__  
So my study has turned into warm, noisy dumping ground of project left overs and household paperwork. I am employing the help of [Marie Kondo](https://konmari.com/) to help me bring joy back into my physical surroundings. This has to happen. I am finding the clutter harder and harder to deal with, mostly because it's my fault.

{% include figure image_path="/assets/img/marie_kondo.jpg" alt="Even putting her picture here makes the blog seem neater" class="image-small image-centre" caption="The queen of declutter." %}

# In conclusion

Goodbye 2018. Bring on 2019. I'm ready for you.

[^1]: Hashtag __getinbeforebrexit__
[^2]: Yes, I know it's still 2018...