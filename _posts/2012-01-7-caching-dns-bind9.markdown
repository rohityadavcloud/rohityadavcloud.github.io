---
layout: post
title: Bind9 as a caching DNS server
excerpt: tricks to make surfing appear fast
---

*Bind*, a widely used DNS server, can be configured as a caching dns server to speed up slow Internet surfing experience, especially when you've low bandwidth. First install `bind9` and dns utilities:

<pre class="prettyprint linenums">
sudo apt-get update
sudo apt-get install bind9 dnsutils
</pre>

Now, simply point your ISP's DNS server in `/etc/bind/named.conf.options`:

<pre class="prettyprint linenums">
  [...]
  forwarders {
       10.1.1.11;
  };
  [...]
</pre>

Now restart the bind daemon: `sudo /etc/init.d/bind9 restart`

And, point your nameserver in /etc/resolv.conf to your DNS server's IP address:

<pre class="prettyprint linenums">
vim /etc/resolv.conf
add "nameserver 127.0.0.1" to this file
</pre>

Finally, test your BIND DNS caching server: `dig wikipedia.org`

Over the time the query time for most frequent domains will reduce anywhere from 5000msec to 0msec :)

