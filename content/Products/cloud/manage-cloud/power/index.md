---
weight: 20
title: "Power"
title_meta: "Manage Cloud on the Utho Platform"
description: "Manage your cloud instance using simply clicks on utho platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/cloud/manage-cloud/power']
icon: "cpanel"
tab: true
---

Users can manage the power state and access settings of their cloud instance. The available options include:

### Console Access :

![1718831327653](image/index/1718831327653.png)Provides users with a console interface to directly access the cloud instance as if they were physically present at the server. This can be useful for troubleshooting and performing administrative tasks that require direct access.

### Reset Server Password :

![1718831347180](image/index/1718831347180.png)Allows users to reset the root or administrator password for the cloud instance. This is essential if the current password is forgotten or needs to be changed for security reasons.

### Power Off :

 ![1718831355384](image/index/1718831355384.png)Powers off the cloud instance. This is a graceful shutdown process that allows all running applications and processes to close properly, minimizing the risk of data corruption.

### Restart :

![1718831364293](image/index/1718831364293.png)Reboots the cloud instance. This involves shutting down and then starting the cloud instance again. It is useful for applying updates or changes that require a restart to take effect.

### Hard Reboot :

 ![1718831372076](image/index/1718831372076.png)Forces an immediate reboot of the cloud instance. This is akin to pressing the reset button on a physical machine and should be used with caution as it does not allow running applications and processes to close properly, which may lead to data loss or corruption.

### Rescue Server :

![1718831378839](image/index/1718831378839.png)Boots the cloud instance into a rescue mode. This is useful for recovering from system failures or performing maintenance tasks. In rescue mode, users can access the file system and troubleshoot issues that prevent the normal boot process.
