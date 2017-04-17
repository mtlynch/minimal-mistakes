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

I've been playing with [Raspberry Pis](https://www.raspberrypi.org/products/) for a while and I've been wanting to take on Pi project that is a bit more hardware-focused. I'd also been interested in getting a plant in my apartment, but I didn't want to have to water it every day. Then I realized I could probably program my Pi to automate it. My friend Jeet is learning Python, so we decided to work together on this project together.

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

GreenPiThumb was also rigorously code reviewed. We did not check any code into the GreenPiThumb Github repository until it had completed a formal code review. You can see in our [pull request history](https://github.com/JeetShetty/GreenPiThumb/pulls?utf8=%E2%9C%93&q=is%3Apr) that we have gone through over a hundred of these code reviews.

## Equipment

Many of these are commodity parts, so you can swap in a different item that does the same thing, but these are components that I used.

### GreenPiThumb Essentials

| Item | Cost |
|------|------|
| [Coolerguys 100-240v AC to 12 & 5v DC 4pin Molex 2A Power Adapter](http://amzn.to/2oET4vC) | $15.00 |
| [SODIAL(R)20PCS Photoresistor GL5528 LDR Photo Resistors Light-Dependent](http://amzn.to/2oCFlUO) | $1.72 |
| [StarTech 6in 4 Pin Molex to SATA Power Cable Adapter](http://amzn.to/2ohoJ3O) | $2.75 |
| [Seaflo 12v Water Pressure Diaphragm Pump 3.8 LPM 1.0 GPM 40 PSI - Caravan/rv/boat/marine](http://amzn.to/2p90wk8) | $29.99 |
| [uxcell Sensitivity Control Temperature Humidity Sensor 20-90%RH](http://amzn.to/2p9iHXa) | $2.13 |
| [Adafruit MCP3008 8-Channel 10-Bit ADC With SPI Interface for Raspberry Pi](http://amzn.to/2poV4tn) | $6.22 |
| [Raspberry Pi Camera Module V2 - 8 Megapixel,1080p](http://amzn.to/2oEVomw) | $28.89 |
| [Kingston 16 GB Class 4 MicroSDHC Flash Card with SD Adapter SDC4/16GB](http://amzn.to/2nTHVZs) | $5.99 |
| [Soil Probe (lead free)](http://dickson.bigcartel.com/product/soil-probe-for-plant-friends) | $10.00 |
| [Busboard Protot SB400 Solder able PC Breadboard 1 Sided PCB Matches 400 Tie-Point Breadboards with Power Rails](http://amzn.to/2nTDOfF) | $5.90 |
| [White SiliconeTubing, 3/8"ID, 1/2"OD, 1/16" Wall, 10' Length](http://amzn.to/2oho2aL) | $10.99 |


### Electronics Components

These are electronics components that I purchased to build the Pi, but these are common components one would use 

Soldering Iron
Wiring
Pack of Resistors

### Optional Components

| Item | Cost | Notes |
|------|------|-------|
| [SharkBite U701 PEX Tubing Cutter, 1/4-Inch, 3/8-Inch, 1/2-Inch, 3/4-Inch and 1-Inch](http://amzn.to/2olsG6N) | $20.99 | Makes it far easier to make clean cuts to the tubing. |
| [Tree Stand or Rail Hunting Bendy Mount for Camera, VideoCam, Camcorder](http://amzn.to/2oCsaD8) | $29.95 | Useful |
| Pi Camera Mount | - | - |
| Pi Camera Extension Cable | - | - |

## The final product

TODO: Add pictures
TODO: Add Fritzing diagram

## Lessons learned

* Nothing is as simple as it seemed
	* I thought this would be a relatively straightforward 2-3 month project, but it took over a year to complete.
* Hardware testing needs as much rigor as software testing
  * Many of the stalls in this project were due to problems in the physical hardware.
  * This was in large part due to our inexperience with electronics, but also because we didn't approach the hardware with the same rigor we approached the software.
  * We realized over time how important it is to build in very small pieces, verifying functionality each time.
  * An inexpensive multimeter (todo: link) proved to be a very valuable tool in testing assumptions about the wiring when components failed.

All of our code is available on GitHub:

* [GreenPiThumb backend](https://github.com/JeetShetty/Greenpithumb)
  * Main
* [GreenPiThumb web API](https://github.com/JeetShetty/GreenPiThumb_Frontend)
  * An HTTP API for retrieving information about GreenPiThumb's state (e.g. sensor readings, camera images).
* [GreenPiThumb AngularJS app](https://github.com/JeetShetty/GreenPiThumb_Frontend_static)
  * A client-side AngularJS web application that shows GreenPiThumb's state and history.
* [GreenPiThumb Ansible Role](https://github.com/JeetShetty/ansible-role-greenpithumb)
  * An [Ansible](https://www.ansible.com/how-ansible-works) configuration that allows users to automatically deploy GreenPiThumb and all dependencies to a device.

TODO: link to the static mirror

## Acknowledgments

Big thanks to those who helped along the way:

* [Devon Bray](http://www.esologic.com)
	* His project, [PiPlanter 2](http://www.esologic.com/?page_id=1042) heavily inspired the hardware aspects of GreenPiThumb
* [Dickson Chow](http://dicksonchow.com)
	* His project, [Plant Friends](http://dicksonchow.com/plant-friends-mkii/) was a helpful hardware reference as well and he supplied a cool [lead-free soil moisture sensor](http://dickson.bigcartel.com/product/soil-probe-for-plant-friends) that we used in GreenPiThumb and provided lots of encouragement throughout the project.
* The [/r/raspberry_pi](https://www.reddit.com/r/raspberry_pi) reddit community
	*  For [their help](https://www.reddit.com/r/raspberry_pi/comments/5i856z/help_turning_on_a_12v_water_pump_with_a_pi/) when we got stuck with wiring issues
