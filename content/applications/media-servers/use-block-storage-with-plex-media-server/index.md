---
slug: "Elevate Your Plex Experience: Integrating Block Storage with Plex Media Server"
description: "
Maximize your media server's capabilities by integrating Plex with Block Storage for scalable storage solutions. Discover how to enhance your setup with this comprehensive guide"
keywords: ["plex", "streaming", "netflix", "roku", "block storage"]
tags: ["ubuntu"]
modified: 2024-04-09
modified_by:
  name: Utho
published: 2024-04-09
title: "Enhancing Plex Media Server with Block Storage Volumes"
title_meta: "How to Use a Block Storage Volume with Plex Media Server"
dedicated_cpu_link: true
authors: ["Pawan Kumar"]
---

## What is Plex?

Plex serves as a media hub, allowing you to store media on a remote server and stream it to various devices. This guide illustrates how to enhance your Plex setup by attaching a Block Storage Volume to your Utho, ideal for accommodating a growing media library.

## Before You Begin
Before proceeding, ensure Plex Media Server is installed and operational on your Utho. If not, follow the instructions in [Install Plex Media Server on Ubuntu 16.04](/docs/guides/install-plex-media-server-on-ubuntu-18-04/). Once installed, configure the Plex server by setting up an SSH tunnel to your Utho and completing the [Initial Setup](/docs/guides/install-plex-media-server-on-ubuntu-18-04/#initial-setup) as outlined in the Initial Setup section.

Additionally, make sure you have a Plex account ready, as Plex Media Player will require login credentials.

## Attach a Block Storage Volume to a Utho

1. Create a Block Storage Volume and attach it to the Utho hosting Plex Media Server. Refer to [View, Create, and Delete Block Storage Volumes](/docs/products/storage/block-storage/guides/manage-volumes/) for detailed instructions via the Utho Manager.

If using the Utho CLI, initiate a new Volume and attach it to your Utho. Below is a sample command creating a 20GB Volume labeled `plex-volume`, attached to a Utho labeled `plex-utho`. Adjust the command as necessary:

        utho-cli volume create plex-volume -l plex-utho -s 20

2.  Format the Block Storage Volume with a filesystem, then establish a mountpoint following the guidelines provided by the Utho Manager:

3.  Verify the available disk space, taking into account the overhead associated with the Volume due to the filesystem:

        df -BG

        {{< output >}}
        Filesystem     1G-blocks  Used Available Use% Mounted on
        /dev/root            20G    3G       17G  13% /
        devtmpfs              1G    0G        1G   0% /dev
        tmpfs                 1G    1G        1G   1% /dev/shm
        tmpfs                 1G    1G        1G   2% /run
        tmpfs                 1G    0G        1G   0% /run/lock
        tmpfs                 1G    0G        1G   0% /sys/fs/cgroup
        /dev/sdc             20G    1G       38G   1% /mnt/plex-volume
        tmpfs                 1G    0G        1G   0% /run/user/1000
        {{< /output >}}

4.  If you want to use `scp` to transfer your media files directly from a client machine to the Volume (see next section), you will need to change the ownership of the mount point:

        sudo chown username:username /mnt/plex-volume

## Configure a Plex Client

1. Install Plex Media Player on your device for streaming. Visit the [Downloads section on the Plex website](https://www.plex.tv/downloads/) and follow the instructions for your device and operating system.

2.  Hover over **Libraries** in the left menu and click the + button:

3.  Choose the library type, such as **Movies**, and proceed:

4.  Click **Browse for Media Folder**.

5. A window will open. Navigate to the folder corresponding to the Block Storage Volume. In this case, it's located at `/mnt/plex-volume`.

## Transferring Media to the Volume Using scp
Transferring media to the Volume can be achieved using `scp` with the following command structure:

    scp example_video.mp4 username@123.456.7.8:/mnt/plex-volume

Depending on the size of the files, this process may take several minutes.


{{< note >}}
Alternatively, you can explore other methods for uploading files to a remote server. Refer to our guide on [Linux System Administration Basics](/docs/guides/linux-system-administration-basics/#upload-files-to-a-remote-server) for additional information.
{{< /note >}}

## Scan for New Media on the Volume

After new media is added to the Block Storage Volume, scan for files in the Plex Media Player. Navigate to the media type in the left menu and expand the dropdown menu. Select **Scan Library Files**.

Your media should now be accessible through the Plex Media Player.
