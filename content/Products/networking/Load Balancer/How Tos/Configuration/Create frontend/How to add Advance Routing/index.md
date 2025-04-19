---
weight: 30
title: Manage Elastic Block Storage
title_meta: "Manage Elastic Block Storage on the Utho Platform"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ['/products/Elastic Block Storage/manage-loadbalancer']
icon: guides
---
### Adding Advance Routing

- Now once the ACL rule is added then click on **Add new routing** then a side bar will open where you have to enter the data like, acl rule, condition and target group. Now after entering all the data click on **Add Routing** then routing will be created.

  ![1743683166617](image/index/1743683166617.png)

Once the Advance routing are added, they will be applied to the frontend configuration.

## **Explanation:-**

**Advanced routing** allows you to direct traffic based on specific criteria like  **URL paths** ,  **HTTP headers** ,  **query strings** , or  **cookies** . This ensures more  **efficient traffic distribution** , enables **personalization** (e.g., A/B testing), and optimizes resource usage. It provides greater **flexibility** in routing requests to the appropriate backend servers, improving **scalability** and  **performance** .

* **ACL Rule** : Filters traffic before it reaches the target group, ensuring security.
* **Condition** : Defines the rules for routing traffic to the correct target group based on request attributes (e.g., path, headers).
* **Target Group** : A **target group** is a set of backend resources (e.g., EC2 instances, containers, IP addresses) that the load balancer routes traffic to. When adding a target group, you specify the **protocol** and **port** for communication. It ensures that the load balancer directs traffic to the appropriate backend service for processing.
* When all details are filled then click on Advance Routing button , then rule will be created.
