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

I ended up going with the [AMD Ryzen 7 1700](http://amzn.to/2o1lDVI) (thanks to advice on [/r/buildapc](https://www.reddit.com/r/buildapc/comments/65hjqe/how_does_this_look_for_a_vm_server/)). It's 8 cores, 16 threads, so it should be a good fit for running many VMs and it has been getting a lot of good reviews lately.

## Memory
My main PC has 32 GB of RAM and tends to use around 15 GB during daily usage (even with Windows 10 and multiple VMs running). I figured I could probably get by with 16 GB, but 32 GB will probably be a safe upper limit for the next 2-3 years.

## Disk

Like Brian, I have a huge NAS with plenty of space available, so all I needed as far as local storage was a small disk to hold the host / hypervisor OS.

## Graphics & Display
I'm going to run the VM server headless, managing it entirely over SSH or a web interface, so there's no need for 

I did need *some* sort of display for the initial install and the occasional administration tasks that I can't do over SSH. I don't need a dedicated GPU for that and many motherboards come with basic onboard graphics. I *could*  just disconnect my main PC's monitor and connect it to the VM server temporarily, but I hate crawling under my desk and swapping cables around, so I used [this excellent little 5" TFT display](http://amzn.to/2o9MlLH) I use when debugging my Raspberry Pis. It's cheap, easily stored when not in use, and just needs a micro-USB cable for power.

## Motherboard

I live in a pretty small 1 BR apartment in Manhattan, so physical space is at a premium. My requirements also obviated a lot of components that typically requires a lot of physical space in a PC, such as disk drives, GPUs, or premium CPU fans.

## Case



## Final Parts List

| Category | Component |  Price |
|------|-------|
| CPU | [AMD Ryzen 7 1700](http://amzn.to/2o1lDVI) | $319.99 |
| Motherboard | [ASRock AB350M-HDV AM4 AMD Promontory B350](https://www.newegg.com/Product/Product.aspx?Item=N82E16813157765) | $69.99 |
| Disk | [Samsung 850 EVO - 250GB](http://amzn.to/2pyfArr) | $113.00 |
| RAM | [Corsair LPX 32GB (2x16GB) 3200MHz C16 DDR4](http://amzn.to/2pytLg3) | $324.99 |
| PSU | [FSP Group Mini ITX Solution / Flex ATX 220W 80 PLUS Certified Active PFC Power Supply](http://amzn.to/2o1iWU9) | $52.99 |
| Case | [Fractal Design Node 804 No Power Supply MicroATX Cube](http://amzn.to/2oTr0FE) | $109.99 |
