---
layout: post
title: "Jekyll and Markdown cheat sheet"
img: selection.jpg
tags: [jekyll] # add tag
toc: True
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


# Footnotes

Footnotes come in two halfs: first the bit that goes inline in your text:

```bash
A long rambling sentence [^1]
```

The second bit is the reference at the bottom of your page:

```bash
[^1]: Some sort of expansion on the rambling sentence.
```


This renders something like this:

<p>Watch out <a href="http://www.bargainhardware.co.uk/">Bargain Hardware</a> - there will be some orders coming your way soon… <sup id="fnref:1"><a href="#fn:1" class="footnote">1</a></sup></p>


And then down at the bottom of the page:

<p>Hashtag <strong>getinbeforebrexit</strong>&nbsp;<a href="#fnref:1" class="reversefootnote">↩</a></p>

# A side note

If you'd like some more flexibility over the alerts and callouts described above, try a side note box:

```html
<div class="notebox">
     <b>A small sidebar on accessModes</b>
     
     <p>
     The access modes govern the cardinality of PV to node:
 
     <ul>
     <li>ReadWriteOnce – the volume can be mounted as read-write by a single node</li>
     <li>ReadOnlyMany – the volume can be mounted read-only by many nodes</li>
     <li>ReadWriteMany – the volume can be mounted as read-write by many nodes</li>
     </ul>
     
     In the CLI, the access modes are abbreviated to:
     
     <ul>
     <li>RWO - ReadWriteOnce</li>
     <li>ROX - ReadOnlyMany</li>
     <li>RWX - ReadWriteMany</li>
     </ul>
     </p>
</div>

```

This looks like:

<div class="notebox">
     <b>A small sidebar on accessModes</b>
     
     <p>
     The access modes govern the cardinality of PV to node:
 
     <ul>
     <li>ReadWriteOnce – the volume can be mounted as read-write by a single node</li>
     <li>ReadOnlyMany – the volume can be mounted read-only by many nodes</li>
     <li>ReadWriteMany – the volume can be mounted as read-write by many nodes</li>
     </ul>
     
     In the CLI, the access modes are abbreviated to:
     
     <ul>
     <li>RWO - ReadWriteOnce</li>
     <li>ROX - ReadOnlyMany</li>
     <li>RWX - ReadWriteMany</li>
     </ul>
     </p>
</div>

# Images

There is an image include html snippet that comes with some syntactic sugar:
1. Image borders are standard as per the preference of yours truly
2. Clicking on an images give you a modal light box
3. There's a default 'gallery' created of each image on the page. You get this for free.


```
{%raw%}{% include figure image_path="/assets/img/linuxacademy.jpg" \ 
alt="This is a smart looking penguin." \
class="image-small image-centre" \
caption="Linux Academy" %}{%endraw%}

```

This looks like:

{% include figure image_path="/assets/img/linuxacademy.jpg" alt="This is a smart looking penguin." class="image-small  image-centre" caption="Linux Academy" %}

# Mermaid diagrams

Mermaid.js is a marvellous javascript library that allows you to render a markup language to svg diagrams on your web page.

See many more lovely examples here: https://mermaid-js.github.io/mermaid/

Note: there is a [mermaid jekyll plugin](https://github.com/jasonbellamy/jekyll-mermaid) that gives you a slightly more elegant integration 

```
{%raw%}{% include mermaid.html content="
graph LR
    A[Freenas] -- publishing graphite --> D
    B(Raspberry Pi) -- publishing collectd --> D
    C[Telegraph] -- publishing native Influx format --> D
    D[(InfluxDB)] --  native influx queries --> E((Grafana));
" %}{%endraw%}

```

This will render: 

{% include mermaid.html content="
graph LR
    A[Freenas] -- publishing graphite --> D
    B(Raspberry Pi) -- publishing collectd --> D
    C[Telegraph] -- publishing native Influx format --> D
    D[(InfluxDB)] --  native influx queries --> E((Grafana));
" %}

# General Cheat sheet

[A bit of everthing Jekyll related](https://devhints.io/jekyll)