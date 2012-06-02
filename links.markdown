---
layout: page
title: Links
tagline: Sweets spots on the Internet
permalink: /links/
---

{% capture hackers %}

[Hacker News](http://news.ycombinator.com)

[Slashdot](http://slashdot.org)

[Hacker Street India](http://hackerstreet.in)

[FLOSS Weekly](http://twit.tv/floss)

[Wired](http://www.wired.com/)

##Amazing People

[j-b](http://www.jbkempf.com/blog/)

[Choquette](http://twitter.com/beauzeh)

[Etix](http://l0cal.com)

[DarkShikari](http://x264dev.multimedia.cx/)

[rms](http://stallman.org/)

[Notch](http://notch.tumblr.com/)

##Blogs and Websites

[Duartes: Kernel and CPU](http://duartes.org/gustavo/blog/best-of)

[Amjith: Python hacker](http://amjith.blogspot.in/)

{% endcapture %}

{% capture people %}

##Ol' Timers

[Abhishek "ShowStopper"][show]

[Saket "Tachyon"][tac]

[Abhinav Kushwaha][abhinav]

[Kushagra Gour][kushagra]

[Vivek "Vicky"][vivek]

[Rahul Jain "Baagi"][rahuljain]

[show]: http://theshowstopper.in
[tac]: http://saketsaurabh.in
[abhinav]: http://akush.in
[kushagra]: http://www.kushagragour.in
[vivek]: http://vyadav.in
[rahuljain]: http://rahuljain.org

##Programming

[ACM-ICPC](http://acm.uva.es/)

[UVa OL](http://uva.onlinejudge.org/)

[TopCoder](http://www.topcoder.com)

[SPOJ](http://www.spoj.pl/)

[SGU](http://acm.sgu.ru/)

[Timus](http://acm.timus.ru/)

[CodeChef](http://codechef.com)

[CodeForces](http://projecteuler.net/)

[Petr's blog](http://petr-mitrichev.blogspot.com/)

[Project Euler](http://projecteuler.net/)

[Come on Code On](http://comeoncodeon.wordpress.com/)

{% endcapture%}

<div class="row">
  <div class="span5">
    {{ hackers | markdownify }}
  </div>
  <div class="span5">
    {{ people | markdownify }}
  </div>
</div>
