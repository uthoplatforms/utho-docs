---
weight: 30
title: Manage Target Group
title_meta: "Manage Target Group on the Utho Platform"
description: "The Manage section allows users to view and update the configuration of their deployed VPNs. This section provides a comprehensive interface to manage Target Group users, configure firewalls, and destroy Target Group instances."
keywords: ["targetgroup", "security"]
tags: ["utho platform","targetgroup"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ['/products/networking/target-group/manage-tg']
icon: guides
tab: true
---
## Target Group Configuration Info

At the top of the Manage section, users can view the configuration information of the selected Target Group. This includes:

![Utho-Manage-targetgroup-config](image/Utho-Manage-targetgroup-config.png)

* **Target Group Name:** The unique name assigned to the Target Group.
* **Protocal:** The protocal to the Target Group.
* **Port:** The port to the Target Group.

## Configuration

In the Configuration section, users can update the Target Group. This section provides the following functionalities:

![Utho-Manage-targetgroup-update](image/Utho-Manage-targetgroup-update.png)

* **Target Group Name:** Update a unique name for your Target Group.
* **Protocal:** Select protocal option from the dropdown for your Target Group.
* **Port:** Update the port for your Target Group.
* **Health Check Path:** Update the health check path for your Target Group.
* **Health Check Protocol:** Update health check protocol option from the dropdown for your Target Group.
* **Health Check Interval:** Update the health check interval for your Target Group.
* **Health Check Timeout:** Update the health check timeout for your Target Group.
* **Healthy Threshold:** Update the healthy threshold for your Target Group.
* **Unhealthy Threshold:** Update the nnhealthy threshold for your Target Group.

## Targets

In the Targets section, users can add the remove Target from Target Group. This section provides the following functionalities:

![Utho-Manage-targetgroup-add-target](image/Utho-Manage-targetgroup-add-target.png)

* **Add Target:** Click the **Add Target** button to open a form where you can enter the user details such as server type, protocal, port and backend server or user have a custom IP option where user enter custom IP, protocal and port.
* **Add Server:** Click the **Add Server** button to the target into Target Group.

## Destroy

In the Destroy section, users can terminate the Target Group instance. This action is irreversible and will permanently delete the Target Group and all associated data. To destroy a Target Group

![Utho-Manage-targetgroup-destroy](image/Utho-Manage-targetgroup-destroy.png)

Click the **Destroy Target Group** button.

##### **Confirmation:**

![Utho-Manage-targetgroup-destroy-popup](image/Utho-Manage-targetgroup-destroy-popup.png)

A confirmation dialog will appear. Confirm the action to proceed with destroying the Target Group.

When you provide the confirmation then your Target Group Instance will destroy.
