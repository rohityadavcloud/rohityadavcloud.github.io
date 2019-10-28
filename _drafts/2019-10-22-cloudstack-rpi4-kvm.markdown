---
layout: post
category: cloudstack
highlight: primary
title: Apache CloudStack IaaS with KVM on Raspberry Pi 4
redirect_from: "/logs/cloudstack-raspberrypi-arm64-kvm/"
---

In this post, I explore and share my personal RaspberryPi4 based CloudStack IaaS
setup. Newer ARM64 boards such as Raspberry Pi 4 (with armv8, cortex-72
quad-core processor @ 1.5Ghz, with upto 4GB RAM) can run Linux kernel with
[KVM](https://www.linux-kvm.org/page/Processor_support#ARM:).

First install Ubuntu 19.10 [ARM64
image](http://cdimage.ubuntu.com/ubuntu/releases/19.10/release/) for Raspberry
Pi 4 (preferably 4GB RAM model) on a decent class 10, u3/v30 sdcard. I used
Samsung EVO+ 128GB micro sd card. And install Linux kernel images with KVM
enabled, for example from:

    https://people.canonical.com/~hwang4/pi4kvm/arm64/ (newer build, fixed USB issue)
    http://dl.rohityadav.cloud/cloudstack-rpi/kernel-19.10/

Install basic packages:

    apt-get install ntpdate openssh-server sudo vim htop tar iotop

Basic setup:
    ntpdate time.nist.gov # update time
    hostnamectl set-hostname cloudstack-mgmt

Disable automatic upgrades and unnecessary packages:

    apt-get remove --purge unattended-upgrades snapd cloud-init
    # Edit the file: /etc/apt/apt.conf.d/20auto-upgrades to the following
    APT::Periodic::Update-Package-Lists "0";
    APT::Periodic::Unattended-Upgrade "1";

Allow the root user for ssh access using password, fix `/etc/ssh/sshd_config`.
Change and remember the `root` password:

    passwd root

To reduce load on RaspberryPi4 sdcard by changing how soon changes are committed
to the disk, for example in `/etc/fstab`:

    LABEL=writable  /        ext4   defaults,commit=60      0 0
    LABEL=system-boot       /boot/firmware  vfat    defaults        0       1

# Setup Networking

Setup Linux bridges that will handle CloudStack's public, guest, management
and storage traffic. For simplicity, we will use a single bridge `cloudbr0` to
be used for all these networks. Install bridge utilities:

    apt-get install bridge-utils

This guide assumes that you're in a 192.168.1.0/24 network which is a typical
RFC1918 private network.

### Ubuntu 19.10

Starting Ubuntu bionic, admins can use `netplan` to configure networking. The
default installation creates a file at `/etc/netplan/50-cloud-init.yaml` which
you can comment, and create a file at `/etc/netplan/01-netcfg.yaml` applying
your network specific changes:

     network:
       version: 2
       renderer: networkd
       ethernets:
         eth0:
           dhcp4: false
           dhcp6: false
           optional: true
       bridges:
         cloudbr0:
           addresses: [192.168.1.10/24]
           gateway4: 192.168.1.1
           nameservers:
             addresses: [8.8.8.8]
           interfaces: [eth0]
           dhcp4: false
           dhcp6: false
           parameters:
             stp: false
             forward-delay: 0

Tip: If you want to use VXLAN based traffic isolation, make sure to increase the MTU setting of the physical nics by `50 bytes` (because VXLAN header size is 50 bytes). For example:

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

# CloudStack Patch

CloudStack by default does not work with ARM64, this guide was based on a custom
4.13.0.0 build that used this patch/pull-request:

    https://github.com/apache/cloudstack/pull/3644

# CloudStack Management Server Setup

Install CloudStack management server and MySQL server: (run as root)

    apt-key adv --keyserver keys.gnupg.net --recv-keys BDF0E176584DF93F
    echo deb http://packages.shapeblue.com/cloudstack/upstream/debian/4.13 / > /etc/apt/sources.list.d/cloudstack.list
    apt-get update
    apt-get install mariadb-server

Make a note of the MySQL server's root user password. Configure InnoDB settings
in `/etc/mysql/mariadb.conf.d/50-server.cnf`:

    [mysqld]

    server_id = 1
    sql-mode="STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,ERROR_FOR_DIVISION_BY_ZERO,NO_ZERO_DATE,NO_ZERO_IN_DATE,NO_ENGINE_SUBSTITUTION"
    innodb_rollback_on_timeout=1
    innodb_lock_wait_timeout=600
    max_connections=1000
    log-bin=mysql-bin
    binlog-format = 'ROW'

Restart database:

    systemctl restart mariadb

Installing management server may give dependency errors, so download and manually install:

    apt-get install cloudstack-common libslf4j-java
    wget http://mirrors.kernel.org/ubuntu/pool/universe/m/mysql-connector-java/libmysql-java_5.1.45-1_all.deb
    dpkg -i libmysql-java_5.1.45-1_all.deb
    apt-get download cloudstack-management
    dpkg -i cloudstack-management*.deb
    # edit /var/lib/dpkg/status and remove `mysql-client` from cloudstack-management section/dependencies
    apt-get install -f
    systemctl stop cloudstack-management # stop the automatic start after install

Setup database:

    cloudstack-setup-databases cloud:cloud@localhost --deploy-as=root: -i 192.168.1.10

# Storage Setup

In my setup, I'm using an external USB SSD for storage. I plug in the USB SSD
storage, with the partition formatted as `ext4`, here's the `/etc/fstab`:

    LABEL=writable  /        ext4   defaults        0 0
    LABEL=system-boot       /boot/firmware  vfat    defaults        0       1
    UUID="91175b3a-ee2c-47a7-a1e5-f4528e127523" /export ext4 defaults 0 0

Then mount the storage as:

    mkdir -p /export
    mount -a

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

    wget http://dl.rohityadav.cloud/cloudstack-rpi/systemvmtemplate/systemvmtemplate-4.13.0.0-kvm-arm64.qcow2
    /usr/share/cloudstack-common/scripts/storage/secondary/cloud-install-sys-tmplt \
              -m /export/secondary -f systemvmtemplate-4.13.0.0-kvm-arm64.qcow2 -h kvm \
              -o localhost -r cloud -d cloud

# Setup KVM host

Install KVM and CloudStack agent, configure libvirt:

    apt-get install qemu-kvm cloudstack-agent

By default due to platform/version issue cloudstack-agent will fail to start,
please copy the following jars from the [upstream
project](https://github.com/java-native-access/jna/tree/master/dist) to
`/usr/share/cloudstack-agent/lib`:

    jna-5.4.0.jar
    jna-platform.jar
    linux-aarch64.jar

And remove the following:

    rm -f /usr/share/cloudstack-agent/lib/jna-4.0.0.jar

Fix the following in `/etc/cloudstack/agent/agent.properties`:

    guest.cpu.arch=aarch64

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

Ensure the following options in the `/etc/cloudstack/agent/agent.properties`:

    guest.cpu.arch=aarch64
    guest.cpu.mode=host-passthrough
    host.reserved.mem.mb=512

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

### Launch Management Server

Start your cloud:

    cloudstack-setup-management
    systemctl status cloudstack-management
    tail -f /var/log/cloudstack/management/management-server.log

After management server is UP, proceed to http://`192.168.1.10(cloudbr0-IP)`:8080/client
and log in using the default credentials - username `admin` and password
`password`.

# Example Advanced Zone Deployment

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

    VLAN/VNI range: 500-800

### Add Resources

Create a cluster with following:

    Name - any name
    Hypervisor - Choose KVM

Add your default/first host:

    Hostname - 192.168.1.10
    Username - root
    Password - <password for root user, please enable root user ssh-access by password on the KVM host>

Note: `root` user ssh-access is disabled by default, [please enable it](https://askubuntu.com/questions/469143/how-to-enable-ssh-root-access-on-ubuntu-14-04).

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
you can proceed with your IaaS usage. You can build your own template or test
the templates at: http://dl.rohityadav.cloud/cloudstack-rpi/template

Happy hacking!
