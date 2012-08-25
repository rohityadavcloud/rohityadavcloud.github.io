---
layout: post
title: An Internet Story
excerpt: Politics of free Internet in BHU
---

During my time at IIT-BHU, Internet was primarily distributed by computer center (CC) there, which was terrible slow and with a lot of restrictions and banned ports (ssh, ftp etc). So, most of us opensource guys would buy a wireless broadband connection. The purpose of CC to provide Internet services to the students, was defeated by their own people.

Around Nov-Dec 2011, my hacker friend _pk_ and I came in contact with WMG, the Website Management Group of IIT-BHU which manages institutional website and some infrastructure. At that time WMG had an unused NKN node and bunch of servers. NKN, the National Knowledge Network, is an initiative by Indian govt. to connect all the major educational instituions and govt bodies. Unlike the copper wire based Internet infrastructure that most of us use in daily life, the NKN node used fibre optics and had a bandwidth of 100Mbps. It was awesome, the first thing we did was get a Linux box and install squid proxy server on it and share the credentials between the student crowd. It was an overnight success. Almost everyone on campus was using it.

During the next few days we contacted NKN to upgrade our bandwidth to 1Gbps, the only requirement was to show adequate consumption of bandwidth. So, we setup an Ubuntu repository mirror and the proxy server was already running; this way we got it upgraded to 1Gbps in about a week or two.

During the next few months, we found that a proxy server was not a good solution for most of us and so we setup an OpenVPN server. Our stack consisted of some python scripts, cron jobs, openvpn-server on RHEL. Below is a screenshot of the [VPN Monitor](https://github.com/bhaisaab/hacktools/tree/master/vpnmon) I wrote.

<br><center><img align="center" src="/images/vpnstats.png"></center><br>

We were very successful, by that time most students and even some faculty members were using the VPN we'd setup. Next problem we solved was scalability, we ran about 16 OpenVPN servers to tackle that and we requested the CC to open 1Gbps ports on their provided gateway/switches for our servers, sadly it was denied. Whatever excuse they may have given I just remember that they did not want anyone to have good Internet bandwidth. So, even if we'd 1Gbps link we were only able to serve 100Mbps of it to hostels and departments.

When something new and innovative disrupts and challenges the workflow of life, there are haters and supporters. I faced the same kind of situation, we all did, all four of us who were involved with our little VPN project. Almost everyone on the IIT-BHU campus was using it, at one time I'd seen at least 1000 people using it. I'm happy with what we did and what we achieved and learnt during the whole chaos. And I've something to brag about ;)

Long story short, a friend who was involved with us and had issues with a big guy nuked the server which later on I had to re-setup and I ended up losing a hacker friend. Anyway when I graduated and left the place, some legal issue came up that had to do with logging the VPN servers and the big guy shut down the VPN servers.
