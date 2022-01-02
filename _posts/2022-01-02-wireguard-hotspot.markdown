---
layout: post
category: cloudstack
highlight: primary
title: RaspberryPi Wireguard VPN Hotspot/AP
---

In this post, we setup a `wireguard` VPN based RasberryPi Wifi Hotspot/AP that
uses `hostapd` and `dnsmasq` on Ubuntu 20.04. I needed this for my parents who
want to be able to connect to our home network, they would connect to the AP and
the AP is connected to the home network over VPN.

Let's start by installing the dependencies

    apt-get update
    apt-get install wireguard wireguard-tools dnsmasq hostapd

### Wireguard VPN

I'm skipping [how to configure Wireguard
server](https://www.digitalocean.com/community/tutorials/how-to-set-up-wireguard-on-ubuntu-20-04)
and client. For quick reference, here's what you need to do on the Ubuntu 20.04
RaspberryPi AP host where the wireguard client is configured:

    $ cat /etc/wireguard/wg0.conf
    [Interface]
    PrivateKey = <your client's private key>
    Address = <wg client ip>/24
    DNS = 8.8.8.8, 8.8.4.4

    [Peer]
    PublicKey = <wg server public key>
    PresharedKey = <wg server psk>
    Endpoint = <wg server IP or domain>:51820
    AllowedIPs = 0.0.0.0/0, ::0/0
    PersistentKeepalive = 15

    $ systemctl enable --now wg-quick@wg0

    # Now to quick check that the interface is UP
    $ wg
    interface: wg0
      public key: <hidden>
      private key: (hidden)
      listening port: <hidden>
      fwmark: 0xca6c

    peer: <hidden>
      preshared key: (hidden)
      endpoint: <wg server IP here>:51820
      allowed ips: 0.0.0.0/0, ::/0
      latest handshake: 1 minute, 36 seconds ago
      transfer: 1.52 MiB received, 501.10 KiB sent
      persistent keepalive: every 15 seconds

### Wireless AP

In my case, I've used here `192.168.4.1/24` for the hotspot/ap network.

    $ cat /etc/netplan/01-netplan.yaml
    network:
      version: 2
      renderer: networkd
      ethernets:
        eth0:
        dhcp4: no
        addresses: [192.168.1.5/24]
        gateway4: 192.168.1.1
        nameservers:
          addresses: [8.8.8.8,8.8.4.4]
        wlan0:
          dhcp4: false
          addresses:
          - 192.168.4.1/24
    $ netplan generate
    $ netplan apply

Next, setup `hostapd`:

    $ cat /etc/default/hostapd 
    DAEMON_CONF="/etc/hostapd/hostapd.conf"

    $ cat /etc/hostapd/hostapd.conf
    interface=wlan0
    driver=nl80211
    ssid=<Wifi Name Here>
    hw_mode=g
    channel=6
    ieee80211n=1
    wmm_enabled=1
    ht_capab=[HT40][SHORT-GI-20][DSSS_CCK-40]

    country_code=IN
    macaddr_acl=0
    auth_algs=1
    ignore_broadcast_ssid=0
    wpa=2
    wpa_key_mgmt=WPA-PSK
    wpa_passphrase=<password here>
    wpa_pairwise=TKIP
    rsn_pairwise=CCMP

    $ systemctl unmask hostapd
    $ systemctl enable --now hostapd

Next, configure `dnsmasq` which allocate IPs to clients on the AP:

    $ cat /etc/dnsmasq.conf
    interface=wlan0
    listen-address=192.168.4.1
    dhcp-range=192.168.4.2,192.168.4.50,255.255.255.0,24h
    bind-dynamic 
    domain-needed
    bogus-priv
    $ cat /etc/resolv.conf
    cat /etc/resolv.conf
    nameserver 8.8.8.8
    nameserver 8.8.4.4

    $ systemctl stop systemd-resolved
    $ systemctl mask systemd-resolved
    $ systemctl restart dnsmasq

### VPN based HotSpot/AP

Enable host to be able to forward packets:

    # Uncomment the following in /etc/sysctl.conf
    net.ipv4.ip_forward=1

Verify and apply the configuration:

    $ sysctl -p
    net.ipv4.ip_forward = 1

Finally, allow IP masquerading by enabling following rules to tunnel Wifi/AP
traffic via the wireguard `wg0` interface and persist rules on reboot:

    $ iptables -t nat -A POSTROUTING -o wg0 -j MASQUERADE
    $ iptables -A FORWARD -i wlan0 -o wg0 -j ACCEPT
    $ iptables -A FORWARD -i wg0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
    $ apt-get install iptables-persistent
    $ reboot
