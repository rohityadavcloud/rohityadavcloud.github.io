---
layout: post
title: Painless Partition Resizing
---

One of my prgmr VPS disk got upgraded to 512MiB/12GiB. I failed to find a good
post describing how to resize a single ext2/ext3 parition so sharing my
experience here.

List your block devices' partitions using  `fdisk -l`. Before proceeding check
your ext2/ext3 partitions and run filesystem repair on the disk:

    $ e2fsck -f /dev/xvda1
    $ fsck -n /dev/xvda1

Let's take out the journaling, just remove it and make it _ext2_. Delete the
partition (don't worry, we're not really removing any data but only altering the
partition table), create new one and expand the filesystem:

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

Let's finally check the partition, have it repaired, check the filesystem and
turn on the journaling:

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

And, done! Hope this helps.
