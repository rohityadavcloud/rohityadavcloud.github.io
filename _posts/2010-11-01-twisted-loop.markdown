---
layout: post
title: Twisted Fun
excerpt: Asynchronous networking
---

After hours of hacking through the source code of [BoincVM][david-boincvm] originally written by [David][david] I finally understood the architecture and workflow. I'm rewriting it (it's now called [VMController][]), fixing bugs and implementing features as part of my [B.Tech][btech] project, with some help from David (the amazing). [Twisted][twisted] is awesome, it's an event-driven networking engine written in Python which we're using in [VMController][]. Below is a cartoon I drew on my whiteboard to show what's really going on inside VMController (host). Read the [report](/files/docs/btp-report-vmcontroller.pdf).

<p align="center"><img align="center" src="/images/vmcontroller-host.jpg"></p>

[btech]: http://en.wikipedia.org/wiki/Bachelor_of_Technology
[david-boincvm]: http://bitbucket.org/dgquintas/boincvm
[david]: http://www.linkedin.com/in/davidgarciaquintas
[twisted]: http://twistedmatrix.com/
[VMController]: http://code.google.com/p/vmcontroller
