---
layout: post
title: Hello Again Arch
excerpt: Installing Arch Linux
---

At this point of time I'm tired of using OSX and I've this feeling that I'm losing
my grip on my system. I no longer know why some processes are running and for
what, and if I should allow those processes to run.

For quite some time I was thinking to migrate back to some Linux distro; Ubuntu
was out of question, Debian release processes are too slow for me, Fedora/CentOS
are not much fun either. So, I decided to checkout Arch Linux [one more time](/logs/tweeting-with-irssi/)
because it's gaining a lot of traction lately and few of my hardcore friends
have already taken refuge under Arch and one of i3/xmonad/awesome.

From my last memories, I liked its minimalistic approach and the fact that no process
will run on it unless I wanted. Getting the [installation media](https://www.archlinux.org/download/)
was easy. Esoteric and technical, but the [installation process](https://wiki.archlinux.org/index.php/Official_Arch_Linux_Install_Guide) is quite simple:

- Disk partioning and formatting
- Mount partitions, generate filesystem table
- Installing Arch Linux base system
- Chroot to configure locale, time, install grub etc. and reboot and done!

Before wiping my MBA and install Arch I'll be evaluating Arch for next few weeks
in a VBox VM. To keep it simple, I'll have only two partitions; one for `/` and ones for swap.

<pre class="prettyprint">
# Partition disks
cfdisk

# Format partitions
mkfs -t ext4 /dev/sda1
mkswap /dev/sda2

# Mount partitions

mount /dev/sda1 /mnt
swapon /dev/sda2

# Fix mirror list
vi /etc/pacman.d/mirrorlist

# Install base system
pacstrap /mnt base base-devel

# Generate filesystem table
genfstab /mnt >> /etc/fstab

# Chroot, config system
arch-chroot /mnt

# Change root password
passwd

# Select en/US locale
vi /etc/locale.gen
locale-gen

# Configure timezone
ln -s /usr/share/zoneinfo/Asia/Kolkata /etc/localtime

# Set hostname
echo myawesomehostname > /etc/hostname

# Install bootloader
pacman -S grub-bios

grub-install /dev/sda
mkinitcpio -p grub
grub-mkconfig -o /boot/grub/grub.cfg

# Exit out of chrooted env
exit

# Cleanup reboot
umount /mnt
swapoff /dev/sda2
reboot
</pre>

After rebooting to the installed system, I enabled bunch of services like `dhcpcd`
so it autoconfigures network/ip for me, edit pacman conf file, update/upgrade using
`pacman` Arch's package manager, configure sound, x-server, bunch of kernel modules,
and install `i3` because all the good desktop environments are so messed up and I
like tiling window managers.

<pre class="prettyprint">
# Configure network, edit stuff...
dhcpcd
systemctl enable dhcpcd

# Add user
visudo  # Allow %wheel
useradd -m -g users -G storage,power,wheel -s /bin/bash bhaisaab

# Pacman
vi /etc/pacman.conf
# Update
pacman -Syy
# Upgrade
pacman -Su

# Sound
pacman -S alsa-utils
alsamixer --unmute
speaker-test -c2

# X
pacman -S xorg-server xorg-server-utils xorg-init

# VirtualBox drivers
pacman -S virtualbox-guest-utils
modprobe -a vboxguest vboxsf vboxvideo
vi /etc/modules-load.d/virtualbox.config

# X twm, clock, term
pacman -S xorg-twm xorg-clock xterm

# i3
pacman -S i3
echo "exec i3" >> ~/.xinitrc

# Startx
startx
</pre>

This is what I got for now ;)

<a href="/images/hello-arch.png">
<img src="/images/hello-arch.png">
</a>

If you're a long time Arch/i3 user share with me your experiences so far and your
i3 config file, it's always good to fork someone's [dotfiles](https://github.com/bhaisaab/dotfiles) than write from scratch :)
