---
weight: 40
title: "Quick Start"
title_meta: "Quick Start"
description: "Quick Start Guide on Firewall in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "firewall", "network security", "access control", "cloud firewall"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/Firewall/Getting Started/Quick Start"]
icon: "globe"
tab: true
---


## **Quick Start Guide for Managing Firewalls in Utho Cloud**

A **Firewall** in Utho Cloud helps secure your cloud infrastructure by controlling incoming and outgoing network traffic. It acts as a barrier between your cloud servers and external networks, allowing you to define rules to permit or block specific types of traffic. Managing firewalls effectively ensures that only authorized traffic can access your servers while preventing unauthorized access, protecting sensitive data and resources.

### **1. How to Deploy a Firewall**

* **[Login](https://console.utho.com/login)** to your Utho Cloud Platform account.
* Navigate to the **Firewall Listing [Page](https://console.utho.com/firewall)** and click the **"Create New Firewall"** button to start the deployment process.
* Fill in the required details:
  * **Name** : Provide a unique name for your firewall.
* Click the **"Deploy Firewall"** button to initiate the deployment. Once deployed, you will be redirected to the firewall manage page.

### **2. How to Add Rules to a Firewall**

* Navigate to the **Manage Page** of the firewall.
* By default, you will be on the **Rules** tab.
* To add a new rule, select either **Incoming** or **Outgoing** rule, and click the **"Add New"** button.
* For each rule, fill in the following details:
  * **Service** : Select from options like SSH, RDP, HTTP, etc.
  * **Protocol** : Typically TCP for custom rules.
  * **Port Range** : Enter the port range for the service.
  * **Sources** : Define the source (default is ALL).
* After filling in the details, click the **"Add New"** button to apply the rule.

### **3. How to Delete Rules from a Firewall**

* Go to the **Manage Page** of the firewall.
* Navigate to the **Rules** section.
* For each rule (incoming or outgoing), at the end of the list, click the **"Delete"** button.
* A confirmation popup will appear. Click **OK** to delete the rule.
* Verify by checking the list of incoming and outgoing rules.

### **4. How to Add Servers to a Firewall**

* Navigate to the **Manage Page** of the firewall.
* In the top-right corner, click the **"Servers"** tab.
* From the dropdown, select the server you want to add to the firewall.
* Click the **"Add Server"** button.
* The server will be added, and you can verify the addition by checking the list of attached servers below.

### **5. How to Delete Servers from a Firewall**

* Go to the **Manage Page** of the firewall.
* Click on the **"Servers"** tab.
* In the list of attached servers, click the **"Delete"** button next to the server you want to remove.
* A confirmation popup will appear. Click **OK** to delete the server from the firewall.
* Verify by checking the list of attached servers again.

### **6. How to Destroy a Firewall**

* Navigate to the **Manage Page** of the firewall.
* Click the **"Destroy"** tab in the top-right corner.
* A warning message will appear informing you that destroying the firewall will dissociate all attached servers.
* Click the **"Destroy Firewall"** button to initiate the process.
* A confirmation popup will appear. Click **OK** to confirm.
* You will be redirected to the **Firewall Listing Page** to verify that the firewall has been destroyed.

### **7. How to Upload Rules to a Firewall**

* Go to the **Manage Page** of the firewall.
* Click the **"Upload Rules"** button at the top.
* Select the **.csv** file containing your custom firewall rules.
* Once the file is uploaded, go to the **Rules** tab to verify if the rules have been successfully added.

### **8. How to Export Firewall Rules**

* Go to the **Rules** tab in the  **Manage Page** .
* Click the **"Export Rules"** button at the top of the page.
* Choose whether to export **Incoming** or **Outgoing** rules.
* Select the file format (CSV, Excel, or PDF).
* Click the **"Export Rules"** button to download the rules to your local system.

---

By following these simple steps, you can easily deploy, manage, and secure your firewalls in Utho Cloud.
