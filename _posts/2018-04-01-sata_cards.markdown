---
layout: post
title: SATA card round up
date: 2018-04-01
description: SATA card round up
img: sata.jpg # Add image post (optional)
tags: [nas,satacard] # add tag
toc: true
---

In order to keep track of my many Amazon purchases to date, I've made a list of the PCI sata cards that I've tried.

| Name | Picture | Details | Notes |
| ---- | --------| --------| ----------- |
| Semlos 4 Ports PCI Sata | ![](/assets/img/amazon_semlos_big.jpg){: .image-medium :} | Sil3114 Chipset  | Worked great out of the box. Stopped working after a week - returned. |
| Smart Gear 4 Ports PCI Sata | ![](/assets/img/amazon_SIL3114_big.jpg){: .image-medium :} | Sil3114 Chipset  | Working greater than one week after install. Forced to set drive type to 'concatenate'. Fights GTP setup that zfs raid uses. |
| SODIAL(TM) 4 Port SATA & IDE | ![](/assets/img/amazon_sodial_big.jpg){: .image-medium :} | VIA VT6421a  | Took two months to arrive. Might have to use it if the GPT issue isn't resolved. |


If options 2 and 3 above fail, I'll try this one:

| Name | Picture | Details | Notes |
| ---- | --------| --------| ----------- |
| PROMISE PCD20378 IDE + SATA + eSATA RAID CONTROLLER CARD | ![](/assets/img/amazon_promise_big.jpg){: .image-medium :} | PCD20378  | Backup |

https://www.amazon.co.uk/CONTROLLER-CHANNELS-INDEPENDANT-CHIPSET-SILICON/dp/B00IXPHNAE/ref=sr_1_5?ie=UTF8&qid=1522589415&sr=8-5&keywords=Sil3112

https://www.amazon.co.uk/CONTROLLER-CHANNELS-INDEPENDANT-CHIPSET-SILICON/dp/B002USPIXO/ref=sr_1_3?ie=UTF8&qid=1522589415&sr=8-3&keywords=Sil3112

https://forums.macrumors.com/threads/pci-sata-cards-what-is-the-state-of-compatibility.1650568/
