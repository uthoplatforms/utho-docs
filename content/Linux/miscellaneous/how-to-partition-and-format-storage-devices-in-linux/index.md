---
title: "How To Partition and Format Storage Devices in Linux"
date: "2022-09-16"
title_meta: "How To Partition and Format Storage Devices in Linux"
description: "How To Partition and Format Storage Devices in Linux"
keywords:  ['partition','linux', 'storage']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/miscellaneous/how-to-partition-and-format-storage-devices-in-linux']
---

![How To Partition and Format Storage Devices in Linux](images/How-To-Partition-and-Format-Storage-Devices-in-Linux_utho.jpg)

**Introduction**

  
Linux disc preparation is simple. Specialized demands may require different tools, [filesystem](https://en.wikipedia.org/wiki/File_system) formats, or partitioning strategies, but the basics remain the same.

This guide explains:

Detecting the new disc.  
Creating a single drive-wide partition (most operating systems expect a partition layout, even if only one filesystem is present)  
Ext4 filesystem formatting (the default in most modern Linux distributions)  
Installation Boot filesystem auto-mounting

To Partition and Format Storage Devices in Linux read below steps..

## Step 1 — Install Parted

The parted utility partitions drives. Linux includes most low-level filesystem operations by default. The exception is parted, which generates partitions.

If you don't have parted on Ubuntu or Debian, type:

```
# sudo apt update 
```

```
# sudo apt install parted 
```

On a server running RHEL, Rocky Linux, or Fedora, you can install it by typing:

```
# sudo dnf install parted 
```

Every other command used in this guide should be preloaded, so you can go to the next step.

## Step.2 Find the new disc

Before configuring the drive, it must be properly identified on the server.

If this is a brand-new drive, you can identify it on your server by observing the lack of a partitioning scheme. If you instruct parted to list the partition layout of your drives, it will generate an error for any discs that lack a proper partition scheme. This can assist in identifying the new disc:

```
# sudo parted -l | grep Error 
```

The fresh, unpartitioned drive should display an unknown disc label error:

```
Output  
Error: /dev/sda: unrecognized disk label
```

Additionally, you can use the lsblk command to locate a disc of the correct size that has no linked partitions:

```
# lsblk 
```

```
 Output  
NAME MAJ:MIN RM SIZE RO TYPE MOUNTPOINT  
sda 8:0 0 100G 0 disk  
vda 253:0 0 20G 0 disk  
└─vda1 253:1 0 20G 0 part / 
```

Once you have determined the name that the kernel has assigned to your disc, you can partition it.

## Step.3 New Drive Partition

As noted in the introduction, in this guide you will build a single partition that spans the entire drive.

**Select a standard partition  
**  
To accomplish this, you must first select the partitioning standard to employ. GPT and MBR are the options available. GPT is a more recent standard, although MBR is supported by a greater number of older operating systems. For a typical cloud server, GPT is the superior choice.

To select the GPT standard, submit the discovered disc to parted with mklabel gpt:

```
# sudo parted /dev/sda mklabel gpt 
```

Use mklabel msdos to utilise the MBR format.

```
# sudo parted /dev/sda mklabel msdos 
```

**Create the New Partition**

```
# sudo parted -a opt /dev/sda mkpart primary ext4 0% 100% 
```

This command can be broken down as follows:

/dev/sda is the disc you are partitioning, and parted -a opt sets the optimal alignment type by default.  
mkpart main ext4 creates an independent (bootable, not extended from another) partition utilising the ext4 filesystem.  
0% 100% indicates that this partition should span the entirety of the disc.

```
# lsblk 
```

## Step.4 Implement a Filesystem on the New Partition

You can initialise an Ext4 partition once you have one. Ext4 isn't the only filesystem option, but it's the easiest for a single Linux drive. Windows utilises NTFS and exFAT, although they have limited support on other platforms (they're read-only in some circumstances and can't be used as a boot drive). macOS uses HFS+ and APFS, with the same limitations. ZFS and BTRFS, newer Linux filesystems than Ext4, have different needs and are suitable for multi-disk arrays.

mkfs.ext4 initialises Ext4 filesystems. The -L flag labels partitions. Choose a name for this drive:

```
# sudo mkfs.ext4 -L datapartition /dev/sda1 
```

If you wish to modify the partition label in the future, you can execute the e2label command:

```
# sudo e2label /dev/sda1 newlabel 
```

lsblk shows how to identify partitions. Find partition's name, label, and UUID.

—fs tells lsblk to print all of this information.

```
# sudo lsblk --fs 
```

You can also use lsblk -o followed by the options.

```
# sudo lsblk -o NAME,FSTYPE,LABEL,UUID,MOUNTPOINT 
```

## Step 5 — Mount the New Filesystem

Mount the filesystem.

For temporarily mounted filesystems, the Filesystem Hierarchy Standard suggests /mnt or a subdirectory (like removable drives). It doesn't suggest where to put permanent storage, so you can choose. For this instruction, mount /mnt/data.

mkdir this directory:

```
# sudo mkdir -p /mnt/data 
```

**Temporary Filesystem Mounting**

```
# sudo mount -o defaults /dev/sda1 /mnt/data 
```

**Automatically Mounting the Filesystem at Boot**

```
# sudo nano /etc/fstab 
```

put the entry in fstab file you can copy from /etc/mtab file

```
# sudo mount -a 
```

**Testing the Mount point**

Check the filesystem after mounting the volume.

df output shows if the disc is available. You can exclude superfluous information about tmpfs by appending -x tmpfs to df output.

```
# df -h -x tmpfs 
```

```
 Output  
Filesystem Size Used Avail Use% Mounted on  
/dev/vda1 20G 1.3G 18G 7% /  
/dev/sda1 99G 60M 94G 1% /mnt/data 
```

You can test the disk's read/write capability by writing to a test file.

```
# echo "success" | sudo tee /mnt/data/test_file 
```

Check the file's writing by reading it back.

```
# cat /mnt/data/test_file 
```

```
Output  
success
```

After verifying the new filesystem's functionality, you can delete the file.

```
# sudo rm /mnt/data/test_file 
```

Must Read:- [https://utho.com/docs/tutorial/how-to-make-a-large-file-in-linux/](https://utho.com/docs/tutorial/how-to-make-a-large-file-in-linux/)

Hopefully you have understand the above steps How To Partition and Format Storage Devices in Linux..

**Thankyou**
