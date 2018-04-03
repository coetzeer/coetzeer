---
layout: post
title: All the things are broken
date: 2018-03-21
description: All the things are broken
img: picard_annoyed.jpg # Add image post (optional)
tags: [nas,satacard,heatsink] # add tag
toc: true
---

As a pleasant surprise, SATA card number 3 arrived very quickly. Ordered on Saturday, arrived on Tuesday. This is one of those small benefits you get from living in a first world European company. You spend a lot of time being uncomfortably cold, but your post comes quickly. Probably because the postal workers want to finish up and get back inside.

So, back into 'surgery' mode. Desk cleared, tools out, box open.

# In with the new

I plugged the new card in, which I might remind you, is not straight forward with the Fractal Design case. You basically have to disassemble the card, put it in, and then reassemnle it. No biggie.

Once all the SATA cables are plugged in, and the power cables are gingerly but securely pushed in place, I power on the machine. Presto - the SiL3114 BIOS screen appears, and it looks like we're back in business.

Once the machine boots, everything looks fine. All the plugins and jails are up and running. The torrent jails are downloading their payloads through the VPN tunnels that have been set up. Netdata is showing lovely graphs. All the world seems ok.

But wait: a foul smell in Denmark: there is a visual queue in the top right hand corner that says the box is in a critical state. Clicking on that tells me that that one of the arrays is in the degraded state. Clicking on the 'storage' menu item takes me through to the Volume screen. Some digging around there shows me: Yes, the disks backing up volume '/mnt/vol01/' have one of their brethren gone astray. Handily, there's a 'Repair' button, which I press and think 'damn, these FreeNAS guys are amazing.' Problematically though, there's no drive in the drop down that would allow me to actually repair the array, even though I'm pretty sure I can see it plugged in in front of me.

I rebooted the machine to see if the drive is present in the mobo BIOS. It isn't, which is a big problem.

# Out of the frying pan onto to the hot CPU

It's around that time too that I notice that the CPU cooler is starting to make some distressing noises. In fact, it's gone past noises and has entered the category of 'machinery'. It sounds, in fact, like an unhealthy tractor. Some poking around with that shows me that at least one of the ridiculous plastic clips that Intel gives you to fasten down their very expensive CPU's has come a-cropper.

{% include figure image_path="/assets/img/intel_clip.jpg" alt="Little piece of plastic jump." class="image-large image-centre" caption="Yes, that little clip." %}

I've never experienced these little barrels of joy before, so I have no idea of how to approach them. I could see that at least one was loose. So I shut everything down, put on hold my mental agony over the missing hard drive that won't repair itself, and started fiddling with damn the wee clippies.

I pushed them in. I tried to pull them out. I turned them gently. I turned them the other way. Nothing made them stick. So I decided to pull them all out, forgetting of course that doing so would be mean making the heatsink part way from the CPU, which would mean a whole evening of cleaning heatsinks and cpus, another delay while I order some thermal paste, and, importantly, not being able to see if the original problem of the broken RAID array could be fixed. I did have this thought, but it was around the time that the cooler came away in my one hand and I had managed to smudge thermal paste all over my jumper.

I did what any rational person would do in that scenario: shrugged and go onto youtube. I found video where what sounded like a 14 year old with a speech impediment was doing his best to explain how the clips worked. There is apparently a knack to getting them in place, and twisting them just so. On closer inspection of the disaster area that was my desk at this point, I found that three of the clips had bits missing one of their two sides, and one of them was completely broken and was going to be no use.

{% include figure image_path="/assets/img/intel_clip2.jpg" alt="Little piece of plastic junk." class="image-small image-centre" caption="This little side things were broken." %}

TODO: add actual picture.

I balanced 2 of the clips back onto the coolers 'legs' and tried to get them back onto the motherboard on top the CPU. After many frustrating attempts and lots of fishing of little plastic clips out of the Computer case and off the floor, I finally managed to get the cooler in place, with 3 of the 4 clips holding it more or less immovabley against the processor. I left the 4th clip in just loose, but quickly took it out when I saw that it was rattlinh against the heatsink when the fan started up.

So now, we're somewhat back in business: if you squish the heatsink down 'just so' and switch the power on, it would rattle for a bit then and settle down into something resembling it's usualy operational state. The obvious trade off is that it's not nearly as effective as it was before. Just looking at the box now makes all 4 cores shoot up to 100 degrees celsius, but if I only poke around the web gui, it stays more or less at 60.

# Back to the beginning

Problem temporarily solved. I will have to look into cooling solutions for a socket LGA-1155 at some point in the very near future, but right now I'd like to get back to the borked drive.

I poke around online a bit and find out that I have to set the drives up in the SiL3114 BIOS as 'concatenated' drives. This is as opposed to the other options available: RAID-0, RAID-1, RAID-5 - all of which need multiple drives. With 'concatenated', you can add a single drive to an 'array', which is the simplest presentation that I want FreeNAS to see.

Now, when I restart and look into the mobo BIOS, I can see the additional drive, and when I click the 'repair' button in the FreeNAS Gui, I see a 4TB in the drop down. This time we really ARE back in business.

I let FreeNAS boot up and notice some errors about GPT partitions being unreadable. That's to be expected I guess. I did just do some serious surgery on the box. Perhaps some sort of relignment will required. I wait till it finishes up and fire up the web UI.

I select my drive from the drop and hit the 'BooYAA' button. (it's actually the ok button, but I was feeling bullish.)

No joy.

TODO: get actual error.

Back to the internet.

So according to the FreeNAS forums, the error I'm getting is telling me that something has locked the disk down. The recommendation is to set the followin system var, and try again:

    sysctl kern.geom.debugflags=16

Again, according to [Stack Exchange](https://unix.stackexchange.com/questions/194249/what-is-the-purpose-of-flag-kern-geom-debugflags-in-freebsd), this variable allows you to overwrite the MBR of the drive. This seems ok to me - FreeNAS seems to know what it wants to do with the drive, so I set it, and try again.

This time it works, and the drive is 'resilvered', and 4 becomes 5.

# Finally, to bed.

It's getting onto midnight now. My wife has gone to bed after stomping around upstairs loudly for couple a minutes: a clear signal that I should wrap up and hit the hay as it's a school night. I power down, put the lid back on the case, move it where it lives on a shelf under the window in my study, plug in the power and the network cable and power it back on. I hear the CPU cooler rattle at me for a minute or two, and expect the web gui to come back up at somepoint, but it appears to be taking it's time.

I go and brush my teeth and try again. Still nothing.

I sigh, and get the machine back into surgery mode. The SiL3114 now no longer likes my newly silvered drive. I have to re-add it as a concatenated drive. And once I've done that, FreeNAS doesn't like the drive anymore, I've got make the MBR writeable and re-add it to the array.

This presents a problem: I can't reboot the machine without losing whatever drives are attached to the SATA controller.

This a big problem, and one that I can't solve now at almost 1 am on a Wednesday morning. So I leave it as it stands.

In summary:

## Silver linings
* FreeNAS + ZFS Raid works well. You can lose a drive and carry on running.
* We're not back to 5 drives yet in the RAID array, but the Mobo BIOS and the Silicon Image BIOS see all the drives.

## Dark Clouds
* The heatsink is shot. That is going to cost money.
* This GPT/SiL3114 thing requires more digging. I am actually only running on 4 drives until that's sorted.
