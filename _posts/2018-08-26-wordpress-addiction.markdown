---
layout: post
title: Why Static content sites rock
date: 2018-08-26
description: and how to overcome your WordPress addiction.
img: addicted_to_WordPress.jpg # Add image post (optional)
tags: [internet, holidays, philosophy ] # add tag
toc: true
---

As I mentioned in my previous post, my Dad approached me about setting up a site for his online stamp business. My advice to him was to firstly find some sites that he liked and had the functionality that he wanted, and then, once he'd showed me a few, I told him that I thought Jekyll was a good way to go. Right now, he's not interested in a multifaceted e-commerce vehicle. He would like something to display his stock nicely; the e-commerce side can be delegated happen on E-bay or PayPal.

Why a generated site like Jekyll? While my Dad's requirements are simple and his idea doesn't need a whole lot of expensive supporting infrastructure, a statically generated site like my blog seemed to fit the bill. I haven't done a whole heap of requirements analysis, but suffice it to say that I've had some experience fiddling around with WordPress and Joomla and Drupal and Ghost and the flavours of hosting solution that use that paradigm. I.e. find a place that will host the right combination of database (usually Mysql), web server (used to be Apache but now more likely Nginx), and Php (whatever version we're up to now). I have started looking into creating and managing a website of this ilk at least 6 times for my own purposes either as an online portfolio or as a a CV or blog. The problems are many and varied:

## 1. Hosting yourself

My day job is installing software that other folks have written, configuring it and bootstrapping it with monitoring to make sure that the whole thing stays upright. I could do the same with a WordPress site, and admittedly the internet is overflowing with instructions, ansible playbooks and docker containers that get you up and running quickly. The problem, as I always like to say, is 'who get's phone call at 3 in the morning'? If you've crafted your own server to run your own website, then the answer is YOU. Now, admittedly, this is not going to be a massive problem if all you're doing is hosting a few pages for your CV, BUT all the money that you've spent on hosting has been wasted if the 30 seconds that a potential employer takes to navigate to your website is trashed because you haven't rotated a log file or something.

Also, what happens if you're actually on the leading wave of the next big internet phenomenon and your little boutique server starts creaking under the wait of all the eager internet zombies? Well, you'll have 2 options: 1) quickly learn how to scale out your database and web server which is about as quickly learning Portuguese 2) quickly buy some hosting and learn how to move your data across. Again, this is a first world problem, but there are a million reasons to grow or to move, and at some point you'll have to do it.

## 2. Using a SaaS offering

I hate it when people say SaaS, or IaaS or whatever the next one is. But that's what this is: a specially crafted service that offers one thing: WordPress. Or Drupal. Or whatever. But you don't get a shell log in and you can't access the database even if you know if one is there. This is not your computer, it's some one else's.

And that is fine, if you can get yourself over that particular OCD hump. You get a working site that is guaranteed to handle X number of requests a second, hold Y Megabytes of data and cost you Z dollars a month. As you ratchet up X and Y, Z goes up too. The important thing is that X and Y can actually grow elastically, with no pain required from you except of the financial variety.

Also, most of the SaaS offerings offer an uptime guarantee, which means that a someone other than you get's the 3 am phone call, which is important.

## 3. Learning the framework

Nothing worthwhile in life comes for free, and the same can be said for your website. While WordPress gives you a reasonable website out of the box, at some point you're going to have to engage with it's innards to add new content and to customise the site to your liking. That means spending some time trawling the internet looking for themes that you like, and maybe paying for it once you've found it. That means getting the first couple of posts REALLY REALLY wrong as you learn the do's and don'ts of the WordPress publishing interface. That means a lot of fiddling, and unless you pay someone to administer your site, you can't get around the fiddling. Fiddling is how you learn.

## 4. Upgrading

Every couple of weeks some enterprising folks find security holes in the web glue that holds a WordPress site together. With the best intentions, it takes work to stay secure. Then, while you're patching the latest version of WordPress, PHP or Mysql, the next feature gets rolled in. Sometimes this is a good thing, because all of a sudden you've benefitted from the cumulative community wisdom of a series of open source projects. Woo hoo! Maybe things are just quicker or maybe you have access to new fonts or a formatting system. Unlike security, you can ride the feature wave for free.

However, sometimes 'features' come with side effects. Usually they involve things that are a little less standard than out-of-box WordPress for example the theme that you just bought. Sometimes the breakage is small and you won't notice it. Sometimes it's catastrophic and your site could be down until the good folks from the open source community (i.e. regular joes like you and me that get tired of the bleating and say things like 'this can't be THAT hard') or the vendor that sold you the theme fixes it. One would hope that either of those options would be fast, but you're really at their mercy. You could of course rollback, which could be easy to do if you've got a SaaS offering, but enters you into a world of pain if you've rolled your own stack.

In short, it's upgrading these hosting frameworks is Russian Roulette - 5 out of 6 times you'll be fine. The other times are a doozy though.

## 5. Keeping up with the Jones's

The internet moves at a distressingly fast pace. Any website, no matter how wonderful it is a release time, is going to need some work if it's to stay up to date with the design fads and fashion trends in online tech today. These could be things like the every decreasing acceptance of plaintext http or the requirement to make sites 'responsive' (i.e. so that they work on a plethora of mobile devices). How much of this stuff affects you depends a lot on which framework you've decided to hitch your wagon to. The likes of WordPress tend to stay up to date, I would say because there is enough momentum in the user base to provide incentives to developers and hosting companies to keep their users happy and paying.

The more you depart from the mainstream however, the more you leave yourself open to code rot and decrepitude.  Again, that might be the right choice for you. New shiney things come with risks, not the least of which they're new and shiney, which for some people is a major problem. Old and crufty has it's place in the world.

# Why Jekyll is the answer

For simple websites, blogs and portfolio sites, a site that is generated at deployment time is an awesome solution. Why?

## 1. No executables in the cloud

Instead of having complexity on a hand rolled or SaaS offering, the thing that lives in the cloud to serve your website is SUPER simple. It's just simple HTML. There is almost not infrastructure to be at risk: no cache layer or database or executable. Just documents.

Yes, a skilled hacker could find a way to kill a web server with the right http request, but that is the ONLY bit of infrastructure that you need to keep you site afloat. In comparison, a completely unskilled hacker could do quite a bit of damage to a WordPress site that's been left unpatched.

## 2. All the fiddling is local

You can do 99% of the writing of your site offline. This is important for 2 reasons: 1) if you want to do some serious content creation, you DON'T need an internet connection, which means you can quite effectively free yourself from distraction and 2) there is one less thing that can go wrong. CTRL-S actually saves your work onto a local disk - there is no intervening web layer that needs to be up and running in order to keep your many hours of toil from being wasted.

## 3. Simplicity is key

There is SOME complexity involved with putting together your Jekyll site. There's a config.yml, which you really only need to fiddle with when you're getting your site up and running in the beginning. You might have to resort to some HTML or Javascript if you can't get a page 100% right, but those times are few and far between. The rest of the time you're writing Markdown in your favourite editor. Now, for some people this might be a problem, but for my father, who spent a good many years drafting work using Multimate on a XT PC, Markdown should be a cinch.

One of the best things about the Jekyll is that while you're writing a post, you're not actually using any framework at all. WordPress constantly requires you to do WordPress stuff. You're always publishing drafts or interacting with the 'Gallery' or any other of strange tasks in the admin console. You're never just writing your post; you can never isolate yourself from the browser page, or the text box or the side bar.

Jekyll however, requires you to write a markdown file. How you choose to do this is up to you. You can make it full of distractions of you can at simple and as clean as you like.
