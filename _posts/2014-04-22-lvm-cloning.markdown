---
layout: post
title: Moving to a Bigger Disk
excerpt: Online LVM disk migration
---

This post would describe a painless disk migration strategy when moving your
partitions to a larger disk. My Thinkpad uses a 120G SSD which I wanted to clone
to a 480G SSD for my desktop so I can migrate my existing setup without having
to reinstall Linux, tons of packages on it and deal with their custom
configurations. I use LVM on all my system which makes the cloning and migration
very simple. This post assumes a simple partitioning scheme where you have at least
one primary partition for `/boot` and another for `/` (second one could be an
extended partition with LVM partitions).

First of all do back up your important data, keys and whatnot. Attach
the disks to a computer (desktop in my case). Next, boot to Linux from your
source disk, in single user mode or recovery mode, which in my case was the OCZ
120G SSD. Identify the destination partition using `fdisk -l`.

Alright, let's copy data bit by bit using `dd`. For readymade UX I use `pv` for
tracking progress, people use Ctrl+t or signals (such as sig USR) for tracking
copied bytes.

<pre class="prettyprint">
    $ dd if=/dev/sda | pv | dd of=/dev/sdb
</pre>

After this is successful, run `sync` to force flush disk buffer and reboot to
the destination disk which in my case was the 480G SSD.

Next, boot to the destination disk (probably detach the source disk). Do `fdisk -l`
to find various partitions, depending on how you may have partitioned the
source disk you may have to adapt to the solution this post describes. In my
case there were two partitions, a primary `/dev/sda1` for the /boot partition
and an extended `/dev/sda2` partition which had one main LVM partition
`/dev/sda5`. We now simply need to alter the partition table so the partitions
can occupy the free space, then resize the primary volumes and the logical
volumes and finally resize the file systems.

Now, we'll delete the partition table entries and resize the boundaries.
Don't worry doing the following does not really wipe off your data but simply
changes partition enteries (but beware what's you're going to do):

<pre class="prettyprint">
    $ fdisk /dev/sda # note this is the new disk

    Command (m for help): p

    Disk /dev/sda: 480.1 GB, 480103981056 bytes
    255 heads, 63 sectors/track, 58369 cylinders, total 937703088 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    Disk identifier: 0x000ea999

      Device Boot      Start         End      Blocks   Id  System
    /dev/sda1   *        2048      499711      248832   83  Linux
    /dev/sda2          499712   937703087   468601688    5  Extended
    /dev/sda5          501760   937703087   468600664   8e  Linux LVM

    Command (m for help): d
    Partition number (1-5): 2

    Command (m for help): n
    Partition type:
      p   primary (1 primary, 0 extended, 3 free)
      e   extended
    Select (default p): e
    Partition number (1-4, default 2):
    Using default value 2
    First sector (499712-937703087, default 499712):
    Using default value 499712
    Last sector, +sectors or +size{K,M,G} (499712-937703087, default 937703087):
    Using default value 937703087

    Command (m for help): n
    Partition type:
      p   primary (1 primary, 1 extended, 2 free)
      l   logical (numbered from 5)
    Select (default p): l
    Adding logical partition 5
    First sector (501760-937703087, default 501760):
    Using default value 501760
    Last sector, +sectors or +size{K,M,G} (501760-937703087, default 937703087):
    Using default value 937703087

    Command (m for help): t
    Partition number (1-5): 5
    Hex code (type L to list codes): 8e
    Changed system type of partition 5 to 8e (Linux LVM)

    Command (m for help): p

    Disk /dev/sda: 480.1 GB, 480103981056 bytes
    255 heads, 63 sectors/track, 58369 cylinders, total 937703088 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 4096 bytes
    I/O size (minimum/optimal): 4096 bytes / 4096 bytes
    Disk identifier: 0x000ea999

      Device Boot      Start         End      Blocks   Id  System
    /dev/sda1   *        2048      499711      248832   83  Linux
    /dev/sda2          499712   937703087   468601688    5  Extended
    /dev/sda5          501760   937703087   468600664   8e  Linux LVM

    Command (m for help): w
</pre>

Finally resize the physical volumes and logical volumes after which we're done:

<pre class="prettyprint">
    $ pvdisplay
    $ pvresize /dev/sda5
    $ lvdisplay
    $ lvresize -l+100%FREE /dev/volume-group-name/root
    $ resize2fs /dev/volume-group-name/root
    $ lvdisplay # verify LVM partition size
    $ df -h # verify partition size
</pre>
