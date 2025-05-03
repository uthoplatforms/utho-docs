---
weight: 40
title: "View Alerts"
title_meta: "View Alerts"
description: "Guide on how to view resource alerts in utho cloud platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "monitoring", "resource alerts", "cloud monitoring", "alert contacts"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/Monitoring/How Tos/Resoruce Alerts/View Alerts"]
icon: "globe"
tab: true
---


## **How to View Resource Alerts**

This guide will walk you through the steps to view the resource alerts on your Utho Cloud Monitoring page. Resource alerts help you keep track of various system parameters such as CPU usage, memory utilization, disk activity, and network performance. These alerts notify you when specific thresholds are met for the selected resources.

### **1. Log in to Utho Cloud Platform**

* Visit the Utho Cloud Platform's  **[login page](https://console.utho.com/login)** .
* Enter your credentials and click  **Login** .
* If you’re not registered, sign up  **[here](https://console.utho.com/signup)** .

### **2. Navigate to the Monitoring Listing Page**

* After logging in, go to the **Monitoring Listing Page** to view all monitoring-related data.
* This page can be accessed directly via the **Monitoring** tab or by clicking  **[here](https://console.utho.com/monitoring "Monitoring Listing Page")** .

### **3. Access the "Resource Alerts" Tab**

* By default, the **"Resource Alerts"** tab will be selected on the  **Monitoring Listing Page** .
* If it is not selected, click on the **"Resource Alerts"** tab to load the data.

  ![1744029126457](image/index/1744029126457.png)

### **4. View Resource Alerts**

Once the **"Resource Alerts"** tab is selected, you will see a list of all resource alerts associated with your account. Each alert contains the following information:

* **Name** : The identifier for the resource alert, helping you easily distinguish between different alerts.
* **Type** : This field indicates the resource being monitored, such as  **CPU** ,  **Memory** ,  **Disk** , or  **Network** . Each type represents a different system metric you are tracking.
* **Comparison** : Shows the comparison condition used for triggering the alert. It can be one of the following:
  * **Is Above** : The alert will be triggered if the resource metric exceeds the specified value.
  * **Is Below** : The alert will be triggered if the resource metric falls below the specified value.
  * **Is Equal To** : The alert will trigger if the resource metric exactly matches the specified value.
* **Value** : This numeric value is the threshold that, when exceeded or matched, will trigger the alert. For example, if monitoring CPU usage, the value might be 80% to indicate an alert if CPU usage goes over 80%.
* **Duration** : This represents the length of time that the condition must persist for the alert to be triggered. Options might include:
  * **5 mins**
  * **10 mins**
  * **30 mins**
  * etc.

### **5. Action Buttons**

Each resource alert will have the following action buttons for management:

* **Update** :
  * Allows you to modify the resource alert. When you click this button, you can change the  **Name** ,  **Type** ,  **Comparison** ,  **Value** , and  **Duration** .
* **Delete** :
  * Lets you delete a resource alert from the list. Clicking this button will remove the alert permanently.
