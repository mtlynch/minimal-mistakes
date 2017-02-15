---
title: GreenPiThumb
excerpt: Using Raspberry Pi to water plants automatically
tags:
- raspberry pi
- greenpithumb
- gardening
---

{% include base_path %}

## Overview

I've been playing with Raspberry Pis (todo: link) for a while and I've been wanting to take on Pi project that is a bit more hardware focused. I'd also been interested in getting a plant in my apartment, but I didn't want to have to water it every day. Then I realized I could probably program my  My friend Jeet is learning Python, so we decided to work together on this project together.

## What is GreenPiThumb?

## Why make another Pi-powered gardening bot?

Many other Pi enthusiasts have created Pi-powered gardening bots. I searched around for tutorials for similar projects and I noticed that a lot of them had a few shortcomings:

* They assume a background in electronics hardware
* The code is not easily extendible

Naturally, 

I'm a professional software developer, so I see unit tests as a hard requirement for any polished software project. Also, this was partly a learning exercise for my friend who was learning to code, I wanted to follow all the practices I'd follow if I was producing commercial software. As such, GreenPiThumb features:

* Full unit tests
* Code coverage tracking
* Continuous integration
* Thorough documentation (both READMEs and code comments)
* Consistent adherence to [a defined style guide](https://google.github.io/styleguide/pyguide.html)
* [A provisioning tool](https://github.com/JeetShetty/ansible-role-greenpithumb)

## The final product

TODO: Add pictures
TODO: Add Fritzing diagram

## Lessons learned

* Nothing is as simple as it seemed
	* I thought this would be a relatively straightforward 2-3 month project, but it took over a year to complete.

## Tutorial: Coming soon

Stay tuned to this blog for future posts where I'll take you through the process of assembling the hardware for a GreenPiThumb and for provisioning all the software.

In the meantime, all of our code is available on GitHub:

* https://github.com/JeetShetty/greenpithumb
* https://github.com/JeetShetty/GreenPiThumb_Frontend
* https://github.com/JeetShetty/GreenPiThumb_Frontend_static
* https://github.com/JeetShetty/ansible-role-greenpithumb

## Acknowledgments

Big thanks to those who helped along the way:

* [Devon Bray](http://www.esologic.com)
	* His project, [PiPlanter 2](http://www.esologic.com/?page_id=1042) heavily inspired the hardware aspects of GreenPiThumb
* [Dickson Chow](http://dicksonchow.com)
	* His project, [Plant Friends](http://dicksonchow.com/plant-friends-mkii/) was a helpful hardware reference as well and he supplied a cool [lead-free soil moisture sensor](http://dickson.bigcartel.com/product/soil-probe-for-plant-friends) that we used in GreenPiThumb.
* The [/r/raspberry_pi](https://www.reddit.com/r/raspberry_pi) reddit community
	*  For [their help](https://www.reddit.com/r/raspberry_pi/comments/5i856z/help_turning_on_a_12v_water_pump_with_a_pi/) when we got stuck with wiring issues