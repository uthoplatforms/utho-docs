---
weight: 30
title: "Configuration"
title_meta: "Configuration"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/storage/Elastic Block Storage/How Tos/Configuration"]
icon: guides
---
### Configuration Page

![1743597008993](image/index/1743597008993.png)

Once the Elastic Block Storage (EBS) is created, click on the **Manage** button to be redirected to the **Manage Section**.

**Create a Filesystem**:
First, create a filesystem on your new storage with the command:
`mkfs.ext4 /dev/disk/by-id/virtio-uthostorage-49050`

**Create a Mountpoint** :
Next, create a mountpoint (a folder) to store your data using:

`mkdir /mnt/uthostorage-49050`

**Mount the Storage:** Once the filesystem is ready, mount the storage to the folder with:

`mount /dev/disk/by-id/virtio-uthostorage-49050 /mnt/uthostorage-49050`

**Auto-Mount on Boot:** To automatically mount the storage every time your server starts, add a line similar to the following to your /etc/fstab file.

`/dev/disk/by-id/virtio-uthostorage-49050 /mnt/uthostorage-49050 ext4 defaults,noatime,nofail 0 2 `
