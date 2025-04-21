---
weight: 40
title: "Quick Start"
title_meta: "Quick Start"
description: "Quick Start Guide on VPCs in utho cloud platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/VPCs/Getting Started/Quick Start"]
icon: "globe"
tab: true
---



## **Quick Start Guide for VPCs in Utho Cloud**

### **Overview**

Virtual Private Clouds (VPCs) in Utho Cloud provide users with a secure, isolated network within the cloud to host resources like cloud servers, storage, and other services. This guide will walk you through the essential steps for managing VPCs, including how to deploy, view, manage, and destroy VPCs, as well as how to create and manage subnets within them.

---

### **1. How to View VPCs**

* **[Login](https://console.utho.com/login)** to your Utho Cloud account.
* From the **VPC** menu in the sidebar, select **VPCs** to view the  **[VPC Listing Page](https://console.utho.com/vpc)** .
* The list shows all previously deployed VPCs along with details like:
  * **Location** : The DC Location of the VPC.
  * **Name** : The VPC’s name.
  * **Network** : The IP range or network associated with the VPC (e.g., 10.137.0.0/20).
  * **Action** : The button to manage the VPC’s resources.

### **2. How to Deploy a New VPC**

* Go to the **VPC Listing Page** and click the **"Create New VPC"** button.
* Fill in the required details:
  * **DC Location** : Choose the datacenter location for the VPC.
  * **VPC Name** : Enter a name for the VPC.
  * **Network** : Enter an IP range for the VPC (e.g., 10.0.0.0/16).
  * **Network Size** : Specify the network size (e.g., 24).
* Click the **"Deploy VPC"** button to deploy the VPC.
* Once the deployment is complete, you’ll be redirected to the manage page of the deployed VPC.

### **3. How to Manage a VPC**

* After deploying the VPC, navigate to the  **VPC Listing Page** .
* Click the **"Manage"** button next to the VPC you want to manage.
* You will be redirected to the  **VPC Manage Page** . Here, you will find:
  * **VPC Details** : Name, DC location, range, and status of the VPC.
  * **Tabs for Further Configuration** :
  * **Resources** : Displays all resources (e.g., cloud servers) attached to the VPC.
  * **Subnet** : Lists all subnets attached to the VPC and gives the option to create new subnets.
  * **Destroy VPC** : Provides the option to permanently delete the VPC.

### **4. How to View Resources Attached to a VPC**

* Go to the **VPC Listing Page** and select the VPC you wish to view.
* Navigate to the **"Manage Page"** of the selected VPC.
* By default, the **Resources** tab will be selected, showing a list of all resources (e.g., cloud servers) attached to the VPC.
* You can view and manage attached resources from this section.

### **5. How to View Subnets Attached to a VPC**

* In the  **VPC Manage Page** , click on the **"Subnet"** tab to see the list of subnets.
* The list will display the following information:
  * **Name** : The name of the subnet.
  * **Subnet Type** : Whether the subnet is public or private.
  * **Status** : The status of the subnet (active or inactive).
  * **Manage Button** : Click this to navigate to the subnet's management page for further configuration.

### **6. How to Create a Subnet**

* On the  **VPC Manage Page** , go to the **"Subnet"** tab.
* Click the **"Create Subnet"** button at the top of the list.
* A drawer will appear where you need to fill in the following details:
  * **Subnet Name** : Provide a name for the subnet.
  * **Auto-assign Public IPv4 Address** : Choose whether to enable or disable automatic assignment of public IPv4 addresses.
  * **Default Subnet** : Choose Yes or No.
  * **Subnet Type** : Select whether the subnet will be Public or Private.
  * **Network** : Enter the IP network for the subnet (e.g., 192.168.1.0).
  * **IP Range** : Enter the subnet's CIDR block size (e.g., 24).
* After filling out the form, click **"Create Subnet"** to deploy the subnet.
* A success toast notification will confirm the subnet creation. You can verify the deployment by checking the updated list of subnets.

### **7. How to Destroy a VPC**

* From the  **VPC Listing Page** , click the **"Manage"** button next to the VPC you want to destroy.
* Navigate to the **"Destroy VPC"** tab in the  **VPC Manage Page** .
* Click the **"Destroy VPC"** button to open a confirmation popup.
* Enter the name of the VPC to confirm the destruction.
* After confirming the name, click **"OK"** to destroy the VPC.
* You will be redirected to the VPC Listing Page where you can verify if the VPC has been removed.

---

### **Conclusion**

By following these steps, you can efficiently manage your VPCs in Utho Cloud, including creating, deploying, viewing, managing, and destroying VPCs and their subnets. This helps you maintain a secure and well-organized cloud environment for your resources.
