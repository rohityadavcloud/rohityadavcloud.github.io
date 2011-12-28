---
layout: project
title: Cade9
permalink: /cade9/
tagline: AtMEGA32 based arcade gaming platform
---

<img align="right" src="/images/projects/cade9-pcb.jpg" width="300"/> 
Cade9 is my small embedded project consisting of ATmega32 microcontroller with custom hardware which I hand soldered on a matrix board. This was my undergraduate 3rd year minor project. It uses the open source [cocoOS][cocoos] as scheduler. On top of CADE9 one can implement classic arcade games like snakes, pong, bricks, breakout etc. (arCADE games with max. displayable score limit of 9, so CADE9 :)

Hardware Specs:

* ATmega32 uC @ 16MHz
* 12 x 7 custom hand soldered (3mm) LED matrix
* Two 7-segment displays to display scores.
* Five buttons for input: Up, Down, Left, Right and Fire.
* One buzzer for audio effects.

In the video below, I play a game of 1-player pong; the left bat is AI and top 7-segment display shows score of computer. The player with a score of 9 dot wins! Because I did not use any locking object like semaphore, it sometimes misses a good hit. The code is pretty naive.

Download project report, and/or browse the <a href="http://github.com/rohityadav/cade9">source</a>.

<iframe width="820" height="450" src="http://www.youtube.com/embed/fmwXqJI0i44" frameborder="0" allowfullscreen></iframe>

[cocoos]: http://www.cocoos.net
