---
layout: project
title: Cade9
permalink: /projects/cade9/
tagline: AtMEGA32 based arcade gaming platform
---

Cade9 is my small embedded project consisting of ATmega32 microcontroller with custom hardware which I hand soldered on a matrix board. This was my undergraduate 3rd year minor project. It uses the open source [cocoOS][cocoos] as scheduler. On top of CADE9 one can implement classic arcade games like snakes, pong, bricks, breakout etc. (arCADE games with max. displayable score limit of 9, so CADE9 :)

<p align = "center"><img align="center" src="/images/projects/cade9-pcb.jpg"/></p>

Hardware Specs:

- ATmega32 uC @ 16MHz
- 12 x 7 custom hand soldered (3mm) LED matrix
- Two 7-segment displays to display scores.
- Five buttons for input: Up, Down, Left, Right and Fire.
- One buzzer for audio effects.

In the video below, I play a game of 1-player pong; the left bat is AI and top 7-segment display shows score of computer. The player with a score of 9 dot wins! Because I did not use any locking object like semaphore, it sometimes misses a good hit. The code is pretty naive.

Download: <a href="/files/docs/minor-project-cade9.pdf">report, <a href="/files/old/cade9.zip">source code</a>.

<p align = "center"><iframe width="820" height="450" src="http://www.youtube.com/embed/F4D6QbLzOpM?rel=0" frameborder="0" allowfullscreen></iframe></p>

[cocoos]: http://www.cocoos.net
