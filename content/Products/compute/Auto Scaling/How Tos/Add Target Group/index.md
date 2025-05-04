---
weight: 40
title: "Add Target Group"
title_meta: "Add Target Group"
description: "Guide on how to Add Target Group"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["auto scaling", "cloud auto scaling", "scaling groups", "load balancing", "automatic resource scaling"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/compute/Auto Scaling/How Tos/Add Target Group"]
icon: "globe"
tab: true
---

# **How to Attach a Target Group to Auto Scaling**

## **Overview**

Attaching a **Target Group** to an auto scaling instance helps distribute incoming traffic efficiently across your instances. This feature is essential for load balancing and improving application performance.

## **Login or Sign Up**

1. Visit the **Utho Cloud Platform** [login](https://console.utho.com/login) page.
2. Enter your credentials and click  **Login** .
3. If you’re not registered, sign up [here](https://console.utho.com/signup).

## **Steps to Attach a Target Group to Auto Scaling**

1. **Go to the Auto Scaling Listing Page**
   * Navigate to the auto scaling listing page in your account, or click [here ](https://console.utho.com/auto-scaling "Auto Scaling Listing Page")to directly access it.
2. **Select Your Auto Scaling Instance**
   * Choose the auto scaling instance you want to manage.
   * Click on the **Manage** button to navigate to the instance's management page.

     ![1743749970803](image/index/1743749970803.png)
3. **Navigate to the "Load Balancers" Section**
   * Look for the **Load Balancers** section on the management page, click on it.

     ![1743750034521](image/index/1743750034521.png)
4. **Find the "Target Groups Management" Sub-section**
   * In this section, all the **currently attached target groups** will be listed.
5. **Click on "Attach Target Group"**
   * Find and click on the **Attach Target Group** button.

     ![1743750069666](image/index/1743750069666.png)
6. **Select a Target Group**
   * A drawer will appear, showing a **dropdown** list of all available target groups for attachment.
   * Choose the desired **target group** from the list.
7. **Click on "Add Target Group"**
   * After selecting your target group, click on the **Add Target Group** button to attach it to the auto scaling instance.

     ![1743750110865](image/index/1743750110865.png)
8. **Verify the Attachment**
   * After successful attachment, check the list of **currently attached target groups** to verify the new target group is now associated with your auto scaling instance.

     ![1743750144647](image/index/1743750144647.png)

## **Impact of Attaching a Target Group**

* **Traffic Distribution:** Attaching a target group ensures that incoming traffic is distributed evenly among the instances in your auto scaling group, optimizing load balancing.
* **Improved Performance:** With a target group, the system can handle more traffic by directing it to healthy instances, which enhances the performance and availability of your application.
