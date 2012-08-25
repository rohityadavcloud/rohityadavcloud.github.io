---
layout: post
title: BaagiChhaap
excerpt: Get yourself baagichhaap'd
---

The freaking IDD'07 watched a [movie](http://en.wikipedia.org/wiki/Paan_Singh_Tomar) and _Baagi_ became a popular [meme](http://en.wikipedia.org/wiki/Meme) among us.

<br><center><img align="center" src="/images/baagi/jain.gif"></center><br>

[Jain](http://rahuljain.org) is from Dholpur which is near the famous valleys of the [Chambal](http://en.wikipedia.org/wiki/Chambal_River) and having seen a bunch of photos of him posing with some country made rifle he was bound to become the baagi of our gang. And after a Saturday night dinner, the gang decided to do something about it, like open a _dhabha_ or something to do with that meme. Believe me the hobby of domain searching and buying is evil and I warned 'em but they all wanted it badly (intended exaggeration), all 0x7 of them (except tintin kookdookoo) so I booked 'em a [domain](http://baagi.org).

I had to come up with a prank and as Baagis are known to wear awesome mustaches, I thought let's take any picture and draw a [moochh](http://en.wikipedia.org/wiki/Mustache) on it, programmatically of course!

<br><center><img align="center" src="/images/baagi/baagichhaap.jpg"></center><br>

And after a fun Sunday evening with python and opencv I hacked up the prank, [baagichhaap](http://chhaap.baagi.org) that takes in a photo, tries to detect a face (red squares), a nose (blue squares) and a mouth (green squares) and based on the obtained information it draws a _moochh_ on it.

<br><center><a href="/images/baagi/baagichhaap-branch.jpg"><img align="center" src="/images/baagi/baagichhaap-branch.jpg"></a></center><br>

It even works with big images with large number of people in it, with some errors though, blame it on opencv. The above baagichhaap'd photograph was taken by ShowStopper's DSLR last year, some of the folks in it really look like they own their _moochh-es_.

We have an IRC channel #baagi on freenode if you care to hangout and we [log](http://baagi.org/irc)! The prank lives in our [baagi](https://github.com/baagi/baagichhaap) repository.
