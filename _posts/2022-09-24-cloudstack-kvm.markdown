---
layout: post
category: cloudstack
highlight: primary
title: Apache CloudStack on Ubuntu with x86_64 KVM
redirect_from: "/logs/cloudstack-kvm/"
---

This is a build your own IaaS cloud guide on setting up a Apache CloudStack
based cloud on a single Ubuntu 18.04/20.04/22.04 (LTS) host that is also used
as a KVM host.

Note: this should work for ACS 4.16 and above, has been updated against ACS 4.17
release. This how-to post may get outdated in future, so please [follow the
latest docs](http://docs.cloudstack.apache.org/en/4.17.1.0/installguide) and/or
[read the latest docs on KVM host
installation](http://docs.cloudstack.apache.org/en/4.17.1.0/installguide/hypervisor/kvm.html).

# Initial Setup

First install Ubuntu 18.04/20.04/22.04 LTS on your x86_64 system that has at
least 4GB RAM (prerably 8GB or more) with Intel VT-X or AMD-V enabled. Ensure
that the `universe` repository is enabled in `/etc/apt/sources.list`.

Install basic packages:

    apt-get install openntpd openssh-server sudo vim htop tar

Optionally, if you've Intel based system install/update CPU microcode:

    apt-get install intel-microcode

Allow the root user for ssh access using password, fix `/etc/ssh/sshd_config`.
Change and remember the `root` password:

    passwd root

# Setup Networking

Setup Linux bridges that will handle CloudStack's public, guest, management
and storage traffic. For simplicity, we will use a single bridge `cloudbr0` to
be used for all these networks. Install bridge utilities:

    apt-get install bridge-utils

This guide assumes that you're in a 192.168.1.0/24 network which is a typical
RFC1918 private network.

### Ubuntu 18.04/20.04

Starting Ubuntu bionic, admins can use `netplan` to configure networking. The
default installation creates a file at `/etc/netplan/50-cloud-init.yaml` that
you should comment, and create a file at `/etc/netplan/01-netcfg.yaml` applying
your network specific changes:

     network:
       version: 2
       renderer: networkd
       ethernets:
         eno1:
           dhcp4: false
           dhcp6: false
           optional: true
       bridges:
         cloudbr0:
           addresses: [192.168.1.10/24]
           gateway4: 192.168.1.1
           nameservers:
             addresses: [1.1.1.1,8.8.8.8]
           interfaces: [eno1]
           dhcp4: false
           dhcp6: false
           parameters:
             stp: false
             forward-delay: 0

Note: If you want to use VXLAN based traffic isolation, make sure to increase the MTU setting of the physical nics by `50 bytes` (because VXLAN header size is 50 bytes). For example:

```
  ethernets:
    enp2s0:
      match:
        macaddress: 00:01:2e:4f:f7:d0
      mtu: 1550
      dhcp4: false
      dhcp6: false
    enp3s0:
      mtu: 1550
```

Save the file and apply network config, finally reboot:

    netplan generate
    netplan apply
    reboot

# CloudStack Management Server Setup

Install CloudStack management server and MySQL server: (run as root)

    apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BDF0E176584DF93F
    echo deb http://packages.shapeblue.com/cloudstack/upstream/debian/4.17 / > /etc/apt/sources.list.d/cloudstack.list
    apt-get update -y
    apt-get install cloudstack-management mysql-server

Optionally, if you want usage server you can run:

    apt-get install cloudstack-usage 

Make a note of the MySQL server's root user password. Configure InnoDB settings
in mysql server's `/etc/mysql/mysql.conf.d/mysqld.cnf`:

    [mysqld]

    server_id = 1
    sql-mode="STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,ERROR_FOR_DIVISION_BY_ZERO,NO_ZERO_DATE,NO_ZERO_IN_DATE,NO_ENGINE_SUBSTITUTION"
    innodb_rollback_on_timeout=1
    innodb_lock_wait_timeout=600
    max_connections=1000
    log-bin=mysql-bin
    binlog-format = 'ROW'

Restart MySQL server and setup database:

    systemctl restart mysql
    cloudstack-setup-databases cloud:cloud@localhost --deploy-as=root:<root password, default blank> -i <cloudbr0 IP here>

# Storage Setup

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

NOTE: The following is no longer necessary for CloudStack 4.16 and above as CloudStack management server does this
automatically. For older versions, the `cloud-install-sys-tmplt` script can be used to seed the systemvmtemplate.
For example, here's the command to use for version 4.16:

    wget http://packages.shapeblue.com/systemvmtemplate/4.16/systemvmtemplate-4.16.1-kvm.qcow2.bz2
    /usr/share/cloudstack-common/scripts/storage/secondary/cloud-install-sys-tmplt \
              -m /export/secondary -f systemvmtemplate-4.16.1-kvm.qcow2.bz2 -h kvm \
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

For Ubuntu 20.04/22.04 and later, the traditional socket/listen based configuration
may not be supported, we can get the old behaviour as follows:

    systemctl mask libvirtd.socket libvirtd-ro.socket libvirtd-admin.socket libvirtd-tls.socket libvirtd-tcp.socket
    systemctl restart libvirtd

On certain hosts where you may be running docker and other services, you may need to
add the following in `/etc/sysctl.conf` and then run `sysctl -p`:

    net.bridge.bridge-nf-call-arptables = 0
    net.bridge.bridge-nf-call-iptables = 0

Optional: If you've a crappy server vendor, they may fail to make each server
unique and libvirtd can complain that servers are not unique. To make them
unique setup host specific UUID in libvirtd config:

    apt-get install uuid
    UUID=$(uuid)
    echo host_uuid = \"$UUID\" >> /etc/libvirt/libvirtd.conf
    systemctl restart libvirtd

Note: For older CloudStack v4.15 and older, when adding KVM host (default, via ssh)
it may fail on newer distros which have OpenSSH version 7+ that has deprecated some
legacy algorithms. To fix that the `sshd_config` on the KVM host may temporarily
be changed to following before adding the KVM host in CloudStack: (you may remove this and restart sshd
after adding the KVM host)

    PubkeyAcceptedKeyTypes=+ssh-dss
    HostKeyAlgorithms=+ssh-dss
    KexAlgorithms=+diffie-hellman-group1-sha1

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
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 8250 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 8080 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 9090 -j ACCEPT
    iptables -A INPUT -s $NETWORK -m state --state NEW -p tcp --dport 16514 -j ACCEPT

    apt-get install iptables-persistent

    # Disable apparmour on libvirtd
    ln -s /etc/apparmor.d/usr.sbin.libvirtd /etc/apparmor.d/disable/
    ln -s /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper /etc/apparmor.d/disable/
    apparmor_parser -R /etc/apparmor.d/usr.sbin.libvirtd
    apparmor_parser -R /etc/apparmor.d/usr.lib.libvirt.virt-aa-helper

If your system uses `ufw` instead (you can check using `ufw status`), run the
following:

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

After management server is UP, proceed to http://`192.168.1.10(i.e. the cloudbr0-IP)`:8080/client
and log in using the default credentials - username `admin` and password
`password`.

# Deploying Advanced Zone

The following is an example of how you can setup an advanced zone in the
192.168.1.0/24 network.

### Setup Zone

Go to Infrastructure > Zone and click on add zone button, select advanced zone and
provide following configuration:

    Name - any name
    Public DNS 1 - 8.8.8.8
    Internal DNS1 - 192.168.1.1
    Hypervisor - KVM

### Setup Network

Use the default, which is `VLAN` isolation method on a single physical nic (on
the host) that will carry all traffic types (management, public, guest etc).

Note: If you've `iproute2` installed and host's physical NIC MTUs configured, you can used `VXLAN` as well.

Public traffic configuration:

    Gateway - 192.168.1.1
    Netmask - 255.255.255.0
    VLAN/VNI - (leave blank for vlan://untagged or in case of VXLAN use vxlan://untagged)
    Start IP - 192.168.1.20
    End IP - 192.168.1.50

Pod Configuration:

    Name - any name
    Gateway - 192.168.1.1
    Start/end reserved system IPs - 192.168.1.51 - 192.168.1.80

Guest traffic:

    VLAN/VNI range: 700-900

### Add Resources

Create a cluster with following:

    Name - any name
    Hypervisor - Choose KVM

Add your default/first host:

    Hostname - 192.168.1.10
    Username - root
    Password - <password for root user, please enable root user ssh-access by password on the KVM host>

Note: `root` user ssh-access is disabled by default, [please enable it](https://askubuntu.com/questions/469143/how-to-enable-ssh-root-access-on-ubuntu-14-04). To add the KVM host using ssh-access, add the management server SSH public key at /var/cloudstack/management/.ssh/id_rsa.pub to the root user at /root/.ssh/authorized_keys.

Add primary storage:

    Name - any name
    Scope - zone-wide
    Protocol - NFS
    Server - 192.168.1.10
    Path - /export/primary

Add secondary storage:

    Provider - NFS
    Name - any name
    Server - 192.168.1.10
    Path - /export/secondary

Next, click `Launch Zone` which will perform following actions:

    Create Zone
    Create Physical networks:
      - Add various traffic types to the physical network
      - Update and enable the physical network
      - Configure, enable and update various network provider and elements such as the virtual network element
    Create Pod
    Configure public traffic
    Configure guest traffic (vlan range for physical network)
    Create Cluster
    Add host
    Create primary storage (also mounts it on the KVM host)
    Create secondary storage
    Complete zone creation

Finally, confirm and enable the zone. Wait for the system VMs to come up, then
you can proceed with your IaaS usage. Happy hacking!
