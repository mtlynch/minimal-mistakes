---
title: Building a Homelab VM Server
date: '2017-04-21 01:00:00 -0400'
tags:
- virtualization
- homelab
excerpt:
---

{% include base_path %}

# Overview

I do the bulk of my development work in virtual machines (VMs).

I was inspired by [this post](https://blog.brianmoses.net/2016/07/building-a-homelab-server.html) from Brian Moses' blog.

# Why VMs?

# Why a Whole VM Server?
I've been running virtual machines with VirtualBox, but I have a few issues with it:

* Any time I restart my main PC, I also have to one-by-one shut down or suspend every VM I'm running, then one-by-one start each up again after the reboot
* My main PC crashes about once a month and VirtualBox is really not good at recovering from crashes. On reboot, it tends to think that the VM image files are locked and I have to futz around with the filesystem to fix it.

There are also some peer-to-peer projects I think are neat (e.g. [OpenBazaar](https://openbazaar.org), [Rein](https://reinproject.org)), but they require running a server all the time. I've tried doing this through VirtualBox, but the hassles I mention above tend to make me lose interest in keeping these VMs running. If I had a VM running all the time, I could just spin up these servers and use these services without jumping through hoops each time of remembering how to spin up the VM and server.

A dedicated VM server solves these problems because it's much less likely to crash

# Choosing the Parts

## CPU

In Brian's blog post, he was excited to take advantage of the [low price of used Intel Xeon CPUs](http://www.techspot.com/review/1155-affordable-dual-xeon-pc/). This was a neat idea, but I expect that buying decommissioned hardware increases the risk of hardware failure and I wanted to minimize my risk of that.

I overclock the CPU on my main PC, but also decreases stability. I want to keep my VM server as stable as possible, so I didn't want to bother with overclocking. That also makes choosing parts easier and less expensive because I don't need to pay a premium for an unlocked CPU, a motherboard that supports overclocking, or a premium CPU cooler.

I'm not going to be hammering the CPU with anything intense like video transcoding or 100 QPS web servers, but I did want it to support lots of low-intensity processes at once.

I ended up going with the [AMD Ryzen 7 1700](http://amzn.to/2o1lDVI). It's 8 cores, 16 threads, so it should be a good fit for running many VMs and it has been getting a lot of good reviews lately.

## Memory
My main PC has 32 GB of RAM and tends to use around 15 GB during daily usage (even with Windows 10 and multiple VMs running). I figured I could probably get by with 16 GB, but 32 GB will probably be a safe upper limit for the next 2-3 years.

## Disk

Like Brian, [I have a NAS]({{ base_path }}/sia-via-docker/) with plenty of space available, so all I needed as far as local storage was a small disk to hold the host / hypervisor OS. I went with a 250 GB [Samsung 850 EVO](http://amzn.to/2pyfArr) mainly because I find the M.2 interface very clean. It's just a chip you screw into your motherboard and you're done. No need to deal with mounts or SATA cables. 250 GB is way more than I need, but for an M.2 SSD, that seems to be about the entry level.

## Graphics & Display
I'm going to run the VM server headless, managing it entirely over SSH or a web interface, so there's no need for 

## Motherboard

I live in a pretty small 1 BR apartment in Manhattan, so physical space is at a premium. My requirements also obviated a lot of components that typically requires a lot of physical space in a PC, such as disk drives, GPUs, or premium CPU fans.

## Case


## Final Parts List

| Category | Component |  Price |
|------|-------|
<<<<<<< HEAD
| CPU | [AMD Ryzen 7 1700](http://amzn.to/2o1lDVI) | $323.66 |
| Motherboard | [ASRock AB350M-HDV](https://www.newegg.com/Product/Product.aspx?Item=N82E16813157765) | $69.99 |
| Disk | [Samsung 850 EVO - 250GB](http://amzn.to/2pyfArr) | $99.99 |
| RAM | [G.SKILL Flare X Series 32GB (2 x 16GB) F4-2400C15D-32GFXR](https://www.newegg.com/Product/Product.aspx?Item=N82E16820232536) | $224.99 |
| PSU | [EVGA 430 W1, 80+ WHITE 430W  100-W1-0430-KR](http://amzn.to/2oVMo9u) | $29.99 |
| GPU | [EVGA 512-P3-1300-LR GeForce 8400 GS](http://amzn.to/2qmwmHO) | $29.99 |
| NIC | [Broadcom BCM5751 Netxtreme](http://amzn.to/2pxVLjH) | $22.95 |
| Case | [Rosewill Micro ATX SRM-01](http://amzn.to/2oYTvP6) | $21.99 |
| **Total** | | **$823.55** |

## Build

[![PC parts]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-parts.jpg)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-parts.jpg)
[![Server after assembly]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-assembled.jpg)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-assembled.jpg)
[![Assembled server - front view]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-front.jpg)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-front.jpg)
[![Assembled server - overhead view]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-above.jpg)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-above.jpg)

## Running Virtual Machines

[![Kimchi host utilization dashboard ]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/kimchi-host-utilization.png)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/kimchi-host-utilization.png)
[![Kimchi guest view ]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/kimchi-guests.png)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/kimchi-guests.png)

## What I'd Change

### CPU

Haven't seen CPU usage above 35%

Dual LGA 2011 motherboards start at at ~$300, so even if I found a couple used Intel Xeons on eBay for $50 apiece, then another $40 or so on coolers, I'd be spending more. Add to that the fact that there don't seem to be any dual LGA 2011 motherboards smaller than ATX.


### RAM


<br>
*Disclosure: This post uses affiliate links to reference some products mentioned in the post. This allows the blog to receive commission when readers make purchases through these links.*
=======
| CPU | [AMD Ryzen 7 1700](http://amzn.to/2o1lDVI) | $319.99 |
| Motherboard | [ASRock AB350M-HDV AM4 AMD Promontory B350](https://www.newegg.com/Product/Product.aspx?Item=N82E16813157765) | $69.99 |
| Disk | [Samsung 850 EVO - 250GB](http://amzn.to/2pyfArr) | $113.00 |
| RAM | [Corsair LPX 32GB (2x16GB) 3200MHz C16 DDR4](http://amzn.to/2pytLg3) | $324.99 |
| PSU | [FSP Group Mini ITX Solution / Flex ATX 220W 80 PLUS Certified Active PFC Power Supply](http://amzn.to/2o1iWU9) | $52.99 |
| Case | [Fractal Design Node 804 No Power Supply MicroATX Cube](http://amzn.to/2oTr0FE) | $109.99 |
