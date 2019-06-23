---
layout: post
title: "Prediction: Developers are the next big PC market"
date: 2019-06-23
description: 'Prediction: Developers are the next big PC market'
img: oldcomputer.jpg 
tags: [gaming,opinion] # add tag
toc: true
---

# The next big market

Over the last 5 years or so, Desktop PC's and to some degree laptops, have seen a drop in popularity and sales, due mainly to rise of the SmartPhone and the Tablet [^1]. I remember being very sceptical of the Ipad when it first arrived in shops, thinking 'Look! Someone has made an IPhone that doesn't fit in my pocket!' Boy was I wrong. I've been wrong before, but the popularity of the IPad is something that I also consider with chagrin. See the graph below (Ipads vs Ipods) that shows the IPad outstripping the IPhone + the IPod almost since it's release.

{% include figure image_path="/assets/img/statistic_id253725_apple-product--iphone-ipad-ipod--sales-comparison-2009-2018-by-quarter.png" alt="https://www.statista.com/statistics/253725/iphone-ipad-and-ipod-sales-comparison/" class="image-small image-left" caption="Ipads vs Ipods (Statista)" %}


I've seen lots of anecdotal evidence of the IPad induced death of the PC. People who skype with elderly parents a lot prefer to use a IPad as the computing device because it much easier to use, doesn't need space on a desk, and can be used for other things like surfing the web and email in the same no mess no fuss manner. There is no doubt that phones and tablets are the easiest, most convenient and most cost effective way to CONSUME digital media, from netflix to websites to ebooks. 


{% include figure image_path="/assets/img/statistic_id272595_laptop-pc-tablet-sales-statistics-2010-2023.png" alt="https://www.statista.com/statistics/272595/global-shipments-forecast-for-tablets-laptops-and-desktop-pcs/" class="image-small image-right" caption="Laptops vs Tablets (Statista)" %}

However, creating or producing content on these devices has always been a challenge. That's not to say it's impossible: IPads can be converted into amazing musical instruments and attaching a bluetooth keyboard to a tablet can make it much easier to finish a homework assignment and draft robust emails. But, there are certain types of work where the desktop form factor still has the edge; Gaming, Media production and Development are 3 of these, although I wholeheartedly admit that there are probably more.

Gaming is interesting one as the things that make the form factor useful are combination of spec limitations of mobile CPUs and GPUs and the limitations of the user interface. Gamers require kit with a good spec in order to play games: good graphics cards, and fast CPUs, both of which draw lots of power and require fans to keep cool. They also use high DPI mouse's and like to play their games on big screens. Now, the CPU and GPU question is probably just a matter of time. At some point in the future, low energy ARM chips will have the same processing power as the beefy Core i9's that intel is pumping out, and mobile platforms will be able to render out 4K (more in the future?) 60FPS stream without breaking making the device warm. Actually, with the introduction of game streaming service like Google Stadia, you don't even need to CPUs and GPUs - you just need a good internet connection. 

The second problem of the user interface is a bigger one, but also just a matter of time before it disappears. With portable screens like Google Glass or whatever the next generation of VR head looks like, who's to say that a person couldn't have a fully immersive game of Call of Duty on the bus to work. 

Media production has traditionally been the demesne of apple mac toting creative professional. Whether it's a photographer pluggin his Nikon into Lightroom and Photoshop or a animator spending hours illustrating a story frame by frame or film maker editing digital reels of movies using the latest Adobe offerings, the large, bright, comfortable workstations provided by Apple have been the go to form factor for ages. The mobile world has similar barriers in the space and similar answers: people love the Macs because they're powerful and pretty. However a great deal of rendering and post production is now moving to the cloud. And innovations that reshape the what a 'Creative Proffesional's' workstation should look like will likely change in the future too: expensive sound boards, mixing desks and editing hardware could very easily become 'apps'.

That leaves Developers. My assertion in this article is that Developers are the last bastion of PC users that are going to stick around, and that they're leading a significant change in Desktop usage paradigm - significant enough that Dell and Lenovo have picked up on it and changed their laptop offerings to match this new pattern.

So, what makes me say this? Here are my reasons:
1. Everything's go to the cloud. Slowly but surely, the world is solving it's problems by putting jobs into the cloud. In this article we've already talked about Game Streaming and online render farms as ways that are currently used to mitigate the need to own expensive, power hungary kit. And that is happening everywhere in almost every sphere, from Game development and creative media software to financial services, medical services and government services. It's just the beginning, and yes, it might change, and yes, there will always be holdouts, but the cloud is where the money is at right now. Why is this important? Well, 2 reasons: firstly, 'the cloud' as we understand it is a complicated software stack running on someone else's computer. That software stack has to be maintained and enhanced. Secondly you have to write your company's amazing idea as software and push it out onto that stack (and maintain it). Both of those activities are development activities. The Cloud has ushered a new era where lone developers are achieving heights that were previously only possible if you had a massive budget and a whole army of hardware professionals. Developers are where the money is at.
2. You can't bootstrap development into the cloud. You can do a job at trying, and a lot of it is, but there certain things that will always be better to do with a local processor e.g. write hardware drivers or experiment with an API that's costs money if you use the online version. You might be able to put a LOT of development stuff into the cloud: online IDE's, remote compiling, etc. At some point though, a keyboard and clear screen become very important tools to get the job done. What this means is that Development as a task, unlike gaming or video editing, will always require a workstation. 
3. Developers are the bridge to the cloud that product managers and marketeers use to make their ideas real.  
4. The evolution of development tools over the last couple of years has been highly biased towards open source tool chains. Python, Ruby, Rust, Go - all of these run on Windows, Mac and Linux, but if you want to run it at scale, you need to be able to think about 100's of computers and not 1 or 2. This is where Linux and the tools that have built up around it have left Windows and Mac behind. Containers have changed the way we think about deploying workloads: they're easy to package and run predictably because the exact thing that will run in production you can run, debug and develop on Desktop. And this has meant that if your OS can't do containers, it's not going to be used by a developer. This has changed the bias away from MacOS and Windows and closer towards Linux as the development platform of choice. 

In a nutsheel, what does this mean? Developers don't account for the majority of desktop users? They don't buy the biggest machines and they don't buy the most machines. They are however an important small percentage:  They're not going anywhere, they're staying on the desktop,there will be more of them as the cloud market grows, and they want linux.









[^1]: See this article from [Gartner](https://techcrunch.com/2019/01/11/gartner-finds-pc-sales-doldrums-continued-in-2018/)