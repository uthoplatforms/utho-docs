---
weight: 30
title: Manage Load Balancer
title_meta: "Manage Load Balancer on the Utho Platform"
description: "The Manage section allows users to view and update the configuration of their deployed Load Balancer. This section provides a comprehensive interface to manage Load Balancer users, configure firewalls, and destroy Load Balancer instances."
keywords: ["loadbalancer", "security"]
tags: ["utho platform","loadbalancer"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ['/products/LoadBalancer/manage-loadbalancer']
icon: guides
tab: true
---
## Load Balancer Configuration Info

At the top of the Manage section, users can view the configuration information of the selected Load Balancer. This includes:

![Utho-Manage-loadbalancer-config](image/Utho-Manage-loadbalancer-config.png)

* **Load Balancer Name:** The unique name assigned to the Load Balancer.
* **Datacenter Location:** The chosen datacenter location.
* **Status:** The current status of the Load Balancer (e.g., active, inactive, pending).

## Manage Load Balancer

In the Manage Load Balancer section, users have the ability to create frontend. This section provides the following functionalities:


![Utho-Manage-loadbalancer-createfrontend](image/Utho-Manage-loadbalancer-createfrontend.png)

* **Create Frontend:** Click the **Create Frontend** button to open a form where you can enter the user details such as name, protocal, port, algorithm, Redirect to HTTPS and Sticky Sessions.
* **Add Frontend:** Click the **Add Frontend** button to create frontend into load balancer.
<!-- * **Add Frontend:** Click the **Add Frontend** button.
* **Send Message:** Select a user from the list, click the **Download** button, which will download your vpn user into your brows. -->

## Destroy

In the Destroy section, users can terminate the Load Balancer. This action is irreversible and will permanently delete the Load Balancer and all associated data. To destroy a Load Balancer

![Utho-Manage-loadbalancer-destroy](image/Utho-Manage-loadbalancer-destroy.png)

Click the **Destroy Load Balancer** button.

##### **Confirmation:**

![Utho-Manage-loadbalancer-destroy-popup](image/Utho-Manage-loadbalancer-destroy-popup.png)

A confirmation dialog will appear. Confirm the action to proceed with destroying the Load Balancer.

When you provide the confirmation then your Load Balancer Instance will destroy.
