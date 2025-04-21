---
weight: 40
title: "Quick Start"
title_meta: "Quick Start"
description: "Quick Start guide on NAT Gateways in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/NAT Gateways/Getting Started/Quick Start"]
icon: "globe"
tab: true
---



## **Quick Start Guide for NAT Gateways in Utho Cloud**

### **Overview**

A **NAT Gateway** in Utho Cloud enables private cloud resources to securely access the internet while protecting them from direct inbound internet traffic. This guide provides a concise overview of how to view, create, manage, and destroy NAT Gateways, as well as how to attach or detach subnets.

---

### **1. How to View NAT Gateways**

* **[Login](https://console.utho.com/login)** to your Utho Cloud account.
* From the **VPC** menu in the sidebar, select **NAT Gateways** to view the  **[NAT Gateway Listing Page]()** .
* The list shows all previously deployed NAT Gateways with key details such as:
  * **Name** : The name of the NAT Gateway.
  * **Internet Gateway ID** : The ID of the associated internet gateway.
  * **Subnet ID** : The subnet ID to which the NAT Gateway is connected.
  * **Type** : The type of NAT Gateway (e.g., Static).
  * **Created At** : The creation date of the NAT Gateway.

### **2. How to Create a NAT Gateway**

* From the  **NAT Gateway Listing Page** , click the **"Create NAT Gateway"** button at the top.
* A drawer will appear with the following configuration inputs:
  * **Name** : Enter a name for the NAT Gateway.
  * **Subnet** : Select an available subnet from the dropdown.
  * **Elastic IP Allocation ID** : Choose an existing Elastic IP or click the **"Allocate IP"** button to auto-generate a new IP.
* After filling out the necessary details, click the **"Create NAT Gateway"** button to deploy the gateway.
* A success toast notification will confirm the creation. Verify the NAT Gateway by checking the updated listing.

### **3. How to Manage a NAT Gateway**

* From the  **NAT Gateway Listing Page** , select the NAT Gateway you want to manage.
* Click the **"Manage"** button to navigate to the  **NAT Gateway Manage Page** .
* In the manage page, you will see key details of the NAT Gateway:
  * **Name** : The NAT Gateway’s name.
  * **IPv4 Address** : The assigned IP address.
  * **Status** : Whether the NAT Gateway is active or not.
  * **Tabs for Further Configuration** :
  * **Subnets** : View or modify the subnets attached to the NAT Gateway.
  * **Destroy** : Permanently delete the NAT Gateway.

### **4. How to Attach a Subnet to a NAT Gateway**

* Go to the  **NAT Gateway Manage Page** .
* Click on the **"Subnets"** tab.
* Click the **"Attach Subnet"** button.
* A drawer will appear showing a dropdown of available subnets.
* Select the subnet you wish to attach and click **"Attach Subnet"** to link the subnet to the NAT Gateway.
* Verify the attachment by checking the updated list of attached subnets.

### **5. How to Detach a Subnet from a NAT Gateway**

* Go to the  **NAT Gateway Manage Page** .
* Navigate to the **"Subnets"** tab where the list of attached subnets will be visible.
* Click the **"Detach"** button next to the subnet you want to remove.
* A confirmation popup will appear. Click **"OK"** to detach the subnet.
* The subnet will be removed from the NAT Gateway. Verify the detachment by checking the updated list of attached subnets.

### **6. How to Destroy a NAT Gateway**

* From the  **NAT Gateway Manage Page** , navigate to the **"Destroy"** tab.
* Click the **"Destroy NAT Gateway"** button.
* A confirmation popup will ask for the exact name of the NAT Gateway.
* Enter the **exact name** of the NAT Gateway to confirm destruction and click  **"Destroy"** .
* After confirming, the NAT Gateway will be permanently destroyed. You will be redirected to the **NAT Gateway Listing Page** where you can verify its removal.

---

### **Conclusion**

This quick start guide provides the essential steps for managing NAT Gateways in Utho Cloud, from creating and viewing NAT Gateways to managing subnets and destroying unused gateways. With these capabilities, you can efficiently manage your cloud network’s access to the internet, while ensuring that internal resources remain secure.
