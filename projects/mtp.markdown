---
layout: project
title: MTP
permalink: /projects/mtp/
---

<p align = "center"><img align="center" src="/images/projects/mtp/side.png"/></p>

The goal of my project was to design and implement a mobile robot that can be controlled wirelessly using speech as a user interface. This was my first mobile robot which I built by forking concepts from [here](http://www.youtube.com/watch?v=cKY9tpxtkvE) and [there](http://www.cs.umu.se/education/examina/Rapporter/ShafkatKibria.pdf). I tried to follow open standards and use opensource hardware and software components wherever possible.

<p align = "center"><img align="center" src="/images/projects/mtp/blender.png"/></p>

I chose a rover/tank like two-wheeled mobile robot design. Using Blender and some freely available Blender models, I modeled the mobile robot and did some animations; the [blend file](/files/docs/mtp/mtp.blend).

<p align = "center"><img align="center" src="/images/projects/mtp/movement.png"/></p>

After comparing many speech recognition hardware modules, I chose EasyVR an isolated speaker independent speech recognition hardware module. I wanted to use RaspbeeryPi but it was not available at the time so I used Arduino microcontroller board which is a opensource low-cost and easy to program microcontroller platform. For wireless communication I used a pair of ZigBee/IEEE-802.15.4 based radio module, XBee Series1.

Other main hardware parts I used were:

* Infrared Proximity sensor
* Half-rotation servo motor
* DC motors and IC L293D (motor driver)
* Voltage regulator LM7805
* Tamiya's Gearbox and Universal Plate sets

<p align = "center"><img align="center" src="/images/projects/mtp/robot.jpg"/></p>

The whole system has three parts; the mobile robot; the base-station and the Monitor program running on a PC for debugging and visual feedback.

The handheld base-station is used by the human user to control the mobile robot using speech as an interface, the base-station is also Arduino microcontroller board that talks to a Monitor program running on a PC/Mac at 9600 baud rate over USB and also connects with the speech recognition module.

<p align = "center"><img align="center" src="/images/projects/mtp/monitor.png"/></p>

For the Monitor's visualization I forked the _Processing_ code from this [tutorial](http://luckylarry.co.uk/arduino-projects/arduino-sonic-range-finder-with-srf05/).

<p align = "center"><img align="center" src="/images/projects/mtp/servosweep.png"/></p>

<p align = "center"><img align="center" src="/images/projects/mtp/obstacle-avoidance.png"/></p>

For obstacle avoidance, I implemented a braindead naive algorithm in which the mobile robot would stop if a obstacle is found less than a given threshold distance and sweep-and-search for a direction without obstacle and move in that direction.
