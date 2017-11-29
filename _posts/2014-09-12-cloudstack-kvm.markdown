---
layout: post
category: cloudstack
highlight: primary
title: How to setup CloudStack with KVM host
redirect_from: "/logs/cloudstack-kvm/"
---

This is a how-to guide on setting up CloudStack on Ubuntu and with KVM host in
basic zone, basic networking but with security groups. You may do this on a VM
or an actual host. This post covers setting up a CloudStack cloud on one box,
which means we'll install everything from CloudStack management server to agent
to MySQL to NFS, all in one box. My motivation was to create a VM with which I
can build, test Apache CloudStack (ACS) locally and show that one can do it in
less than 30 minutes given ample bandwidth.

Note: this should work for ACS 4.5.0 and above. This how-to post may get
outdated in future, so please
[read the latest docs](http://docs.cloudstack.apache.org/projects/cloudstack-installation/en/latest/)
and/or [read the latest docs on KVM host installation](http://cloudstack-installation.readthedocs.org/en/latest/hypervisor/kvm.html).

First install Ubuntu 14.04 LTS x86_64 on a system that has at least 2G RAM,
preferably 4GB RAM, and with a 64-bit CPU that has Intel VT-x or AMD-V enabled.
I personally use VMWare Fusion which can give VMs CPU with Intel VT-x which is
needed by KVM to do HVM. Too bad VirtualBox cannot do this yet, or one can say
KVM cannot do paravirtualization (like Xen).

Next, we need to do bunch of things:

- Setup networking, IPs, create bridge
- Install cloudstack-management and cloudstack-common
- Install and setup MySQL server
- Setup NFS for primary and secondary storages
- Preseed systemvm templates
- Prepare KVM host and install cloudstack-agent
- Configure Firewall
- Start your cloud!

Let's start by installing some basic packages, assuming you're root or have `sudo`
powers:

```bash
    apt-get install openntpd openssh-server sudo vim htop tar build-essential
```

Make sure root is able to ssh using password, fix in /etc/ssh/sshd_config.

Reset `root` password and remember this password:

    passwd root

### Networking

Ubuntu s**ks at configuring networking, you need to reboot everytime to apply
changes, and they don't have systemd yet. Though, its Unity feels more
stable than Gnome3, but then again it's the distribution that does not even say
that it's GNU/Linux (go ahead, read their [website](https://ubuntu.com)).
For good or bad reasons, it's one of the most popular distros. Enough of
trolling, let's configure some network.

We'll be setting up bridges and not OpenVswitch because I like bridges and they
seem to work for people for several years now. CloudStack requires that KVM
hosts have two bridges `cloudbr0` and `cloudbr1` which is because these names
are sort of hard coded in the code and on the KVM host we need to have a way
to let VMs communicate to the host, between themselves and reach the outside
world etc. Add network rules and configure IPs as applicable.

    apt-get install bridge-utils

    cat /etc/network/interfaces # an example bridge configuration

    auto lo
    iface lo inet loopback

    auto eth0
    iface eth0 inet manual

    # Public network
    auto cloudbr0
    iface cloudbr0 inet static
        address 172.16.154.10
        netmask 255.255.255.0
        gateway 172.16.154.2
        dns-nameservers 172.16.154.2 8.8.8.8
        bridge_ports eth0
        bridge_fd 5
        bridge_stp off
        bridge_maxwait 1

    # Private network
    auto cloudbr1
    iface cloudbr1 inet manual
        bridge_ports none
        bridge_fd 5
        bridge_stp off
        bridge_maxwait 1

Notice, we're not using `cloudbr1` because the intention is to setup basic
zone, basic networking, so all networking going through one bridge only.

We're done with setting up networking, just note the cloudbr0 IP. In my case, it
was `172.16.154.10`. You may notice that we're not configuring eth0 at all, it's
because we've a bridge now and we expose this bridge to the outside networking
using this cloudbr0's IP. By removing IP from eth0 (static or dhcp), we get
ubuntu to use cloudbr0 as its default interface and use cloudbr0's gateway
as its default gateway and route. Silly Ubuntu, you need to reboot your VM or
host now.

## Management server and MySQL

Setup CloudStack repo, you may use something that I host (the link is unreliable,
let me know if it stops working for you). You may use any other debian repo as
well. One can also build from source and [host their own repositories](http://cloudstack-installation.readthedocs.org/en/latest/installation.html#configure-package-repository).

We need to install the CloudStack management server, MySQL server and setup
the management server database:

    echo deb http://packages.shapeblue.com/cloudstack/upstream/debian/4.5 / >> /etc/apt/sources.list.d/acs.list
    apt-get update -y
    apt-get install cloudstack-management cloudstack-common mysql-server
    # pick any suitable root password for MySQL server

You don't need to explicitly install `cloudstack-common` because the management
package depends on it. This is to point out that many tools, scripts can be found
in this package, such as tools to setup database, preseed systemvm template etc.

You may put following rules on your /etc/mysql/my.cnf, they are mostly to configure
innodb settings and have MySQL use the bin-log "ROW" format which can be useful
for replication etc. Since we're doing only test setup we may skip this, even
though CloudStack docs say that you put only this but I think on production
systems you may need to configure many more options (perhaps 400 of those).

    [mysqld]
    innodb_rollback_on_timeout=1
    innodb_lock_wait_timeout=600
    max_connections=350
    log-bin=mysql-bin
    binlog-format = 'ROW'

Now, let's setup managment server database;

    service mysql restart
    cloudstack-setup-databases cloud:cloudpassword@localhost --deploy-as=root:passwordOfRoot -i <stick your cloudbr0 IP here>

### Storage

We'll setup NFS and preseed systemvm.

    mkdir -p /export/primary /export/secondary
    apt-get install nfs-kernel-server quota
    echo /export  *(rw,async,no_root_squash,no_subtree_check) > /etc/exports
    exportfs -a
    sed -i -e 's/^RPCMOUNTDOPTS="--manage-gids"$/RPCMOUNTDOPTS="-p 892 --manage-gids"/g' /etc/default/nfs-kernel-server
    sed -i -e 's/^NEED_STATD=$/NEED_STATD=yes/g' /etc/default/nfs-common
    sed -i -e 's/^STATDOPTS=$/STATDOPTS="--port 662 --outgoing-port 2020"/g' /etc/default/nfs-common
    sed -i -e 's/^RPCRQUOTADOPTS=$/RPCRQUOTADOPTS="-p 875"/g' /etc/default/quota
    service nfs-kernel-server restart

I prefer to download the systemvm first and then preseed it:

    wget http://packages.shapeblue.com/systemvmtemplate/4.5/4.5.2/systemvm64template-4.5-kvm.qcow2.bz2
    /usr/share/cloudstack-common/scripts/storage/secondary/cloud-install-sys-tmplt \
              -m /export/secondary -f systemvm64template-4.5-kvm.qcow2.bz2 -h kvm \
              -o localhost -r cloud -d cloudpassword

## KVM and agent setup

Time to setup cloudstack-agent, libvirt and KVM:

    apt-get install qemu-kvm cloudstack-agent
    sed -i -e 's/listen_tls = 1/listen_tls = 0/g' /etc/libvirt/libvirtd.conf
    echo 'listen_tcp=1' >> /etc/libvirt/libvirtd.conf
    echo 'tcp_port = "16509"' >> /etc/libvirt/libvirtd.conf
    echo 'mdns_adv = 0' >> /etc/libvirt/libvirtd.conf
    echo 'auth_tcp = "none"' >> /etc/libvirt/libvirtd.conf
    sed -i -e 's/\# vnc_listen.*$/vnc_listen = "0.0.0.0"/g' /etc/libvirt/qemu.conf
    sed -i -e 's/libvirtd_opts="-d"/libvirtd_opts="-d -l"/' /etc/init/libvirt-bin.conf
    service libvirt-bin restart

### Firewall

Finally punch in holes on the firewall, free 'em ports, substitute your network
in the following:

    # configure iptables
    NETWORK=172.16.154.0/24
    iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 111 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 111 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 2049 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 32803 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 32769 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 892 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 892 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 875 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 875 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 662 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 662 -j ACCEPT

    apt-get install iptables-persistent

    # Disable apparmour on libvirtd
    ln -s /etc/apparmor.d/usr.sbin.libvirtd /etc/apparmor.d/disable/
    ln -s /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper /etc/apparmor.d/disable/
    apparmor_parser -R /etc/apparmor.d/usr.sbin.libvirtd
    apparmor_parser -R /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper

    # Silly ufw
    ufw allow mysql
    ufw allow proto tcp from any to any port 22
    ufw allow proto tcp from any to any port 1798
    ufw allow proto tcp from any to any port 16509
    ufw allow proto tcp from any to any port 5900:6100
    ufw allow proto tcp from any to any port 49152:49216

### Launch Cloud

All set! Make sure tomcat is not running, start the agent and management server:

    /etc/init.d/tomcat6 stop
    /etc/init.d/cloudstack-agent start
    /etc/init.d/cloudstack-management start

If all goes well, open http://cloudbr0-IP:8080/client and you'll see the ACS login page.
Use username `admin` and password `password` to log in. Now setup a basic zone,
in the following steps change the IPs as applicable:

- Pick zone name, DNS 172.16.154.2, External DNS 8.8.8.8, basic zone + SG
- Pick pod name, gateway 172.16.154.2, netmask 255.255.255.0, IP range 172.16.154.200-250
- Add guest network, gateway 172.16.154.2, netmask 255.255.255.0, IP range 172.16.154.100-199
- Pick cluster name, hypervisor KVM
- Add the KVM host, IP 172.16.154.10, user root, password whatever-the-root-password-is
- Add primary NFS storage, IP 172.16.154.10, path /export/primary
- Add secondary NFS storage, IP 172.16.154.10, path /export/secondary
- Hit launch, if everything goes well launch your zone!

Keep an eye on your `/var/log/cloudstack/management/management-server.log` and
`/var/log/cloudstack/agent/agent.log` for possible issues. [Read the admin
docs](http://cloudstack-administration.readthedocs.org/en/latest/index.html)
for more cloudy admin tasks. Have fun hacking your CloudStack cloud.
