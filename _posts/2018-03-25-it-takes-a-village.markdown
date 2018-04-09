---
layout: post
title: It takes a village to build a NAS
date: 2018-03-25
description: It takes a village to build a NAS
img: village.jpg # Add image post (optional)
tags: [nas,heatsink,satacard] # add tag
toc: true
---

During a rant at work about my NAS related woes, a friend of mine offered me a CPU and cooler upgrade and to assist me with both to boot. Given the fact that I was in spasms of rage about the state of the project, what with the silly Intel cooler clips giving up the ghost and how the crazy SiL3114 chipset kept on rewriting the MBR on the one poor drive connected to it (spasms I tell you), I decided that I would take him up on his kind offer.

So I did my best to clean my wiring, removed any evidence of blatant stupidity and packed the case, some tools and a power cord into the car and headed over to his house this weekend.

The current setup is:
* Case : [Fractal Design Node 304](http://www.fractal-design.com/home/product/cases/node-series/node-304-black)
* Motherboard :  [H61N-D2V S1155 Intel H61 DDR3 mITX](http://download.gigabyte.cn/FileList/manual/mb_manual_ga-h61n-d2v_e.pdf)
* Memory :  16GB (8GBx2) Ballistix Sport DDR3 1600MHz Cas9 1.5v Kit
* Processor : [Core i5-2400](http://ark.intel.com/products/52207/Intel-Core-i5-2400-Processor-6M-Cache-up-to-3_40-GHz)
* CPU Cooler :  Akasa Low-Profile Cooler for LGA1155
* PSU : Seasonic M12II Bronze Evo Edition 520 Watt
* Hard drives: 5x 4Tb WD Red, 1x 2G Samsung
* Sata card: SiL3114 chipset 4 port sata

The proposed upgrade is
* CPU to [Intel(R) Core(TM) i7-3770 CPU @ 3.40GHz](https://ark.intel.com/products/65719/Intel-Core-i7-3770-Processor-8M-Cache-up-to-3_90-GHz)
* CPU cooler to [Enermax ETS-T40-TB](http://www.enermax.com/home.php?fn=eng/product_a1_1_1&lv0=49&lv1=57&no=161)

{% include figure image_path="/assets/img/heatsink1.jpg" alt="Look at the size of that thing!! " class="image-large image-centre" caption="Enermax ETS-T40-TB." %}

{% include figure image_path="/assets/img/heatsink2.jpg" alt="Look at the size of that thing!! " class="image-large image-centre" caption="Enermax ETS-T40-TB." %}

The day was a resounding success. We landed up having to remove almost every component from the Node 304 case. This is because the bigger coolers have a mini chassis that needs a support plate bolted on the bottom of the mobo. Some things I learned:
* The ATX plugs into the motherboard are probably the hardest thing to get off. This is because the clip is pushed right up against the Power Supply. This meant that the motherboard had to be removed before the cables could be removed.
* My gushy and embarrassing love for the Node 304 case was completly justified. The HDD's come out so easily, including the support strut that they hang off. That made putting the motherboard with the new cooler in really straight forward. The fact that the back fan could come off with minimal fuss was great. Everything just fitted nicely.
* Arctic silver is the best thermal paste.
* CPUs have changed since I last looked at one. When I last looked they had pins.

{% include figure image_path="/assets/img/heatsink3.jpg" class="image-medium image-centre" caption="The new cooler in place." %}

{% include figure image_path="/assets/img/heatsink4.jpg" class="image-large image-centre" caption="Side on view. Case fan on the left removed." %}

{% include figure image_path="/assets/img/heatsink5.jpg" class="image-medium image-centre" caption="Top down showing the drive hangers." %}

The cooler itself was probably the best fit you could hope for. We had to remove the back fan on the case, and I might think about adding that back on the outside of the case later (it'll need some spacers as the the fan grating on the case protrudes slightly, but fortunately I have some rubber gromets from Fractal Design that will fit the bill very nicely.).

Here is some finger waving performance info. Since I've had the new cooler in I've been brave enough to fiddle around with some CPU Monero Mining. Here is some CPU and Temperature performance for the setup having been mining Monero for 24 hours:

{% include figure image_path="/assets/img/new_cooler_cpu.png" alt="Flat like a pancake." class="image-large image-centre" caption="9 Monero threads keeping the CPU nice and busy." %}

{% include figure image_path="/assets/img/new_cooler_temp.png" alt="Flat like a pancake. Again. " class="image-large image-centre" caption="Not higher than 60 degrees." %}

All in all I'm super stoked and feel much less jaw quivering rage. The CPU cooler problem is sorted, but I'm kind of back to the drawing board with the SATA problem.
