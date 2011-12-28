#Apache <small>The web server</small>

## Symlinks

Symlinks are easy: `ln -sf /source /destination`

Enable `FollowSymLinks` in the */etc/httpd/conf/httpd.conf* or */etc/apache2/apache2.conf* file as per your distro:

<pre class="prettyprint linenums">
Alias /myalias "/home/my/folder"
<Directory "/home/my/folder">
    Options Indexes FollowSymLinks
    AllowOverride None
    Order allow,deny
    Allow from all
</Directory>
</pre>

## Curing 403

In case of Fedora/RHEL, SELinux is enforce on Apache (httpd). First check the permission that are off, then set the permission you want to true, then recheck and restart httpd; for example:

<pre class="prettyprint linenums">
sestatus -b | grep httpd | grep off$
setsebool httpd_enable_homedirs true
sestatus -b | grep httpd | grep on$
sudo service httpd restart
</pre>
