---
weight: 40
title: "Quick Start"
title_meta: "Quick Start"
description: "Quick Start Guide on Elastic IPs in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/Elastic IPs/Getting Started/Quick Start"]
icon: "globe"
tab: true
---


## **Quick Start Guide for Elastic IPs in Utho Cloud**

### **Overview**

An **Elastic IP** in Utho Cloud is a static public IP address that can be associated with your cloud resources, such as NAT Gateways or Instances. Elastic IPs are ideal for ensuring consistent IP addressing even if resources are stopped or restarted. This guide provides a concise walkthrough to view, create, manage, and delete Elastic IPs.

---

### **1. How to View Elastic IPs**

* **[Login](https://console.utho.com/login)** to your Utho Cloud account.
* From the **VPC** menu in the sidebar, select **Elastic IPs** to open the  **[Elastic IP Listing Page](https://console.utho.com/vpc/elasticip)** .
* This page displays all currently allocated Elastic IPs along with key details such as:
  * **Elastic IP ID** : Unique identifier of the IP.
  * **IP Address** : The actual public IP.
  * **Type** : Denotes if it's Static or Dynamic.
  * **Location** : The Data Center in which the IP is allocated.
  * **Network Type** : Type of network it's associated with (e.g., Public).

---

### **2. How to Allocate a New Elastic IP**

* On the  **Elastic IP Listing Page** , click the **"Add Elastic IP"** button at the top.
* A drawer will appear with a dropdown to select a  **Data Center Location** .
* Choose the appropriate **data center** based on where you plan to use the IP.
* Click **"Add Elastic IP"** to allocate the address.
* Once created, the new IP will appear in the listing with all relevant details.

---

### **3. How to Use or Assign an Elastic IP**

* After allocating an Elastic IP, you can assign it to supported resources like **NAT Gateways** or **Instances** during their creation or update processes.
* Simply select the Elastic IP from the dropdown list when configuring the resource.

---

### **4. How to Delete (Deallocate) an Elastic IP**

* From the  **Elastic IP Listing Page** , locate the IP you want to delete.
* Click the **"Deallocate"** button next to the corresponding IP address.
* A confirmation popup will appear. Click **"OK"** to confirm.
* The IP will be released and removed from your list.

---

### **Conclusion**

This quick start guide outlines the essential steps for managing **Elastic IPs** in Utho Cloud — from viewing and allocating to deallocating. Elastic IPs provide flexibility and stability for public access to your cloud services. Always ensure that an Elastic IP is no longer in use before deallocating to avoid service disruptions.
