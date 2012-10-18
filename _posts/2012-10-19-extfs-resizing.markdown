---
layout: post
title: Painless Partition Resizing
excerpt: expanding ext3 partition
---

Alrighty, got my prgmr [VPS](http://baagi.org) upgraded to 512MiB/12GiB. Google failed me for the obvious keywords and the kind of results on it's first page of search results, hence this post. Meh, one could have read man pages to do this but blog post works for some chump who would rather Google than read man pages, in future on this topic. So, here you go;

You know your block device, partitions, `fdisk -l` already. Just check your ext2/ext3 partitions and run filesystem repair on the disk:

<pre class="prettyprint linenums">
$ e2fsck -f /dev/xvda1
$ fsck -n /dev/xvda1
</pre>

Let's take out the journaling, just remove it to make it _ext2_, delete the partition (worry not my chump :), create new one and expand the filesystem:

<pre class="prettyprint linenums">
$ tune2fs -O ^has_journal /dev/xvda1
$ fdisk /dev/xvda

    The number of cylinders for this disk is set to 1566.
    Command (m for help): p

    Disk /dev/xvda: 12.8 GB, 12884901888 bytes
    255 heads, 63 sectors/track, 1566 cylinders
    Units = cylinders of 16065 * 512 = 8225280 bytes

        Device Boot      Start         End      Blocks   Id  System
    /dev/xvda1               1        1566    12578863+  83  Linux

    Command (m for help): d
    Selected partition 1

    Command (m for help): n
    Command action
      e   extended
      p   primary partition (1-4)
    p
    Partition number (1-4): 1
    First cylinder (1-1566, default 1):
    Using default value 1
    Last cylinder or +size or +sizeM or +sizeK (1-1566, default 1566):
    Using default value 1566

    Command (m for help): w
    The partition table has been altered!
    Calling ioctl() to re-read partition table.
    Syncing disks.
</pre>

Let's finally check the partition, have it repaired, check the filesystem and turn on the journaling:

<pre class="prettyprint linenums">
$ e2fsck -f /dev/xvda1
    e2fsck 1.39 (29-May-2006)
    Pass 1: Checking inodes, blocks, and sizes
    Pass 2: Checking directory structure
    Pass 3: Checking directory connectivity
    Pass 4: Checking reference counts
    Pass 5: Checking group summary information
    PRGMRDISK1: 93269/786432 files (1.8% non-contiguous), 676860/1572864 blocks

$ resize2fs /dev/xvda1
    resize2fs 1.39 (29-May-2006)
    Resizing the filesystem on /dev/xvda1 to 3144715 (4k) blocks.
    The filesystem on /dev/xvda1 is now 3144715 blocks long.

$ fsck -n  /dev/xvda1
    fsck 1.39 (29-May-2006)
    e2fsck 1.39 (29-May-2006)
    PRGMRDISK1: clean, 93269/1572864 files, 702302/3144715 blocks

$ tune2fs -j /dev/xvda1
</pre>

And, done!

