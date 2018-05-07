---
layout: post
category: cloudstack
highlight: primary
title: Install CloudStack 4.11 with KVM Ubuntu
redirect_from: "/logs/cloudstack-kvm/"
---

This is a how-to install guide on setting up Apache CloudStack all in a single
Ubuntu 18.04 host that is also used as a KVM host.

Note: this should work for ACS 4.11.1 and above. This how-to post may get
outdated in future, so please [follow the latest docs](http://docs.cloudstack.apache.org/projects/cloudstack-installation/en/latest/)
and/or [read the latest docs on KVM host installation](http://cloudstack-installation.readthedocs.org/en/latest/hypervisor/kvm.html).

# Initial Setup

First install Ubuntu 16.04/18.04 LTS on your x86_64 system that has at least
4GB RAM (prerably more) with Intel VT-X or AMD-V enabled.

Install basic packages:

    apt-get install openntpd openssh-server sudo vim htop tar

Optionally, if you've Intel based system install/update CPU microcode:

    apt-get install microcode.ctl intel-microcode

Allow the root user for ssh access using password, fix `/etc/ssh/sshd_config`.
Change and remember the `root` password:

    passwd root

# Setup Networking

Setup Linux bridges that will handle CloudStack's public, guest, management
and storage traffic. For simplicity, we will use a single bridge `cloudbr0` to
be used for all these networks. Install bridge utilities:

    apt-get install bridge-utils

### Ubuntu 16.04

To configure bridge on Ubuntu 16.04, make suitable changes to
`/etc/network/interfaces`:

    auto lo
    iface lo inet loopback

    auto enp2s0
    iface enp2s0 inet manual

    auto cloudbr0
    iface cloudbr0 inet static
        address 192.168.1.10
        netmask 255.255.255.0
        gateway 192.168.1.1
        dns-nameservers 1.1.1.1
        bridge_ports enp2s0
        bridge_fd 0
        bridge_stp off

Restart networking or reboot the host to enforce network settings.

### Ubuntu 18.04

Starting Ubuntu bionic, admins can use `netplan` to configure networking. The
default installation creates a file at `/etc/netplan/50-cloud-init.yaml` which
you can comment, and create a file at `/etc/netplan/01-netcfg.yaml` applying
your network specific changes:

     network:
       version: 2
       renderer: networkd
       ethernets:
         enp2s0:
           dhcp4: false
           dhcp6: false
           optional: true
       bridges:
         cloudbr0:
           addresses: [192.168.1.10/24]
           gateway4: 192.168.1.1
           nameservers:
             addresses: [1.1.1.1,8.8.8.8]
           interfaces: [enp2s0]
           dhcp4: false
           dhcp6: false
           parameters:
             stp: false
             forward-delay: 0

Save the file and apply network config, finally reboot:

    netplan generate
    netplan apply
    reboot

# Setup Management Server

Install CloudStack management server and MySQL server: (run as root)

    apt-key adv --keyserver keys.gnupg.net --recv-keys 584DF93F
    echo deb http://packages.shapeblue.com/cloudstack/upstream/debian/4.11 / > /etc/apt/sources.list.d/cloudstack.list
    apt-get update -y
    apt-get install cloudstack-management mysql-server

Make a note of the MySQL server's root user password. Configure InnoDB settings
in mysql server's `/etc/mysql/mysql.conf.d/mysqld.cnf`:

    [mysqld]

    server_id = 1
    innodb_rollback_on_timeout=1
    innodb_lock_wait_timeout=600
    max_connections=1000
    log-bin=mysql-bin
    binlog-format = 'ROW'

Restart MySQL server and setup database:

    systemctl restart mysql
    cloudstack-setup-databases cloud:cloud@localhost --deploy-as=root:<root-user-password> -i <cloudbr0 IP here>

# Setup Storage

Install NFS server:

    apt-get install nfs-kernel-server quota

Create exports:

    echo "/export  *(rw,async,no_root_squash,no_subtree_check)" > /etc/exports
    mkdir -p /export/primary /export/secondary
    exportfs -a

Configure and restart NFS server:

    sed -i -e 's/^RPCMOUNTDOPTS="--manage-gids"$/RPCMOUNTDOPTS="-p 892 --manage-gids"/g' /etc/default/nfs-kernel-server
    sed -i -e 's/^STATDOPTS=$/STATDOPTS="--port 662 --outgoing-port 2020"/g' /etc/default/nfs-common
    echo "NEED_STATD=yes" >> /etc/default/nfs-common
    sed -i -e 's/^RPCRQUOTADOPTS=$/RPCRQUOTADOPTS="-p 875"/g' /etc/default/quota
    service nfs-kernel-server restart

Seed systemvm template:

    wget http://packages.shapeblue.com/systemvmtemplate/4.11/systemvmtemplate-4.11.1-kvm.qcow2.bz2
    /usr/share/cloudstack-common/scripts/storage/secondary/cloud-install-sys-tmplt \
              -m /export/secondary -f systemvmtemplate-4.11.0-kvm.qcow2.bz2 -h kvm \
              -o localhost -r cloud -d cloud

# Setup KVM host

Install KVM and CloudStack agent, configure libvirt:

    apt-get install qemu-kvm cloudstack-agent

Enable VNC for console proxy:

    sed -i -e 's/\#vnc_listen.*$/vnc_listen = "0.0.0.0"/g' /etc/libvirt/qemu.conf

Enable libvirtd in listen mode:

    sed -i -e 's/.*libvirtd_opts.*/libvirtd_opts="-l"/' /etc/default/libvirtd

Configure default libvirtd config:

    echo 'listen_tls=0' >> /etc/libvirt/libvirtd.conf
    echo 'listen_tcp=1' >> /etc/libvirt/libvirtd.conf
    echo 'tcp_port = "16509"' >> /etc/libvirt/libvirtd.conf
    echo 'mdns_adv = 0' >> /etc/libvirt/libvirtd.conf
    echo 'auth_tcp = "none"' >> /etc/libvirt/libvirtd.conf
    systemctl restart libvirtd

Optional: If you've a crappy server vendor, they may fail to make each server
unique and libvirtd can complain that servers are not unique. To make them
unique setup host specific UUID in libvirtd config:

    apt-get install uuid
    UUID=$(uuid)
    echo host_uuid = \"$UUID\" >> /etc/libvirt/libvirtd.conf
    systemctl restart libvirtd

Note: In Ubuntu 18.04, the libvirt daemon process has been named libvirtd but
libvirt-bin alias is also available.

# Configure Firewall

Configure firewall:

    # configure iptables
    NETWORK=192.168.1.0/24
    iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 111 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 111 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 2049 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 32803 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p udp --dport 32769 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 892 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 875 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 662 -j ACCEPT

    apt-get install iptables-persistent

    # Disable apparmour on libvirtd
    ln -s /etc/apparmor.d/usr.sbin.libvirtd /etc/apparmor.d/disable/
    ln -s /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper /etc/apparmor.d/disable/
    apparmor_parser -R /etc/apparmor.d/usr.sbin.libvirtd
    apparmor_parser -R /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper

Enable using `ufw` if that is enabled and what you're using:

    ufw allow mysql
    ufw allow proto tcp from any to any port 22
    ufw allow proto tcp from any to any port 1798
    ufw allow proto tcp from any to any port 16509
    ufw allow proto tcp from any to any port 16514
    ufw allow proto tcp from any to any port 5900:6100
    ufw allow proto tcp from any to any port 49152:49216

### Launch Management Server

Start your cloud:

    cloudstack-setup-management
    systemctl status cloudstack-management
    tail -f /var/log/cloudstack/management/management-server.log

After management server is UP, proceed to http://`cloudbr0-IP`:8080/client
and log in using the default credentials - username `admin` and password
`password`.

Now, deploy your zone!
