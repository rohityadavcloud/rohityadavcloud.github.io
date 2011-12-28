#Bind9 <small>Using as caching DNS server</small>

**Bind9** can be configured as a caching dns server to speed up slow Internet, especially when you've low bandwidth. First install `bind9` and dns utilities:

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

Finally, test your BIND DNS caching server: `dig yadav.im`

Over the time the query time for most frequent domains will reduce anywhere from 2000msec to 0msec :)

