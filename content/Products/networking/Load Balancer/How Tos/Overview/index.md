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
aliases: ["/products/networking/Load Balancer/How Tos/Overview"]
icon: guides
tab: true
---
## Overview of manage Section:

![1743678183724](image/index/1743678183724.png)

### 1. Configuration :

The **Load Balancer Configuration** in Utho Cloud allows users to set up and manage frontend and backend traffic distribution. Users can configure frontends with protocols like HTTP, HTTPS, TCP,  and enable SSL termination for secure connections. Backend servers can be added, with health checks and session persistence options to ensure reliability. Access Control List (ACL) rules provide security by defining inbound and outbound traffic restrictions. Advanced routing options allow traffic to be directed based on content, headers, or request methods, ensuring flexible and optimized load balancing for applications.

### 2. **Destroy** :

* **Definition** : **Destroying** an Load Balancer in Utho refers to permanently deleting the volume and all the data stored within it. Once destroyed, the lb cannot be recovered unless you have a **backup** or **snapshot** of the data.
* **Example** : When you no longer need an Load Balancer, you can destroy it to free up resources and avoid incurring unnecessary costs.
