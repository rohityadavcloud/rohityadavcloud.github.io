---
layout: post
title: Pimp My Desktop
---

I love computers! My primary computer is a _heavyduty_ desktop which I'd custom built more than a year ago. I love to hack microcontrollers and I'm still waiting for my RaspberryPi. I'm not a big fan of laptops though. But, as long as any of 'em can run GNU/Linux, I'm happy.

This morning I was going through some of my rarely used electronic parts (I've tons of electronic parts, and perhaps 100s of LEDs, buttons and all sorts of jumpers) which I've not used since last one year. Of them I found a UART controlled LCD display, a spare Arduino and bunch of sensors and I decided to make anything that adds some luxury to my desktop experience. A really small hack and this is the result:

<div class="post-image">
  <img align="center" src="/images/desktop-pimping.jpg">
</div>

A visual luxury to my desktop's front panel which shows realtime CPU and Memory usage with average temperature of all the cores and bonus an audio visualizer. How was it hacked? It's an Arduino board that is bolted inside my desktop and communicates with a python server to get the stats serially over an internal USB connection via a FTDI chip. I don't know if hooking an alcohol or a LPG or a carbon monoxide sensor can be a good idea to implement an alternative method to (say) login. The server has its own fake REPL, the code can be found in my [hacktools](https://github.com/bhaisaab/hacktools/tree/master/ardulcd) repository where I put silly hacks like this one every now and then.

<div class="post-image">
  <img align="center" src="/images/ardulcd.jpg">
</div>