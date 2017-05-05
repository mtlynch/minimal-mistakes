---
title: Building a Homelab VM Server
date: '2017-04-21 01:00:00 -0400'
tags:
- virtualization
- homelab
- kimchi
- kvm
excerpt: Taking my development VMs to the next level
---

{% include base_path %}

# Overview

I do the bulk of my development work in virtual machines (VMs). I'd always just run my VMs from within Windows on my main desktop machine, but I began wondering if there was a better solution.

I searched around and found [this post](https://blog.brianmoses.net/2016/07/building-a-homelab-server.html) on Brian Moses' blog where he describes building a dedicated "homelab" server for running VMs and I was inspired to do the same.

# Why VMs?
All the software I write depends on several software dependencies. For example, development on my project [ProsperBot]({{ base_path }}/prosperbot/) depends on the Go toolchain, nginx, and Redis. If I kept installing dependencies for each of my projects all on my main desktop PC, it would become a mess of different web servers, database servers, and competing versions of the same libraries.


# Why a Whole VM Server?
I've been running virtual machines within my main Windows desktop with VirtualBox, but I have a few issues with it:

* Any time I restart my main PC, I also have to one-by-one shut down or suspend every VM I'm running, then one-by-one start each up again after the reboot
* My main PC crashes about once a month and VirtualBox is really not good at recovering from crashes. On reboot, it tends to think that the VM image files are locked and I have to futz around with the filesystem to fix it.

With a dedicated VM server, I can run a very stripped down Linux server OS on it. The less software running on a machine, the less frequently it requires reboots and the less likely it is to crash.

There are also some peer-to-peer projects I think are neat (e.g. [OpenBazaar](https://openbazaar.org), [BitSquare](https://bitsquare.io/)), but they require running a server all the time. I've tried doing this through VirtualBox, but the hassles I mention above tend to make me lose interest in keeping these VMs running. If I could just spin up a VM once and leave it running, it makes experimenting with these projects a lot more attractive.

# Choosing the Parts

## CPU

In Brian's blog post, he was excited to take advantage of the [low price of used Intel Xeon CPUs](http://www.techspot.com/review/1155-affordable-dual-xeon-pc/). This was a neat idea, but I was afraid of the risk of hardware failure from used server hardware, so I preferred a new retail CPU.

I overclock the CPU on my main PC, but this also leads to occasional crashes. I want to keep my VM server as stable as possible, so I didn't want to bother with overclocking. This happens to make choosing parts easier and less expensive because I don't need to pay a premium for an unlocked CPU, a motherboard that supports overclocking, or a premium CPU cooler.

I ended up going with the [AMD Ryzen 7 1700](http://amzn.to/2o1lDVI). It's 8 cores, 16 threads, so it should be a good fit for running many VMs and it has been getting a lot of good reviews lately.

## Memory
My main PC has 32 GB of RAM and tends to use around 15 GB during daily usage (even with Windows 10 and multiple VMs running). I figured I could probably get by with 16 GB, but 32 GB will probably be a safe upper limit for the next 2-3 years.

## Motherboard

I live in a pretty small 1 BR apartment in Manhattan, so physical space is at a premium. My requirements also obviated a lot of components that typically requires a lot of physical space in a PC, such as disk drives, GPUs, or premium CPU fans.

## Case
For the case, I was primarily looking for something very small. I plan to tuck the server away out of sight, so it didn't need to be anything pretty or have fancy aesthetics. The [Rosewill Micro ATX SRM-01](http://amzn.to/2oYTvP6) is a nice, small, inexpensive, and functional.

## Disk

Like Brian, [I have a NAS]({{ base_path }}/sia-via-docker/) with plenty of space available, so all I needed as far as local storage was a small disk to hold the host / hypervisor OS. I went with a 250 GB [Samsung 850 EVO](http://amzn.to/2pyfArr) mainly because I find the M.2 interface very clean. It's just a chip you screw into your motherboard and you're done. No need to deal with mounts or SATA cables. 250 GB is way more than I need, but for an M.2 SSD, that seems to be about the entry level.

## Graphics & Display
I'm mainly going to run this system headless and just manage it over SSH/Ansible, but I need a display occasionally (e.g. during initial install or when I accidentally break the network configuration). I initially *thought* I could use the motherboard's integrated graphics support, but I could not (see the [parts review](#motherboard-1) below).

Because my requirements for the GPU were so flexible, I just wanted something inexpensive and positively reviewed, so I chose the [EVGA GeForce 8400 GS](http://amzn.to/2oVMo9u).

## Network Adapter
I planned to just use the motherboard's onboard 1 Gbps NIC because I only have a 1 Gbps network. It did work out of the box with Ubuntu 16.04, but I soon noticed that my network speeds were limited to about 10 Mbps. After a bit of research, I discovered that Ubuntu 16.04 does not include the correct drivers, but requires you to add a separate apt-get repo to install the `r8168-dkms` package. I did this, but on reboot, Ubuntu would completely fail to detect the NIC...

At this point, I got tired of tinkering with the onboard NIC and just bought a PCI NIC that I'd read was supported out of the box on Ubuntu: [Broadcom BCM5751 Netxtreme](http://amzn.to/2pxVLjH). It got 1 Gbps speeds with zero tinkering, so for $23, I decided it wasn't worth the time to keep trying to investigate the problems with the onboard NIC.

Also of note: the onboard NIC was *not* compatible with ESXi 6.5, but the Broadcom NIC *was* compatible.

## Final Parts List

| Category | Component |  Price |
|------|-------|
| CPU | [AMD Ryzen 7 1700](http://amzn.to/2o1lDVI) | $323.66 |
| Motherboard | [ASRock AB350M-HDV](https://www.newegg.com/Product/Product.aspx?Item=N82E16813157765) | $69.99 |
| Disk | [Samsung 850 EVO - 250GB](http://amzn.to/2pyfArr) | $99.99 |
| Memory | [G.SKILL Flare X Series 32GB (2 x 16GB) F4-2400C15D-32GFXR](http://www.tkqlhce.com/38102iqzwqyDMHGNMLGDFFMNGHKK?url=http%3A%2F%2Fwww.newegg.com%2FProduct%2FProduct.aspx%3FItem%3DN82E16820232536%26nm_mc%3DAFC-C8Junction-Storage%26cm_mmc%3DAFC-C8Junction-Storage-_-Memory%2B%28Desktop%2BMemory%29-_-G.SKILL-_-20232536&cjsku=N82E16820232536) | $224.99 |
| Power | [EVGA 430 W1, 80+ WHITE 430W  100-W1-0430-KR](http://amzn.to/2oVMo9u) | $29.99 |
| Graphics | [EVGA 512-P3-1300-LR GeForce 8400 GS](http://amzn.to/2qmwmHO) | $29.99 |
| Network | [Broadcom BCM5751 Netxtreme](http://amzn.to/2pxVLjH) | $22.95 |
| Case | [Rosewill Micro ATX SRM-01](http://amzn.to/2oYTvP6) | $21.99 |
| **Total** | | **$823.55** |

# Build

[![PC parts]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-parts.jpg)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-parts.jpg)
[![Server after assembly]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-assembled.jpg)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-assembled.jpg)
[![Assembled server - front view]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-front.jpg)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-front.jpg)
[![Assembled server - overhead view]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-above.jpg)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/vm-server-above.jpg)

# Running Virtual Machines

## Ubuntu 16.04 + KVM + Kimchi

I tried a few different Linux distros, but the only one that worked out of the box on the Ryzen was Ubuntu server (tested successfully on both Ubuntu 16.04 and 17.04). It includes a bit more packages than I'd prefer, but it's also the Linux distro I'm most familiar with.

For the hypervisor, I used KVM. The biggest weakness is that compared to ESXi

[![Kimchi host utilization dashboard ]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/kimchi-host-utilization.png)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/kimchi-host-utilization.png)
[![Kimchi guest view ]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/kimchi-guests.png)]({{ base_path }}/images/2017-04-24-building-a-vm-homelab/kimchi-guests.png)

## Also ran: ESXi 6.5

I actually went into this project planning to use [VMware vSphere Hypervisor](https://www.vmware.com/products/vsphere-hypervisor.html), VMware's free hypervisor offering. It seemed like a much more mature product with a larger user base (so presumably easier to find support). However, it ended up being incompatible with both my motherboard's NIC and the Ryzen CPU. I was finally able to run it after I installed the Broadcom NIC and disabled my CPU's SMT in BIOS, but by that point, I'd been using Kimchi for a few days and gotten used to it.

vSphere didn't seem to offer a significantly better experience than Kimchi. The UI is much more slick, but it also had very klunky flows where one mistake would force you to completely restart a whole multi-stage process from scratch. It also wasn't obvious how to access the shell to just do what I want on the command-line (I'm sure it's possible, but I didn't investigate long enough for the answer).

The dealbreaker for me was that on login, vSphere prominently displayed a warning saying that the software would stop working in 60 days unless I entered a VMware registration key. VMware provides a license key for free, but I didn't want to bother with registration keys when Kimchi isn't tied to any kind of licensing checks and provides an experience that's about equal to vSphere.

# Reviewing My Choices

## CPU

My most questionable choice is the CPU. It does run very fast, but it may have also been overkill, as I haven't seen total CPU usage rise above 35%, even when I've got five VMs running with  CPU-intensive jobs running on several of them.

The downside to the Ryzen is that it's very bleeding edge right now and compatibility is shaky. I tried installing Fedora 25 server, Debian 8.7, Centos 7, and ESXi 6.5 and they all died during the installation because they weren't compatible with the Ryzen. I was able to install some of these successfully if I disabled SMT (multithreading) for the CPU in BIOS, but that reduces it to an from a 16-core to an 8-core CPU, which felt sad. The only OS that installed successfully was Ubuntu (successfully installed both 16.04 and 17.04).

The Ryzen also limited what RAM sticks I could buy. The motherboard supports DDR4 RAM up to 3200 MHz, but Corsair has no memory [tested compatible](http://www.corsair.com/en-us/memory-finder) with it. [G.SKILL does](https://www.gskill.com/en/configurator?manu=52&chip=2952&model=2990), but nothing faster than DDR4 2400 MHz.

## RAM

32 GB seems to be a bit overkill as well. With all my VMs running, the system only uses about 12 GB of memory, so I could have maybe gotten away with only 16 GB. At the same time, it's nice knowing that I'm very far from the memory ceiling.

## Motherboard

I'm mostly happy with my motherboard choice. It's nice and compact without sacrificing adequate space for all the components.

My one regret is that I didn't read the onboard video support carefully enough. Its specs under "Onboard Video Chipset" read:

>Integrated AMD Radeon R7/R5 Series Graphics in A-series APU
>Supports HDMI with max. resolution up to 4K x 2K (4096x2160) @ 24Hz / (3840x2160) @ 30Hz

So I thought, "Great! It's got its own graphics card. One less thing to install." What I didn't understand was that this meant, "Supports graphics *only if* you have an AMD A-Series APU." APUs are AMD's combined CPU/GPU chips, and the Ryzen is not one of them.

If I were to do this again, I'd go with the [GIGABYTE GA-AB350M-Gaming 3](http://www.dpbolvw.net/click-8329872-11892368?url=http%3A%2F%2Fwww.newegg.com%2FProduct%2FProduct.aspx%3FItem%3DN82E16813145002%26nm_mc%3DAFC-C8Junction-Components%26cm_mmc%3DAFC-C8Junction-Components-_-Motherboards%2B-%2BAMD-_-GIGABYTE-_-13145002&cjsku=N82E16813145002) just for the simplicity of having an onboard GPU.

# Conclusion
This homelab VM server is working out very well. It's very convenient to be able to know that my VMs are running all the time, so I can just SSH in or view them in the browser without having to spin anything up in VirtualBox.


<br>
*Disclosure: This post uses affiliate links to reference some products mentioned in the post. This allows the blog to receive commission when readers make purchases through these links.*
