---
layout: post
title: "A small jekyll update showcase"
date: 2018-10-09
description: I've added some stuff!!
img: format_update.png 
tags: [jekyll] # add tag
toc: False
---

# Alerts 

Something that's been on the back burner for a while.

{% include note.html content="This is my note. All the content I type here is treated as a single paragraph." %}

```bash
{%raw%}{% include note.html 
    content="This is my note. All the content I type here is 
    treated as a single paragraph." %}{% endraw%}
```

{% include tip.html content="This is my tip. Never pat a burning goat." %}

```bash
{%raw%}{% include tip.html 
    content="This is my tip. Never pat a burning goat." %}{% endraw%}
```

{% include important.html content="This is my important thing. Please pay attention!." %}

```bash
{%raw%}{% include important.html 
    content="This is my important thing. Please pay attention!" %}{% endraw%}
```

{% include warning.html content="Warning. Here be dragons." %}

```bash
{%raw%}{% include warning.html 
    content="Warning. Here be dragons." %}{% endraw%}
```

# Callouts

Callouts have six different styles:
* primary
* default
* danger
* success
* info
* warning


{% include callout.html content="Primary" type="primary" %}

{% include callout.html content="Default" type="default" %}

{% include callout.html content="Danger" type="danger" %}

{% include callout.html content="Success" type="success" %}

{% include callout.html content="Info" type="info" %}

{% include callout.html content="Warning" type="warning" %}

__Example:__

```bash
{%raw%}{% include callout.html content="Warning" 
            type="warning" %}{% endraw%}
```

If you feel like quoting war and peace, 

{% include callout.html content="There's a passage I got memorized. ***Ezekiel 25:17***. 

\"The path of the righteous man is beset on all sides by the inequities of the selfish and the tyranny of evil men. Blessed is he who, in the name of charity and good will, shepherds the weak through the valley of the darkness, for he is truly his brother's keeper and the finder of lost children. And I will strike down upon thee with great vengeance and furious anger those who attempt to poison and destroy My brothers. And you will know I am the Lord when I lay My vengeance upon you.\" <br/>
 
 Now... I been sayin' that shit for years. And if you ever heard it, that meant your ass. You'd be dead right now. I never gave much thought to what it meant. I just thought it was a cold-blooded thing to say to a motherfucker before I popped a cap in his ass. But I saw some shit this mornin' made me think twice. See, now I'm thinking: maybe it means you're the evil man. And I'm the righteous man. And Mr. 9mm here... he's the shepherd protecting my righteous ass in the valley of darkness. Or it could mean you're the righteous man and I'm the shepherd and it's the world that's evil and selfish. And I'd like that. But that shit ain't the truth. The truth is you're the weak. And I'm the tyranny of evil men. But I'm tryin', Ringo. I'm tryin' real hard to be the shepherd." type="primary" %}


