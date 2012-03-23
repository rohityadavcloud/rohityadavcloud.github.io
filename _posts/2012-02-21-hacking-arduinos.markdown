---
layout: post
title: Hacking Arduino Like a Boss
excerpt: Hardware hacking
---

<p align="center"><img align="center" src="/images/boss.gif"></p>

<blockquote>
<p>	Arduino is an open-source electronics prototyping platform based on flexible, easy-to-use hardware and software. It's intended for artists, designers, hobbyists, and anyone interested in creating interactive objects or environments. </p>
<small>arduino.cc</small>
</blockquote>

First thing: get yourself an Arduino board from one of the popular online electronic shops like <a href="http://www.sparkfun.com">Sparkfun</a> or some in India such as [Robokits India](http://robokits.co.in/shop/index.php?main_page=index&cPath=6_72) and [Rhydolabz](http://www.rhydolabz.com/index.php?main_page=index&cPath=152_123).

One may use avr-gcc and avrdude if they choose to program in C using barebone Makefiles, but the easiest way to program an Arduino board is using the [Arduino IDE](http://arduino.cc/en/Main/Software) that is supported on Linux, Mac OSX and Windows. Once you've the IDE and the board setup, get some LEDs or perhaps servos or sensors and play around the [tutorials](http://arduino.cc/en/Tutorial/HomePage) listed on the project webpage.

The Arduino IDE is written in Java and based on [Processing](http://processing.org/) and mainly uses avr-gcc, avr-dude. A typical Arduino sketch is written in [Wiring](http://wiring.org.co/), an open source electronics prototyping platform derived from Processing which has a simplified C++ language, an IDE and used for a single board microcontroller. In a typical sketch a programmer is only required to define two functions; setup() and loop(). When you compile your Arduino sketch, it simply generates equivalent AVR-C code and compiles it into an Intel hex file which is uploaded to the Arduino's microcontroller's on-chip flash memory by avr-dude. Many Arduino hackers use [Fritzing](http://fritzing.org/), a project that aims at providing tools for designing and prototyping the hardware, pcb layout. Now start [hacking](http://arduino.cc/en/Hacking/HomePage) Arduino Like a Boss™.
