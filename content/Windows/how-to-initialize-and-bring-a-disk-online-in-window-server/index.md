---
title: "How to Initialize and bring a disk online in Window Server"
date: "2022-08-22"
Title_meta: GUIDE TO Initialize and Bring a Disk Online in Windows Server

Description: This guide provides step-by-step instructions on how to initialize and bring a disk online in Windows Server. Learn the process to initialize new disks, bring them online, and prepare them for use using Disk Management or PowerShell commands, ensuring efficient storage management and utilization on your server.

Keywords: ['Windows Server', 'initialize disk', 'bring disk online', 'Disk Management', 'storage management', 'server administration']

Tags: ["Windows Server", "Initialize Disk", "Bring Disk Online", "Disk Management", "Storage Management", "Server Administration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-initialize-and-bring-a-disk-online-in-window-server/']
---
---

![](images/How-to-Initialize-and-bring-a-disk-online-in-Window-Server_utho.jpg)

Step 1. Log into your Win server and open Computer Management

Step 2. Right click on the "Not initialized" and then click on 'Initialize Disk'

![](images/initialize-new-disk.png)

Then the Disk will show as "Offline"

Step 3. Right click on "Offline" and then click on "Online"

![](images/dynamic-disk-offline.jpg)

step 4. Open 'Run', type 'diskpart' and click OK

Step 5. Run diskpart according to the article below:

https://utho.com/docs/tutorial/how-to-allocate-unallocated-disk-space-in-windows/

Step 6. After completing diskpart, go to File Manager, and check your disk space.

Thank you!
