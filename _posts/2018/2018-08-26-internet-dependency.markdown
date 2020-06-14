---
layout: post
title: Dependant on the internet
date: 2018-08-26
description: How come we're so dependant on the internet.
img: dependant_on_the_internet.jpg # Add image post (optional)
tags: [internet, holidays, philosophy ] # add tag
toc: true
---

I am on holidays here in Carvoeiro, a beautiful, hot, beachy part of Portugal which is the completed checklist of everything I want from a holiday. I'm surrounded by my family. The secada whispering wilderness of this magical place is working the kinks out of my soul. I haven't a want in the world, apart from a little voice in the back of my mind saying 'why the hell don't you live in a place like this if you love it so much?'.

More about that voice in another blog post.

The problem that I have right now is that my Dad mentioned in passing that he'd like to set up a web site to act as simple online stamp shop, and this has set into motion 3 things that are disturbing the effect that the secada's are having:

__1. A sudden interest in Jekyll Gallery templates__

A lot of the sites that my Dad has showed me fit the static site paradigm quite well. They don't need fancy search functionality (although Jekyll can do that to a degree), there's nothing seriously sensitive about the information on display (so it can live in a public github repo) and all of the e-commerce functionality is usually delegated to eBay and PayPal. Provided you have a reasonable site map and navigation links, most of the site is very gallery-like or portfolio-esq: there are scans of stamps and other philatelic paraphernalia that need to be laid out in a sensible way.

__2. A sudden interest in Hugo as an alternative to Jekyll__

I always challenge my choices, which is a good thing and a bad thing. Obviously it leads to a lot of malcontent for no reason other than an alternative exists, but on the plus side, very occasionally, during one of the long and tormented sojourns into doubtfulness, I find something really inspiring.

And so it is with Hugo, which a Go implementation of the sort of functionality that drives Jekyll. I found out about Hugo through the BSD Now podcast when Alan Jude mentioned they were converting their site away from Jekyll because of issues with the ever shifting sands of Ruby Gems dependencies, and grok that completely. Every time I do a (brew __\|__ apt __\|__ yum __\|__ dnf __\|__ pkg) update and one minor thing changes, you could be looking at a half an our of headscratching while trying to figure out what dependency you installed to get a particular gem working, and all of that effort just to get back to where you were before you did the update.

So I thought, before I dive head long into a project that could be alive for many a year, let's see if I can make my life (and my Dad's life) a bit easier.

__3. A sudden and manic need to justify the static site choice__

Again, when the doubtfulness demons creep into your mind in the dark of night and start vomiting on the nice clean sheets of certainty, it's imperative to be armed with good information. So before I plant a stake in the ground too firmly regarding advice for my Dad's site, I thought it a good idea to revisit some of the reasons why a static html site was a good idea and if those reasons still held true. This discussion grew some serious legs, so I split it off into a separate entry here: TODO figure out how to do a link.

## The problem statement

So here's the scene: I have all of these ideas buzzing around my head like a beehive in a hurricane. So I do what any techie worth his salt would do: pull out the laptop that you've shipped to Portugal (to deal with situations exactly such as these), crack open a freedom loving browser and start 'googling' (or a FOSS equivalent thereof). It's at this point that I understand the general sense of unease that has been pervading our Portuguese lodgings for the past two days: the internet is shite.

I'd been putting it down to family politics, the heat or residual fear from having to drive on the wrong side of the road. But no: this is the real thing: browser fatigue. People are snapping it at each other because Whatsapp is taking forever to download photos. Everyone is stuck in a pit of ignorance because google and tripadvisor are out of action. We can't even drive anywhere because we failed to cache some maps on our phones before we left civilisation, so we're doomed to just drive around and be lost. It's my own personal 'Back to the future', and this time it's South Africa in the early 2000's when broadband was something that was 'overseas' (like Mars Bars and Danny Devito) and you were infinitely familiar dial up modem sounds.

And compared to all that I have an additional challenge: all of the mind hornets need a working internet connection to order to be subdued. Not only an http/https connection, but I'll be cloning repos of git, so port 22 is going to get it's legs stretched too. And what makes the mind hornets even more agitated is that I actually have a little time to DO this experimentation now, BECAUSE I'M ON HOLIDAY!!!

At this point, I'm 4 days in and I've found no workable solution. There's a pizza place up the road that has a good connection, but they're starting to get suspicious of me hiding in the shade behind their shop. The marvellous EU has allowed for use of cellular data out of my normal data bundle, so I've attempted to tether to my phone for what at this point feels like contraband, but it's useless to me because even at 1 bar of 3g reception, the connection is so slow as to be unusable.

## How did we get here

So, coming back to the near paradise that I find myself in: how did we ever do anything before the internet? Before there were iPhones and Facebook, what was in our minds? What did the mind hornets have to buzz about?

I'm guessing there were more books to start with. Books for entertainment (not kindles), map books (not satnavs and google maps), tour guide books (not trip advisor), magazines from the airport and newspapers with tv guides in them. There was a lot more strategic reading, done a lot more in advance (in successful cases - in unsuccessful cases there was a lot more asking for directions).

I'm guessing things were a slower and maybe a bit less frenetic. As a techie person that tends towards some sort mind hornet, back then it probably would have been an obsession with electronics or planes or cars or boats. There would have been a well thumbed catalogue that got dragged to the beach and back. There would have been a notebook with ideas or drawings.

For me the anxiety of where we find ourselves today and the fascination with the past comes from a fear that we've lost things in our fast paced digital lives: we've sacrificed something for __perceived__ convenience and speed. I'm not pointing the finger at anything in particular, it's more a feeling. I __feel__ like I am personally more creative when I don't have an internet connection. I'm forced to think internally, to make use of the resources that I have at hand, which are, right now quite significant: I have my mind and I have an expensive laptop - the combination of those two things should provide almost limitless possibilities. Yet I find myself getting irritated because I can't download a website theme.

I'm aware of it, which is, I suppose the first step to doing something about it. I'm attempting to describe it, to tease it out, to work out it's shape. For now, this internet-less moment of introspection has, I think been good for me, and there is a good time note step 1: spend less time connected to the internet.

Enough introspection for now.
