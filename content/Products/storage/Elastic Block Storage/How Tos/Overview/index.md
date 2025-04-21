---
weight: 30
title: "Overview"
title_meta: "Overview"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/storage/Elastic Block Storage/How Tos/Overview"]
icon: guides
tab: true
---
## Overview of manage Section:

    ![1743595414868](image/index/1743595414868.png)

### 1. Configuration : 

**Configuration** in Elastic Block Storage (EBS) refers to the process of preparing and setting up the volume for use. This involves creating a filesystem, mounting the volume to a directory, and ensuring it persists after reboots.

### 2.  **Resize** :

* **Definition** : **Resizing** an EBS volume in Utho means changing the volume's size or performance characteristics (e.g., increasing its storage space or adjusting its  **IOPS** ). After resizing, the volume will reflect the new size and performance capabilities.
* **Example** : If you need more storage for your application data, you can resize the EBS volume to increase capacity without needing to destroy the volume.

### 2.  **Attach/Detach** :

* **Attach** :
* **Definition** : **Attaching** an EBS volume in Utho means linking the volume to an **instance** (e.g., a virtual machine). Once attached, the instance can use the EBS volume as additional storage.
* **Example** : You might attach an EBS volume to a Utho virtual machine running a web application, where the volume serves as storage for the application data.
* **Detach** :
* **Definition** : **Detaching** an EBS volume means removing it from the Utho virtual machine. After detachment, the volume remains in your account and can be attached to another instance as needed.
* **Example** : Detaching an EBS volume from a virtual machine when you want to stop using that machine or move the storage to another virtual machine.

### 4.  **Destroy** :

* **Definition** : **Destroying** an EBS volume in Utho refers to permanently deleting the volume and all the data stored within it. Once destroyed, the volume cannot be recovered unless you have a **backup** or **snapshot** of the data.
* **Example** : When you no longer need an EBS volume, you can destroy it to free up resources and avoid incurring unnecessary costs.
