---
slug: how-to-use-zfs-on-ubuntu-16-04
description: 'Learn how to leverage ZFS on Ubuntu 16.04 to ensure data redundancy and prevent silent data corruption effectively.'
og_description: 'Discover how to utilize the Z File System (ZFS) to enhance data integrity, redundancy, read/write speeds, and compression. This tutorial provides detailed instructions on setting up ZFS and configuring virtual devices (vdevs) on a Utho server.'
keywords: ["zfs", "file system", "volume manager", "redundant", "silent corruption", "mirror", "raid", "pool"]
tags: ["ubuntu"]
modified: 2024-03-14
modified_by:
  name: Utho
title: 'Using ZFS on Ubuntu 16.04: A Comprehensive Guide'
published: 2024-03-14
authors: ["Pawan Kumar"]
---

How to Use ZFS on Ubuntu 16.04 title graphic

## Understanding Silent Data Corruption and ZFS Operation

Silent data corruption can occur due to various hardware or software issues, leading to erroneous data being written without any indication of errors. This corrupted data may remain undetected until it causes problems, potentially leading to data loss or other issues.

ZFS, or the Z File System, addresses this issue by implementing data checksumming. Before writing data to disk, ZFS computes a checksum for each block of data and stores it alongside the data itself. When reading the data, ZFS verifies the checksum to ensure data integrity. If the [checksums](https://en.wikipedia.org/wiki/Checksum) doesn't match, ZFS detects that the data has been corrupted and can attempt to repair it using redundancy features if available.

In addition to data checksumming, ZFS offers several other features:

 - ZFS allows users to create storage pools by combining multiple physical storage devices. This simplifies storage management and enables dynamic resizing of storage pools without the need for partitioning.
 - Many operations in ZFS can be performed while the filesystem is online, minimizing downtime and interruptions.
 - ZFS allows users to take snapshots of filesystems, capturing their state at a specific point in time. These snapshots can be used for backups or to rollback to a previous state if needed.
 - Clones can be created from snapshots, allowing users to create copies of filesystems that share data blocks with the original snapshot. Clones can be promoted to independent filesystems if necessary.
 - ZFS supports data compression and deduplication, which can reduce storage space usage and improve read and write performance, especially for compressible data types like text files.

## Understanding ZFS Pool Structure

1.  A ZFS pool comprises one or more groups of virtual devices (vdevs), each composed of block devices. These block devices can be supported by partitions or entire disks/drives. Below is an illustration of a pool structure:

        root@zfsbox:~# zpool status
          pool: testpool
         state: ONLINE
          scan: none requested
        config:

                NAME            STATE     READ WRITE CKSUM
                testpool        ONLINE       0     0     0
                  mirror-0      ONLINE       0     0     0
                    sdb         ONLINE       0     0     0
                    sdc         ONLINE       0     0     0
                  mirror-1      ONLINE       0     0     0
                    sdd         ONLINE       0     0     0
                    sde         ONLINE       0     0     0

        errors: No known data errors

    The block devices **sdb** and **sdc** represent entire disks and form the vdev named **mirror-0**. Another vdev, **mirror-1**, also comprises two disks. Together, these two vdevs constitute the pool named **testpool**.

2.  Within the pool, you can store various types of datasets, such as filesystems, snapshots, clones, or volumes. Volumes act as virtual block devices.

For instance, you could merge two physical disks into a pool and then generate a volume spanning both physical devices. This setup creates a large, virtual disk formatted with your preferred filesystem.

### Single-Disk vdev

You can form a vdev from a single disk. By combining multiple vdevs, you create a structure akin to RAID-0, where data is dynamically striped across all the physical devices.

Advantages:

 - Utilizing nearly 100% of available storage space
 - Achieving the fastest write and read speeds
 - Easily expanding pool capacity by adding more drives

Disadvantages:

 - If one disk fails, your entire pool fails
 - No way to recover original data in case of read/checksum errors; ZFS will report them but there's nothing more it can do

### Mirror vdevs

Mirror two or more devices, then later add another mirror to the same virtual device to increase redundancy. Alternatively, you can create a new vdev that includes a new set of mirrored drives to expand the pool. This creates something similar to RAID-10. While expanding the pool with other types of vdevs is an option, it's generally recommended to stick to the same kind of structures in each pool.

If you pair a 1TB disk with a 2TB disk, the usable space will be limited to 1TB. This is because that is the maximum information that can be mirrored in such a setup.

Advantages:

- Fast read speeds, slightly better than RAID-Z but slightly worse than the previous non-redundant setup.
- Fast resilvering for replacing a failed device and mirroring data back.
- If there's at least one functional device in a mirror vdev, you can replace the failed drives and recover.
Redundancy can be increased later on.

Disadvantages:

- Least storage space available. If there are 4 disks in a mirror vdev, you can only use the capacity of the smallest disk in that structure.
- Weakest write performance, comparable to a single disk.

### RAID-Z vdevs

These vdevs can have single, double, or triple parity (and even more in recent ZFS versions). Parity data is distributed across all physical devices in the vdev. The usable space is roughly the total storage capacity minus the space used for parity. For instance, in a setup with double parity and a RAID-Z2 vdev containing 9 disks of 1TB each, you can store approximately 7TB of information (9TB total - 2TB for parity).

Advantages:

- Strikes a balance between usable space and redundancy.
- Offers decent read/write speeds, which are inversely proportional to the level of parity.

Disadvantages:

- Unable to add disks to a RAID-Z vdev after its creation.
- Resilvering process can be more time-consuming and resource-intensive compared to mirrors.

Other types of virtual devices like cache and log can be utilized, especially when dealing with mechanical hard drives. Spare vdevs can also be configured to automatically replace failed devices, enhancing the reliability of the storage system.

## RAID-Z and ZFS Recommendations

1. When compression is enabled and RAID-Z arrays are large or have more than two parity devices, using a Utho with multiple virtual CPUs can significantly enhance performance, especially in scenarios involving intense write operations.

2. For storage servers that experience intensive read requests from other servers or services, opting for a Utho with ample RAM is beneficial. This allows data to be cached effectively, resulting in rapid delivery upon request.

These recommendations aim for optimal performance, but ZFS can function on any Utho. There's a misconception regarding the memory-intensive nature of the Adaptive Replacement Cache (ARC), which will be clarified later in this guide. This misconception primarily applies when deduplication is enabled.

3. Maintain uniformity in the size of physical devices within a vdev. Mixing different storage capacities can often lead to degraded performance or loss of usable space. For example, in a non-redundant striped array consisting of a 1TB disk and a 4TB disk, if 5GB of data is written, the write process will be bottlenecked by the slower 4GB disk. Aligning the sizes of the disks would result in a more balanced write process and faster completion.

In the case of mirrors or RAID-Z configurations, the usable size will be limited to the smallest storage device within a vdev.

4. Proper planning and design from the outset are crucial. It's advisable to create the setup correctly initially rather than starting with minimal resources and then adding vdevs as needed. An empty vdev will handle writes more efficiently than one that is partially filled.

If additional storage space is required, it's preferable to create a secondary pool rather than adding virtual devices to the existing one. This involves creating a new Utho, setting up a larger pool, and then importing data from the old Utho.

5. Despite the measures in place to mitigate data corruption, it's essential to maintain off-site backups as a best practice.
## Before You Begin

1. If you haven't already, sign up for a Utho account and create a Compute Instance.

1.  Setting Up and Securing a Compute Instance to update your system. You might want to adjust the timezone, set up your hostname, create a restricted user account, and secure SSH access. During updates, if prompted about a configuration change in GRUB's config file, choose to keep the current local version installed.

    {{< note respectIndent=false >}}
  The procedures outlined in this guide require root privileges. Ensure that you execute the following steps as root or with the sudo prefix.

{{< /note >}}

Install a metapackage that will fetch the latest Ubuntu-provided kernel optimized for virtual machines.

        apt install linux-image-virtual

You'll enable automatic booting with GRUB later, which currently has a default timeout of 10 seconds for user input. Set the timeout to 0 and then rebuild the GRUB configuration files.

        sed -i.bak 's/GRUB_TIMEOUT.*/GRUB_TIMEOUT=0/' /etc/default/grub; update-grub

Once you've determined how to structure your ZFS setup, you can skip the steps related to creating a filesystem, mounting, and editing fstab. ZFS will handle those tasks automatically.

To enable the ZFS module, switch from Utho's default kernel to the one provided by Ubuntu. Go to your Utho dashboard, click on Edit to access your Ubuntu configuration profile. Then, under Boot settings, switch the Kernel to GRUB 2.

Restart your system using the Utho Manager.

## Creating a Pool with ZFS Installation

1.  Install the ZFS tools:

        apt install zfsutils-linux

2.  Discover the names of the symbolic links to your volumes:

        ls /dev/disk/by-id/

    Here's a sample output:

        root@localhost:~# ls /dev/disk/by-id/
        scsi-0Utho_Volume_d1  scsi-0Utho_Volume_d4
        scsi-0Utho_Volume_d2  scsi-0QEMU_QEMU_HARDDISK_drive-scsi-disk-0
        scsi-0Utho_Volume_d3  scsi-0QEMU_QEMU_HARDDISK_drive-scsi-disk-1

      For example, d1, which represents device 1, produced the ID scsi-0Utho_Volume_d1. Make sure to substitute each occurrence in the subsequent example commands with your specific identifiers.

### Non-redundant ZFS Pools

1.  Combining two disks in a striped, non-redundant configuration is achieved by:

        zpool create -f testpool /dev/disk/by-id/scsi-0Utho_Volume_d1 /dev/disk/by-id/scsi-0Utho_Volume_d2

    The -f flag denotes "force." Typically, it's advisable not to use this flag to receive warnings about potential errors. However, in this scenario, it's necessary because the devices are empty, and zpool won't utilize them without the -f flag.

    `testpool` is the name of the pool. This will be automatically mounted in `/testpool`.

2. To modify this mountpoint subsequently:

        zfs set mountpoint=/somewhereelse testpool

3.  To append additional disks to the array:

        zpool add -f testpool /dev/disk/by-id/scsi-0Utho_Volume_d4

4.  Destroy the test pool to proceed with the next examples:

        zpool destroy testpool

### Mirror ZFS Pools

1.  Create a mirrored pool using two devices:

        zpool create -f testpool mirror /dev/disk/by-id/scsi-0Utho_Volume_d1 /dev/disk/by-id/scsi-0Utho_Volume_d2

2.  Increase redundancy by adding another device and creating a three-way mirror:

        zpool attach -f testpool /dev/disk/by-id/scsi-0Utho_Volume_d2 /dev/disk/by-id/scsi-0Utho_Volume_d3

3.  Inspect the pool with:

        zpool status

The **READ**, **WRITE**, and **CKSUM** columns indicate the number of errors encountered during reading, writing, and checksum verification, respectively.

If these are different than 0, it's time to replace the respective device.

4.  Examine the pool using the command:

        zpool destroy testpool

      Let's generate a different type of array:

        zpool create -f testpool mirror /dev/disk/by-id/scsi-0Utho_Volume_d1 /dev/disk/by-id/scsi-0Utho_Volume_d2 mirror /dev/disk/by-id/scsi-0Utho_Volume_d3 /dev/disk/by-id/scsi-0Utho_Volume_d4

      Using zpool status, you'll notice that you now have two mirrors. This setup offers both redundancy and efficient distribution of writes across two vdevs.

      Destroy the pool so that you can also experiment with RAID-Z.

### Raid-Z Pools

1.  Create a RAID-Z pool with single parity by running the following command:

        zpool create -f testpool raidz1 /dev/disk/by-id/scsi-0Utho_Volume_d1 /dev/disk/by-id/scsi-0Utho_Volume_d2 /dev/disk/by-id/scsi-0Utho_Volume_d3 /dev/disk/by-id/scsi-0Utho_Volume_d4

2.  Execute this command:

        zpool list

    **SIZE** represents the raw size, including parity data. To determine the usable storage size:

        zfs list

  **AVAIL** indicates the storage capacity available for your files and directories.

3.  To create a double parity RAID-Z setup, first, delete your existing pool, then run the following command:

        zpool create -f testpool raidz2 /dev/disk/by-id/scsi-0Utho_Volume_d1 /dev/disk/by-id/scsi-0Utho_Volume_d2 /dev/disk/by-id/scsi-0Utho_Volume_d3 /dev/disk/by-id/scsi-0Utho_Volume_d4

    After running the command, checking `zfs` list again will display less available space because two devices are now allocated for parity instead of one. You can increase parity further by replacing raidz2 in the previous command with raidz3, raidz4, or higher. However, adding more parity can degrade performance. It's often preferable to have two RAID-Z2 arrays with eight disks each rather than a single array with 16 disks.

## Datasets, Snapshots and Rollbacks

1.  Creating multiple ZFS filesystems can be beneficial. In our example, these are mounted under `/testpool` and appear as regular directories.

        zfs create testpool/data

  If you have one directory containing code from a project and another directory with images, you can create two datasets. Then, you can enable compression for the dataset containing code and disable compression for the dataset with images. You can view the value of all properties that can be modified with:

        zfs get all

    By keeping a logical separation of data in different filesystems, you will also be able to snapshot and rollback just the parts of interest.

2.  Navigate to the `/testpool/data`data directory and create some files:

        cd /testpool/data/; touch {a..z}

Type `ls` to view the files.

3.  Create a snapshot by executing the following command:

        zfs snapshot testpool/data@a-to-z

4.  Simulate a disaster by deleting a few files as follows:

        rm /testpool/data/{a..l};ls /testpool/data/

5.  Each filesystem contains a hidden directory named `.zfs`, which provides access to the content of your snapshots.

        ls /testpool/data/.zfs/snapshot/a-to-z/

6.  Rollback to the snapshot by executing the following command:

        zfs rollback testpool/data@a-to-z

7.  After rolling back to the snapshot, if you list the contents of your `data` filesystem, it will be restored back to the state captured by the snapshot.

        ls /testpool/data/

8.  To delete a snapshot, use the following command:

        zfs destroy testpool/data@a-to-z

## Speed Up ZFS

1.  By default, compression is disabled. Enabling compression can accelerate both writing and reading data for compressible files.

        zfs set compression=on testpool

    To enable compression for a particular dataset:

        zfs set compression=on testpool/data

2.  The memory used by the Adaptive Replacement Cache (ARC) may appear as "used" instead of "cached" in utilities like `free` or `htop`. Despite this, the ARC dynamically allocates memory based on system requirements, ensuring that other utilities have access to memory when needed. This enables a ZFS filesystem to operate efficiently even with limited RAM.

3.  By default, the ARC utilizes up to 75% of total system memory. However, on dedicated storage servers with ample RAM and no other critical applications running, this default setting may lead to underutilization of available memory. To optimize memory usage for ZFS, adjust the `zfs_arc_max` and `zfs_arc_min` parameters in the `zfs.conf` file.

To set these values, use numbers representing bytes. For example, to reserve approximately 7GB for ARC cache and set the minimum ARC cache size to 50% of total RAM, adjust the `zfs_arc_max` and `zfs_arc_min` values accordingly. For instance, if the system has 16GB of available RAM, set `zfs_arc_max` to `15000000000` and `zfs_arc_min` to `8000000000`.

4. Open the `zfs.conf` file and add the following lines, adjusting the numerical values as needed:

    {{< file "/etc/modprobe.d/zfs.conf" conf >}}
options zfs zfs_arc_min=3221225472
options zfs zfs_arc_max=6442450944
{{< /file >}}

5. Save the changes to the file and reboot the machine using the Utho Manager to apply the modifications.

Periodically check the status of the ZFS pool by running `zpool status`. Additionally, a cron script located at `/etc/cron.d/zfsutils-linux` automatically runs `zpool scrub` monthly to maintain pool health.
