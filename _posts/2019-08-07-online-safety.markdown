---
layout: post
title: "Data protection: keeping your mind safe from bullies"
date: 2019-08-07
description: Stop people pigeon holing you, and trying to change your mind
img: data_protection.jpg
tags: [opinion,FaceBook] # add tag
toc: true
---



I watched a Netflix Documentary this week called ['The Great Hack'](https://www.rottentomatoes.com/m/the_great_hack), and it was a well timed reminder of degree to which your online data can be harvested and then used nefariously. In a nutshell, the writers of movie break down the secret sauce behind the success of Cambridge Analytica. The film does a really good job of describing the discomfort and fear that has been scratching and gnawing away in the dark recesses of my mind for many years now. I have never been able to articulate it. I have always ground to a halt in conversations at 'they take your data (what data?)', or 'they track you ( I've got nothing to hide)' and 'it's not opt-in (it's free!)'.

This movie is mandatory watching for anyone with a FaceBook account. Actually, I'll take it further: this movie is mandatory watching for anyone who has access to a working internet connection. 

# The Three Steps

I'm not going spoil the plot, but my takeaway from the film was the Modus Operandi of Cambridge Analytica. Certainly with their 2016 US election campaign, they seemed to follow 3 steps that allowed them sway just the right number of people in the right states (the film quotes that the election was swung by 70 000 in just 3 states).

## Step 1: steal the data

Send people what seems like a harmless survey or personality test. The first thing you have to do with any of these is 'grant them access' to your profile.  Most people just do this without thinking; I have to admit that I have done this myself. 

But take a second to think about it: When you give access to your profile, __they will be able to see what you see__. That includes all the photos you've put up, all the comments, status updates, pokes and emojis and more. If __you__ can see your friends, then __they_ can see your friends too. So you've not only opened up your profile, but you opened up anyone on your friend list too, without their consent or knowledge.


## Step 2: Reduce to data points

Once they have access to your account, they can set about the process of harvesting the data. This is where real magic happens. They use special algorithms to reduce the content of your profile, the text, pictures, location data, likes, history and friendship network into data points. 

This is almost a bit like the trick that Sherlock Holmes does when deduces someone's life history from one glance at their shoes, but it's done with specific intent: in this case, the data scientists in Cambridge Analytica where trying to find out if you or the people around you were on the fence in terms of an election candidate. They were trying to find the 'persuadables'.

## Step 3: Start pumping the bile

Once they've categorized you, they can then turn back the same platform that they use to harvest your data. That's right: the FaceBook pipe goes both way. This time they're using it to deliver you personalized content, the sole purpose of which is to change your mind, to manipulate you. They spend huge amounts of money to design propaganda that is specialized almost down to the individual, that they then feed to via your FaceBook news feed.

The big wakeup call for me was that this is not just 'data theft' or 'opt in'. This is blatant manipulation of my thoughts, and not on billboards or in magazines where advertisers have been trying to do this for years; it's on the same platform that I use when I talk to my parents or see pictures of my nephew. Right next to photos of our family holiday is a bit of internet, a video or a picture, who's sole intent is to steal 30 seconds of my life and change my mind about something.

# So what do we do about it

There are several things we can do. Some are easy. Some are a bit harder. Some are a bit abstract. But for the record, here they are.


1. Lose your FaceBook account. Or if you can't lose it, try and do the following:
  - use a good password (not a dictionary word, a name or a birthday). Good, strong passwords don't have to hard to remember, long streams of rubbish. Simply glue 4 or 5 short words together with a separator e.g.
        - ``` $couch$graduatetax$president$halibut$ ```
        - ``` 2suppport2marine2clay2warn2serious2 ```
        - use the [https://xkpasswd.net/s/](xkcd password generator)
  - go and see what has been 'connected' to your account and disconnect it. That means FaceBook apps, other websites, surveys, games, etc. Remove all of it. Their presence on your FaceBook profile means that you have granted them at least some access to your data, so they represent a gaping, yawning chasm in your personal data security.

{% include figure image_path="https://imgs.xkcd.com/comics/password_strength.png" alt="https://xkcd.com/936/" class="image-large image-centre" caption="Why you should use a strong password" %}

{:start="2"}

2. Don't use FaceBook (or google or twitter) to 'login' to other websites. When you do this, you join FaceBook (or Google or whatever) to that site. They become connected using your login credentials and your profile. They exchange data and possibly grant access to information on one or both of the sites. Most importantly you're adding data-points to your 'profile'. Anyone interrogating your data now knows, in addition to your FaceBook login, that perhaps you signed up to Brain.FM, which puts you in a certain demographic. __Do not surrender this this information.__ It's not FaceBook's business to know what sites you log into. 

{% include figure image_path="/assets/img/sign_in_with_google.png" alt="Yes. Let's JOIN the bug data thieving networks together! What could possibly go wrong." class="image-large image-centre" caption="Just use a username and password" %}

{:start="3"}

3. Use a password manager like LastPass or one of [these](https://www.google.com/search?client=firefox-b-d&channel=trow&q=password+manager+comparison) to generate a secure password for every site.

4. Use 2 factor authentication. Even hard passwords can be cracked, if not by brute force then by social engineering or simply by reverse engineering from another compromised source (e.g. guessing your GMail password from you FaceBook password). Two factor authentication means logging into a website or app requires 2 things: something you __have__ and something you __know__. Most of the time, the thing you know is as password. The thing you HAVE could be: a pin code that has been SMSed to you, or a token that's generated on your phone (using an App like Google Authenticator). Two factor authentication means that even if your password is somehow breached, you have the safety blanket of the OTHER factor preventing people from logging in and stealing your data.

{% include figure image_path="https://imgs.xkcd.com/comics/security.png" alt="https://xkcd.com/538/" class="image-medium image-centre" caption="The simplest kind of social engineeering" %}

{:start="5"}


5. Try NOT to use your fingerprint or any other biometric data as the 'thing you have'. You only have 10 fingerprints. If they get compromised (scanned, stolen, reproduced somehow), you can't generate any more.

6. Don't use Google Chrome. Google has a vested interested in monetizing your data. They are an advertising company with shareholders and a bottom line, which means the balance sheet will ALWAYS win when it comes to ethical choices around data freedom. Use Mozilla FireFox instead.

7. Install Ghostery - a browser plugin that attempts to stop web pages downloading images and other assets that allow third parties (people who aren't you or the website author) to be notified everytime you load a page. A good example of an image like this is the 'FaceBook Like' image. Whenever you download this image, FaceBook can record when, from which website and who downloaded the button (by public IP address). Even worse, if you're logged into FaceBook or accessing it from the same computer or network, FaceBook can even pin down exactly WHO are you. Ghostery prevents this by simply stopping the image being loaded in the first place. While you're at it, install Adblock, Adblock plus and Privacy Badger too. The more the merrier.

8. Install browser containers - this is a browser plugin that prevents your FaceBook or Twitter or Google or Amazon session from leaking between webpages. See he above example for why this is a good idea. Note: while this prevents session level leakage, browser containers are windows on your computer, the traffic is still going in and out via your wifi card, and out through your internet connection. The potential for network level tracking is still high.

9. Switch on 'Do Not Track' in your browser. See [here](https://www.digitalcitizen.life/enable-do-not-track-dnt-chrome-firefox-edge-opera-internet-explorer) for some instructions on how to do this. Do Not Track is a setting in browsers that websites may or may not choose to honour.  Setting it to True means that you DO NOT give your consent to be tracked, thereby instructing a website to disable it's tracking functionality. As I mentioned, whether or not this setting is honoured is debatable, but setting to True at least makes your desire known if there is every any question after the fact. Browsers default this to FALSE - so this is something you have do set yourself.

10. Don't get bogged down in cookies or javascript or things that used to be an issue on websites. Cookies, javascript, CSS, AJAX - these are all things that modern websites need in order to function. See this great Quora answer [Getting rid of cookies is like getting rid of knives and then trying to cut a steak with a spoon](https://www.quora.com/Why-are-website-cookies-potentially-dangerous-to-a-computers-security) . 

11. Be interested and informed. These 'pursuadables' were not gullible or stupid. There were regular people going about their lives, but they allowed themselves to be manipulated because they didn't have strong opinions of their own. Try not to be a persuadable. Don't just read news from a FaceBook feed. Read newspapers; read different news papers. Even with editorial oversight, newspapers and the companies that produce them usual have some sort of agenda. Understand what it is, and bear that in mind when you're reading the content. Consider boths sides of any story. Don't let people, whether they're on 'your side' or not, tell you what to think.  


# More reading
Here is some more reading for anyone who's interested:
1. [The Motherboard guide to not getting hacked onlines](https://www.vice.com/en_us/article/d3devm/motherboard-guide-to-not-getting-hacked-online-safety-guide) - this is a good article full of very practical advice. It's centred more around general security rather than data security in particular, but it's a good read non the less.
2. [David McWilliams discussing the evolution of Journalism and Technology with Tom Friedman](https://open.spotify.com/episode/78td8vElM4Jd70gM1ik3GR) - Some wonderful takes on social networking and it's role in our world. 
3. [ Brexit the uncivil war]( https://en.wikipedia.org/wiki/Brexit:_The_Uncivil_War ) - some interesting insight into how the campaign for Brexit was run.