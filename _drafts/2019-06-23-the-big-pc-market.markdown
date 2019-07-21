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

Over the last 5 years or so, Desktop PC's, and to some degree laptops, have seen a drop in popularity and sales, due mainly to rise of the SmartPhone and the Tablet [^1]. I remember being very sceptical of the Ipad when it first arrived in shops. 'Look!', I said. 'Someone has made an IPhone that doesn't fit in my pocket!' Boy was I wrong. I've been wrong before, but this was a whole new leage of wrongness. The popularity of the IPad is something that I completely failed to predict, and to this day completely fail to understand. See the graph below (Ipads vs Ipods) - they show IPad sales outstripping the IPhone + the IPod almost since it's release.

{% include figure image_path="/assets/img/statistic_id253725_apple-product--iphone-ipad-ipod--sales-comparison-2009-2018-by-quarter.png" alt="https://www.statista.com/statistics/253725/iphone-ipad-and-ipod-sales-comparison/" class="image-small image-left" caption="Ipads vs Ipods (Statista)" %}


Over the past few years I've seen lots of anecdotal evidence that the IPad induced death of the PC. Regular PC users e.g. People who skype with elderly parents a lot, understand the allure of the IPad as the computing device because it much easier to use. It doesn't need space on a desk, and can be used for a whole series of use cases such surfing the web, email, online shopping, video conferencing, etc. They've replaced textbooks in schools and even the heavy, leather, book laden wheely bags that lawyers used to cart around behind them. Children on long haul flights get given skinny tablets loaded with films and episodes of Thomas the Tank Engine to keep them entertained. There is no doubt that phones and tablets are the easiest, most convenient and most cost effective way to CONSUME digital media, from netflix to websites to ebooks. 

{% include figure image_path="/assets/img/statistic_id272595_laptop-pc-tablet-sales-statistics-2010-2023.png" alt="https://www.statista.com/statistics/272595/global-shipments-forecast-for-tablets-laptops-and-desktop-pcs/" class="image-small image-right" caption="Laptops vs Tablets (Statista)" %}

# The problem of producing

However, creating or producing content on these devices is a challenge. That's not to say it's impossible: IPads can be converted into amazing musical instruments and attaching a bluetooth keyboard to a tablet can make it much easier to finish a homework assignment and draft robust emails. But there are certain types of work where the desktop form factor still has the edge; three examples of which are Gaming, Media production and Development. There are more than likely many more. These are just the ones that I have dabbled in.

## Games all day long

Why don't gamers like tablets? Maybe that's not the right question. Maybe a better question is: What is appealing about a desktop form factor for gaming? A handy parallel discussion is the decades old debate about whether consoles or PC's are better for gaming.  

The argument for PC's has always been flexibility. PC's are modular. It's one of the things that made them so popular over the more bespoke personal computing hardware devices of the  late 80's. If your screen is too small, you can get a bigger one, provided it had a VGA plug on it. If your processor is too slow, you could get a faster one. You can get a better graphics card, more RAM, spice up your case with water cooling and fancy LED's. PC's have attracted the sort of gamer that is enthralled by the endless ways in which you customize and upgrade and tweak, much to the absolute horror of the console gaming enthusiasts. 

Console gamers tend to look down their noses at the ability to add RAM or upgrade a graphics card because that fiddling ultimately detracts from time that could be spent gaming. Up until very recently, a gaming console was the a snapshot of the best gaming hardware available, all packed into a neat little box. Any game certified for a particular console was, more than likely, going to run pretty well. The room for doubt that's created when you custom build your gaming rig is completely removed with a console.

This is going to change though: the days of being able download a game or buy a game on disc are fast disappearing; a new era of game streaming services is being ushered in, and will completely change the way that we interact with gaming hardware and games themselves. Consoles, being the extension of the Gaming Platform Vendor into your home, are the logical place where we'll see these changes first. There will be no need for 'the best gaming hardware available' any more as  all the heavy lifting of rendering will be done in a geographically convenient datacentre, and then streamed straight to your screen. The only thing you'll need is a good internet connection and a comfortable controller.

Why go down this rabbit hole? What do consoles and game streaming services have to do with tablets? Well, once the requirement for high-end hardware is gone, mobile devices are back in the picture. Once gaming has been reduced to 'a service', mobile devices like phones and tablets are perfect devices to consume that service.  

So yes, gaming as use case is holding on, but I predict that in the not too distant future, owning your own gaming hardware will be considered 'retro.'   

## Content producers

Media production has traditionally been the demesne of apple mac toting creative professional. Whether it's a photographer plugging his Nikon camera into Lightroom and Photoshop or an animator spending hours illustrating a story frame by frame or a film maker editing digital reels of movies using the latest Adobe offerings, the large, bright, comfortable workstations provided by Apple have been the go to form factor for ages. 

The movement however in this space is that a lot of the heavy lifting in terms of disk space and CPU for rendering is all moving to cloud services. 

## Developers

That leaves Developers. My assertion in this article is that Developers are the last bastion of PC users that are going to stick around, and that they're leading a significant change in Desktop usage paradigm - significant enough that Dell and Lenovo have picked up on it and changed their laptop offerings to match this new pattern.

So, what makes me say this? Here are my reasons:
1. Everything's going to the cloud. Slowly but surely, the world is solving it's problems by putting services into the cloud. In this article we've already talked about Game Streaming and online render farms as ways that are currently used to mitigate the need to own expensive, power hungary kit. And that is happening everywhere in almost every sphere, from Game development and creative media software to financial services, medical services and government services. It's just the beginning, and yes, it might change, and yes, there will always be holdouts, but the cloud is where the money is at right now. Why is this important? Well, 2 reasons: firstly, 'the cloud' as we understand it is a complicated software stack running on someone else's computer. That software stack has to be maintained and enhanced. Secondly you have to write your company's amazing idea as software and push it out onto that stack (and maintain it). Both of those activities are development activities. The Cloud has ushered a new era where lone developers are achieving heights that were previously only possible if you had a massive budget and a whole army of hardware professionals. Developers are where the money is at.
2. You can't bootstrap development into the cloud. You can do a job at trying, and a lot of it is, but there certain things that will always be better to do with a local processor e.g. write hardware drivers or experiment with an API that's costs money if you use the online version. You might be able to put a LOT of development stuff into the cloud: online IDE's, remote compiling, etc. At some point though, a keyboard and clear screen become very important tools to get the job done. What this means is that Development as a task, unlike gaming or video editing, will always require a workstation. 
3. Developers are the bridge to the cloud that product managers and marketeers use to make their ideas real.  
4. The evolution of development tools over the last couple of years has been highly biased towards open source tool chains. Python, Ruby, Rust, Go - all of these run on Windows, Mac and Linux, but if you want to run it at scale, you need to be able to think about 100's of computers and not 1 or 2. This is where Linux and the tools that have built up around it have left Windows and Mac behind. Containers have changed the way we think about deploying workloads: they're easy to package and run predictably because the exact thing that will run in production you can run, debug and develop on Desktop. And this has meant that if your OS can't do containers, it's not going to be used by a developer. This has changed the bias away from MacOS and Windows and closer towards Linux as the development platform of choice. 

In a nutsheel, what does this mean? Developers don't account for the majority of desktop users? They don't buy the biggest machines and they don't buy the most machines. They are however an important small percentage:  They're not going anywhere, they're staying on the desktop,there will be more of them as the cloud market grows, and they want linux.









[^1]: See this article from [Gartner - Gartner finds PC sales doldrums continued in 2018](https://techcrunch.com/2019/01/11/gartner-finds-pc-sales-doldrums-continued-in-2018/)