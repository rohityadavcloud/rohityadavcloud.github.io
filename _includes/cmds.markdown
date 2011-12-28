##Contents

* [SSH](#ssh)
  * [Tunnelling and Port Forwarding](#ssh_tunneling)
  * [SOCKS Proxy](#ssh_proxy)
* [Git](#git)
  * [GitHub Over HTTP](#git_corkscrew)

<br>

##<span id="ssh">SSH <small>Useful SSH commands</small></span>

###<span id="ssh_tunneling">Tunnelling and Port Forwarding</span>

`ssh -L 2080:cvmappi09.cern.ch:80  <username>@lxplus.cern.ch`

`ssh -p 2080 username@localhost`

###<span id="ssh_proxy">SOCKS Proxy</span>

Simply use localhost and port=8080 as SOCKS proxy host and port and run:

`ssh -C2qTnN -D 8080 username@server`

NOTE: In Firefox (config:about) set the `network.proxy.socks_remote_dns` to *true*.

##<span id="git">Git <small>The amazing version control system</small></span>

###<span id="git_corkscrew">GitHub Over HTTP</span>

Accessing Github using *corkscrew* over HTTP/S, put the following in `~/.ssh/config`:
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
