---
layout: post
title: Damn Proxies
excerpt: Internet inside a proxy
---

It's an unnecessary overhead to do a lot of things inside (http) proxies. For example you cannot use services such as git, ssh, email/smtp, ftp right away when the only allowed ports are 80 and 443. To use Github over HTTP proxy you may use corkscrew over HTTPS; just put something like the following in your `~/.ssh/config`:

<pre class="prettyprint linenums">
Host gh
User git
Hostname ssh.github.com
Port 443
IdentityFile ~/.ssh/id_rsa
ProxyCommand corkscrew 10.1.1.18 80 %h %p ~/.ssh/proxyauth 
</pre>

Put `username:passwd` in the *~/.ssh/proxyauth* file. Now, simply use normal git cmds, such as:

`git clone gh:rohityadav/recipes.git`

In case you're lucky and have access to a computer that has unrestricted Internet (maybe your personal VPS), use tunnelling and port forwarding over ssh to connect to a particular host; for example:

`ssh -L 2080:cvmappi09.cern.ch:80  <username>@lxplus.cern.ch`

`ssh -p 2080 username@localhost`

Or, surf Internet over a [SOCKS proxy](http://en.wikipedia.org/wiki/SOCKS), for example:

`ssh -C2qTnN -D 8080 username@myserver -p 1123`

How do you use it in a browser, say Firefox? In Firefox open *about:config* set the `network.proxy.socks_remote_dns` field to *true* and in proxy setting leave everything blank and put *localhost* as SOCKS host and whatever *port* (8080 in the example) you used.

Assuming you've a socks proxy like the one above, you can use `proxychains` to force any application to use that socks proxy by configuring `/etc/proxychains.conf`, and setting a suitable DNS server in `/usr/lib/proxychains3/proxyresolv` (default is 4.2.2.2). Now `$ proxychains vlc` on *bash* and listen to that awesome Internet radio [station](http://listen.di.fm/public3/electro.pls).
