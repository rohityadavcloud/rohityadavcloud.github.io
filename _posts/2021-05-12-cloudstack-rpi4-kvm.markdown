---
layout: post
category: cloudstack
highlight: primary
title: Apache CloudStack on RaspberryPi4 with Ubuntu 22.04 and KVM
---

    Originally posted here: https://www.shapeblue.com/apache-cloudstack-on-raspberrypi4-with-kvm/

Last updated: 12 Apr 2024 for ACS 4.18.2.0

[IoTs](https://en.wikipedia.org/wiki/Internet_of_things) have gained interest
over recent times. In this post I explore and share my personal experience of
setting up an Apache CloudStack based IaaS cloud on
[Raspberry Pi4](https://www.raspberrypi.org/products/raspberry-pi-4-model-b/), a
popular single-board [ARM64](https://en.wikipedia.org/wiki/ARM_architecture) IoT
computer that can run GNU/Linux kernel with
[KVM](https://www.linux-kvm.org/page/Processor_support#ARM:).

<div class="post-image">
  <img src="/images/rpi4/board.jpg">
</div>

CloudStack support for ARM64/RaspberryPi4 is available from version
[4.13.1.0+](https://github.com/apache/cloudstack/pull/3644). This guide uses a
[custom CloudStack 4.16 repository](https://download.cloudstack.org/rpi4/4.16/)
that was created and tested specifically against the new RaspberryPi4 and Ubuntu
20.04 aarch64/arm64 to setup an IAAS cloud computing platform.

<div class="post-image">
  <img src="/images/rpi4/dashboard.png">
</div>

Update: I was also able to test this guide on Mac Mini M2 Pro based machine with
ACS 4.18. I had to seed the most recent 4.18 systemvmtemplate manually using
https://download.cloudstack.org/arm64/systemvmtemplate/4.18

# Contents

- [Getting Started](#getting-started)
- [Network Setup](#network-setup)
- [Management Server Setup](#management-server-setup)
- [Storage Setup](#storage-setup)
- [KVM Host Setup](#kvm-host-setup)
- [Configure Firewall](#configure-firewall)
- [Launch Cloud](#launch-cloud)
- [Example Setup](#example-setup)

## Getting Started

By default, KVM is not enabled in the arm64 pre-built images. I found this
during my research where I built Linux kernel with KVM enabled on the ARM64 RPi4
board and shared my findings with the Ubuntu kernel team and influence the build
team [to have KVM enabled by
default](https://bugs.launchpad.net/ubuntu/+source/linux-raspi2/+bug/1783961).
With the changes, now Ubuntu 19.10+ ARM64 builds have KVM enabled.

To [get started](https://projects.raspberrypi.org/en/projects/raspberry-pi-getting-started) you need the following:
- RPi4 board (cortex-72 quad-core 1.5Ghz processor) 4GB/8GB RAM model
- Ubuntu 22.04 [arm64
image](http://cdimage.ubuntu.com/ubuntu/releases/22.04/release/) installed on a
Samsung EVO+ 128GB micro sd card (any 4GB+ class 10 u3/v30 sdcard will do).
- (Optional) An external USB-based SSD storage with high iops for storage

Flash the image to your microSD card:

    $ xzcat ubuntu-22.04.4-preinstalled-server-arm64+raspi.img.xz | sudo dd bs=4M of=/dev/mmcblk0
    0+381791 records in
    0+381791 records out
    3259499520 bytes (3.3 GB, 3.0 GiB) copied, 131.749 s, 24.7 MB/s

Eject and insert the microSD card again to initiate volume mounts, then create
an empty `/boot/ssh` file to enable headless ssh:

    # find the mount point
    mount -l | grep /dev/mmcblk0
    # cd to the writable mount point, for example:
    cd /media/rohit/writable
    # create an empty ssh file
    sudo touch boot/ssh

Next, check and ensure that 64-bit mode is enabled:

    cd /media/rohit/system-boot

    # Edit config.txt to have this:
    [all]
    arm_64bit=1
    device_tree_address=0x03000000
    dtoverlay=vc4-fkms-v3d
    enable_gic=1

    # Save file and unmount to safely eject the microSD card
    sync
    sudo umount /dev/mmcblk0p1
    sudo umount /dev/mmcblk0p2

Next, eject and insert the microSD card in your Raspberry Pi4 and power on. Find
the device via your router dhcp clients list and ssh into it using username
`ubuntu` and password `ubuntu`.

    ssh ubuntu@<ip>

Allow the root user for ssh access using password, fix `/etc/ssh/sshd_config`
and set `PermitRootLogin yes` and restart ssh using `systemctl restart ssh`.
Change and remember the `root` password:

    passwd root

Next, install basic packages and setup time as the root user:

    apt-get update
    apt-get install ntpdate openssh-server sudo vim htop tar iotop
    ntpdate time.nist.gov # update time
    hostnamectl set-hostname cloudstack-mgmt

Ensure that KVM is available at `/dev/kvm` or by running `kvm-ok`:

    # apt install cpu-checker

    # kvm-ok
    INFO: /dev/kvm exists
    KVM acceleration can be used

Disable automatic upgrades and unnecessary packages:

    apt-get remove --purge unattended-upgrades snapd cloud-init
    # Edit the files at /etc/apt/apt.conf.d/* with following
    APT::Periodic::Update-Package-Lists "0";
    APT::Periodic::Unattended-Upgrade "1";

Tip: In case you suspect IO load, to reduce load on RaspberryPi4 micrSD card
change the fs commit duration, for example in `/etc/fstab`: (however this adds
risk of potential data loss)

    LABEL=writable  /        ext4   defaults,commit=60      0 0
    LABEL=system-boot       /boot/firmware  vfat    defaults        0       1

## Network Setup

Next, setup host networking using Linux bridges that can handle CloudStack's
public, guest, management and storage traffic. For simplicity, a single bridge
`cloudbr0` to be used for all traffic types on the same physical network.
Install bridge utilities:

    apt-get install bridge-utils

Note: This part assumes that you're in a 192.168.1.0/24 home network which is a
typical RFC1918 private network.

Admins can now use `netplan` to configure networking with Ubuntu 20.04. The
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
          routes:
           - to: default
             via: 192.168.1.1
          nameservers:
            addresses: [192.168.1.1,8.8.8.8]
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

## Management Server Setup

Install MySQL server: (run as root)

    apt-get install mysql-server

Make a note of the MySQL server's root user password. Configure InnoDB settings
in `/etc/mysql/mysql.conf.d/mysqld.cnf`:

    [mysqld]

    server_id = 1
    sql-mode="STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,ERROR_FOR_DIVISION_BY_ZERO,NO_ZERO_DATE,NO_ZERO_IN_DATE,NO_ENGINE_SUBSTITUTION"
    innodb_rollback_on_timeout=1
    innodb_lock_wait_timeout=600
    max_connections=1000
    log-bin=mysql-bin
    binlog-format = 'ROW'

    default-authentication-plugin=mysql_native_password

Restart database:

    systemctl restart mysql

Installing management server may give dependency errors, so download and
manually install few packages as follows:

    # Setup Repo
    mkdir -p /etc/apt/keyrings
    wget -O- http://packages.shapeblue.com/release.asc | gpg --dearmor | sudo tee /etc/apt/keyrings/cloudstack.gpg > /dev/null
    echo deb [signed-by=/etc/apt/keyrings/cloudstack.gpg] http://packages.shapeblue.com/cloudstack/upstream/debian/4.18 / > /etc/apt/sources.list.d/cloudstack.list

    # Install management server
    apt-get update
    apt-get install cloudstack-management cloudstack-usage

    # Stop the automatic start after install
    systemctl stop cloudstack-management cloudstack-usage

Setup database:

    cloudstack-setup-databases cloud:cloud@localhost --deploy-as=root: -i 192.168.1.10

## Storage Setup

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

(Optional) Seed systemvm template from the management server: (note that starting 4.16 this is done automatically by CloudStack on zone deployment, the systemvmtemplate is bundled within the CloudStack deb/rpm package)

    wget http://download.cloudstack.org/arm64/systemvmtemplate/4.18/systemvmtemplate-4.18.1-kvm-arm64.qcow2
    /usr/share/cloudstack-common/scripts/storage/secondary/cloud-install-sys-tmplt \
              -m /export/secondary -f systemvmtemplate-4.18.1-kvm-arm64.qcow2 -h kvm \
              -o localhost -r cloud -d cloud

## KVM Host Setup

Install KVM and CloudStack agent, configure libvirt:

    apt-get install qemu-kvm cloudstack-agent
    systemctl stop cloudstack-agent

Enable VNC for console proxy:

    sed -i -e 's/\#vnc_listen.*$/vnc_listen = "0.0.0.0"/g' /etc/libvirt/qemu.conf

Fix security driver issue:

    echo 'security_driver = "none"' >> /etc/libvirt/qemu.conf

Enable libvirtd in listen mode:

    echo LIBVIRTD_ARGS=\"--listen\" >> /etc/default/libvirtd

Configure default libvirtd config:

    echo 'listen_tls=0' >> /etc/libvirt/libvirtd.conf
    echo 'listen_tcp=1' >> /etc/libvirt/libvirtd.conf
    echo 'tcp_port = "16509"' >> /etc/libvirt/libvirtd.conf
    echo 'mdns_adv = 0' >> /etc/libvirt/libvirtd.conf
    echo 'auth_tcp = "none"' >> /etc/libvirt/libvirtd.conf

The traditional socket/listen based configuration may not be supported, we can
get the old behaviour as follows:

    systemctl mask libvirtd.socket libvirtd-ro.socket libvirtd-admin.socket libvirtd-tls.socket libvirtd-tcp.socket
    systemctl restart libvirtd

Ensure the following options in the `/etc/cloudstack/agent/agent.properties`:

    guest.cpu.arch=aarch64
    guest.cpu.mode=host-passthrough
    host.cpu.manual.speed.mhz=1500

Note: the `host.cpu.manual.speed.mhz` is needed because Linux isn't able to
report the correct CPU speed for some models such as the M1/M2/M2 Pro etc. You
can set the correct CPU speed in Mhz of your host manually using this property.

By default 1GB of host memory is reserved for agent/host usage, which you can
change using the following in the agent.properties file:

    host.reserved.mem.mb=800

## Configure Firewall

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

## Launch Cloud

Start your cloud:

    cloudstack-setup-management
    systemctl status cloudstack-management
    tail -f /var/log/cloudstack/management/management-server.log

After management server is UP, proceed to
`http://192.168.1.10:8080/client/primate` (change the IP suitably)
and log in using the default credentials - username `admin` and password
`password`.

<div class="post-image">
  <img src="/images/rpi4/login.png">
</div>

NOTE: I found some DB issue while deploying a zone, please check/run the
following until the bug is fixed upstream, using mysql client:

    use cloud;
    ALTER TABLE nics MODIFY COLUMN update_time timestamp NULL;

## Example Setup

The following is an example of how you can setup an advanced zone in the
192.168.1.0/24 network.

### Setup Zone

Go to Infrastructure > Zone and click on add zone button, select advanced zone and
provide following configuration:

    Name - any name
    Public DNS 1 - 8.8.8.8
    Internal DNS1 - 192.168.1.1
    Hypervisor - KVM

<div class="post-image">
  <img src="/images/rpi4/addzone.png">
</div>

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
you can proceed with your IaaS usage.

You can build your own arm64 guest templates or deploy VMs using these guest
templates at: [https://download.cloudstack.org/rpi4/templates](https://download.cloudstack.org/rpi4/templates).
To get further help and to ask questions please join the Apache CloudStack users
mailing list:
[https://cloudstack.apache.org/mailing-lists.html](https://cloudstack.apache.org/mailing-lists.html)
