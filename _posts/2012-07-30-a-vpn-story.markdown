---
layout: post
title: A VPN Story
excerpt: Politics of free Internet in academia
---

This is a WIP post, will update/edit and post some more pics when I'll have some free time. I wanted to write about it for long and finally I've the courage to write about it. It's about my experience in IIT-BHU where I earned my engineering degrees. Please note that the word _free_ and freedom is used interchangeably. This blog post may be harsh, I did not intend to defame anyone, just the truth

During my time at IIT-BHU, Internet was primarily distributed by computer center (CC) there, which was terrible slow and with a lot of restrictions and banned ports (ssh, ftp, *). So, most of us gitsters would buy a wireless broadband connection, the purpose of CC to provide Internet services to students was defeated by their own people.

Around Nov-Dec 2011, my hacker friend _pk_ and I came in contact with WMG, the Website Management Group of IIT-BHU. At that time WMG had an unused NKN node and bunch of servers. NKN, the National Knowledge Network, is an initiative by Indian govt. to connect all the major educational instituions and govt bodies. Unlike the copper wire based Internet infrastructure that most of us use in daily life, the NKN node used fibre optics and had a bandwidth of 100Mbps. It was awesome, the first thing we did was get a Linux box and install squid proxy server on it and share the credentials between the student crowd. It was an overnight success. Almost everyone was using it.

During the next few days we contacted NKN to upgrade our bandwidth to 1Gbps, the only requirement was to show adequate consumption of bandwidth. So, we setup an Ubuntu repository mirror and the proxy server was running and we got it upgraded to 1Gbps.

During next few months, we found that a proxy server is still not a good solution and we setup a VPN server on that link. Our stack consisted of some python scripts, cron jobs, openvpn-server on RHEL. Below is a screenshot of the [VPN Monitor](https://github.com/bhaisaab/hacktools/tree/master/vpnmon) I wrote.
<br><center><img align="center" src="/images/vpnstats.png"></center><br>

Sadly, the CC rejected our application to open 1Gbps ports for our servers, whatever excuse they must have given I just remember that they did not want anyone to have good Internet bandwidth. So, even if we'd 1Gbps, we were only able to serve 100Mbps to hostels and departments as all the switches have that limit.

When something new and innovative disrupts and challenges the workflow of life, there are haters and supporters. I faced the same kind of situation, we all did, all four of us who were involved with our little VPN project. Almost everyone on the IIT-BHU campus was using it. I was happy with it. But it did not last very long.

Long story short, a friend who was involved with us and had issues with a big guy nuked the server which later on I had to re-setup and I ended up losing a hacker friend. Anyway when I graduated and left the place, some legal issue came up that had to do with logging the VPN servers and the big guy shut down the VPN servers. CC 1, Internet 0.
