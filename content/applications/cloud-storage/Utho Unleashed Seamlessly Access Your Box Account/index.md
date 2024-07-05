---
slug: "Utho Unleashed: Seamlessly Access Your Box Account"
description: "Unlocking Box: Learn to Connect Your Utho via WebDAV for Seamless Access"
keywords: ["box", "box.com", "cloud", "cloud storage", "file storage", "file", "webdav", "davfs", "davfs2"]
modified: 2024-03-15
modified_by:
  name: Pawan Kumar
published: 2024-03-15
title: "Utho Unleashed: Connect to Your Box.com Account with Ease"
authors: ["Pawan Kumar"]
---
If you've found [Box](https://www.box.com/), you probably know it's awesome for storing, moving, and organizing files. This guide will walk you through installing and setting up free software to access Box from your Utho.

## Before You Begin

1.  If you haven't already, make sure to set up your Utho account and Compute Instance.

1.  Use the "Setting Up and Securing a Compute Instance" guide to:

- Update your system.

- Set the timezone.

- Configure your hostname.

- Create a limited user account.

- Strengthen SSH access.

Note: To follow this guide, you'll need a Box account.

## Configuring Box's Mount Point

In this step, you'll make an empty directory to host your Box files and folders. You can choose any location, but for this guide, we'll use `/home/example_user/box`.

1.  Establishing a Mount Point:

        mkdir ~/box

Note: If only the user named `example_user` requires access to the Box account, it's okay to create the mount point within that user's `/home` directory. However, if multiple users (excluding root) need access, it's better to place the mount point in a system directory like `/mnt/box`. For further details, refer to [the davfs man page](http://linux.die.net/man/8/mount.davfs).

2.  Automate Mounting with fstab

 The `/etc/fstab` file is where system configurations for mounting are stored. To automate mounting your Box account, add an entry like this:


         {{< file "/etc/fstab" >}}
         https://dav.box.com/dav /home/example_user/box davfs rw,user,noauto 0 0
         {{< /file >}}

## Configuring WebDAV and User Permissions

1.  You'll need to install davfs2, the WebDAV backend that facilitates communication between your Utho and Box account.

**CentOS**

        sudo yum install davfs2

**Debian / Ubuntu**

        sudo apt-get install davfs2

When asked if unprivileged users should be allowed to mount WebDAV resources, choose `Yes`.

**Fedora**

        sudo dnf install davfs2

2.  Ensure your user has permission to mount using davfs2. Replace `example_user` with your username.

        sudo usermod -aG davfs2 "example_user"

3.  Restart your distro. This ensures there are no lingering user sessions open. If there are, you might encounter issues mounting the Box drive even after adding your user to the correct group.

        sudo reboot

4.  Log back into your Utho via SSH.

5.  Since the WebDAV share from Box.com doesn't support file locks, you'll need to turn off file locks in the davfs2 configuration. Otherwise, you might face an "Input/output error" when trying to create a file.

        echo 'use_locks 0' >> ~/.davfs2/davfs2.conf

6.  In the WebDAV secrets file, replace both occurrences of email with your Box account email address and password with your Box account password.

        echo 'https://dav.box.com/dav' email password' >> ~/.davfs2/secrets

Note: If your password includes quotation characters (' or "), you'll have to manually edit the secrets file using a text editor.

7. Ensure that the `secrets` file is readable only by its owner:

        chmod 600 ~/.davfs2/secrets

## Mounting and Dismounting Your Box Drive

1.  To mount and change into its directory:

        mount ~/box

2.  To unmount:

        umount ~/box

## Wrapping Up

To verify that your Box drive is mounted:

         df

The output should resemble this:

      {{< output >}}
      Filesystem              1K-blocks   Used Available Use% Mounted on
      /dev/root                 4122048 886316   3009636  23% /
      devtmpfs                   505636      0    505636   0% /dev
      tmpfs                      507504      0    507504   0% /dev/shm
      tmpfs                      507504   1420    506084   1% /run
      tmpfs                      507504      0    507504   0% /sys/fs/cgroup
      tmpfs                      507504      0    507504   0% /tmp
      tmpfs                      101504      0    101504   0% /run/user/1000
      https://dav.box.com/dav  10485756     72  10485684   1% /home/example_user/box
        {{< /output >}}

To view the mount options used for your Box drive:

    cat /proc/mounts | grep box

The output should display the following:    

    https://dav.box.com/dav /home/example_user/box fuse rw,nosuid,nodev,noexec,relatime,user_id=1000,group_id=1000,allow_other,max_read=16384 0 0

Congratulations, you're all set! The directory `/box` will now mirror your Box contents. The initial access to the folder might take a few minutes for synchronization. However, subsequent access is almost instantaneous.
