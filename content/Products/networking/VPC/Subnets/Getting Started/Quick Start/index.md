---
weight: 40
title: "Quick Start"
title_meta: "Quick Start"
description: "Quick Start guide on subnets in utho cloud platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/Subnets/Getting Started/Quick Start"]
icon: "globe"
tab: true
---


## **Quick Start Guide for Subnets in Utho Cloud**

### **Overview**

Subnets in Utho Cloud allow you to create isolated network environments within your Virtual Private Cloud (VPC). This guide provides a concise overview of how to view, create, manage, and destroy subnets, as well as how to attach and detach NAT Gateways.

---

### **1. How to View Subnets**

* **[Login](https://console.utho.com/login)** to your Utho Cloud account.
* From the **VPC** menu in the sidebar, select **Subnets** to view the  **[Subnet Listing Page](https://console.utho.com/vpc/subnets "Subnets Listing Page")** .
* The list will show all the subnets created, with the following details:
  * **Name** : The name of the subnet.
  * **Subnet ID** : Unique identifier for the subnet.
  * **IPV4** : The subnet’s IP address.
  * **Default** : Indicates whether the subnet is the default one for the VPC.
  * **VPC ID** : The ID of the VPC to which the subnet belongs.
  * **Manage Button** : Access to manage the subnet's settings and configurations.

---

### **2. How to Create a Subnet**

* Go to the **Subnet Listing Page** and click the **"Create Subnet"** button.
* Fill in the details for your subnet:
  * **Subnet Name** : Provide a name for the subnet.
  * **Choose VPC** : Select the VPC where you want to create the subnet.
  * **Auto Assign Public IPv4 Address** : Choose whether to enable or disable public IP assignment.
  * **Default Subnet** : Set this as Yes or No based on your preference.
  * **Subnet Type** : Choose between Public or Private subnet.
  * **Network** : Enter the subnet's IP network (e.g., 192.168.1.0).
  * **IP Range** : Specify the subnet's CIDR block size (e.g., 24).
* After filling out the form, click  **"Create Subnet"** .
* You will receive a success notification once the subnet is created, and you can verify it by checking the updated subnet list.

---

### **3. How to View Subnet Configuration**

* Go to the **Subnet Listing Page** and click on the **Manage** button for the subnet you want to view.
* By default, the **Configuration** tab will show the subnet’s configuration details, including:
  * **Name**
  * **DC Location**
  * **Status** (Running if active)
  * **VPC ID**
  * **IP Range**
  * **Subnet Type** (Public/Private)
  * **Auto Assign Public IPV4** (Enabled/Disabled)
  * **Default** (Yes/No)

---

### **4. How to Attach NAT Gateway to a Subnet**

* Go to the **Manage Page** of the desired subnet.
* In the **"NAT Gateway"** tab, click the **"Add Nat Gateway"** button.
* A drawer will appear with a dropdown list of available NAT Gateways. Select the NAT Gateway you want to attach.
* Click **"Add Nat Gateway"** to attach the selected NAT Gateway to the subnet.
* You can verify the attachment by checking the updated list of attached NAT Gateways in the **"NAT Gateway"** tab.

---

### **5. How to Detach NAT Gateway from a Subnet**

* In the **Manage Page** of the subnet, go to the **"NAT Gateway"** tab.
* For each attached NAT Gateway, there will be a **"Detach"** button.
* Click the **"Detach"** button next to the NAT Gateway you want to remove.
* The NAT Gateway will be immediately detached, and no confirmation will be required.

---

### **6. How to View Attached Resources**

* Go to the **Manage Page** of the subnet.
* Click on the **"Resources"** tab to view a list of all resources (like cloud servers or storage) attached to the subnet.

---

### **7. How to Destroy a Subnet**

* In the  **Subnet Listing Page** , click the **"Manage"** button next to the subnet you wish to destroy.
* Navigate to the **"Destroy Subnet"** tab.
* Click the **"Destroy Subnet"** button.
* A confirmation popup will appear. Enter the exact name of the subnet to confirm.
* After confirming, click **"Destroy"** to permanently delete the subnet.
* You will be redirected to the Subnet Listing Page where you can verify that the subnet has been destroyed.

---

### **Conclusion**

With these steps, you can quickly and efficiently manage your subnets in Utho Cloud. From creating and configuring subnets to managing attached resources and NAT Gateways, this guide ensures you can handle all subnet management tasks with ease.
