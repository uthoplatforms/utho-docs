---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "FAQs on Monitoring in Utho Coud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "monitoring", "resource alerts", "cloud monitoring", "alert contacts"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/Monitoring/FAQs"]
icon: "globe"
tab: true
---

# **Monitoring - Frequently Aaked Questions (FAQ)**

## 1. **What is Monitoring in Utho Cloud? 🤔**
Monitoring in Utho Cloud helps you track and manage the health and performance of your cloud resources, such as EC2 instances, CPU, memory, disk activity, and network traffic. It ensures that you are notified about any critical events or thresholds so that you can take timely action.

## 2. **How can I create a resource alert? 📣**
You can create a resource alert to monitor different parameters like CPU usage, memory, disk activity, or network performance. Here’s how:
- **Step 1:** [**Login**](https://console.utho.com/login) to the Utho Cloud Platform.
- **Step 2:** Go to the [**Monitoring**](https://console.utho.com/monitoring) page and select the **Resource Alerts** tab.
- **Step 3:** Click on **Create Alert** and fill out the required details like name, resource type, threshold value, and more.
- **Step 4:** Hit **Create Alert** to set it up!

## 3. **How can I view my resource alerts? 👀**
To view your resource alerts:
- [**Login**](https://console.utho.com/login) to Utho Cloud.
- Navigate to the [**Monitoring**](https://console.utho.com/monitoring).
- Select the **Resource Alerts** tab to see a list of all your active alerts. You'll see details like the alert name, type, threshold values, and duration.

## 4. **How do I update an existing resource alert? ✏️**
To update an alert:
- Go to the **Resource Alerts** tab in the [**Monitoring**](https://console.utho.com/monitoring).
- Click on the **Update** button next to the alert you want to modify.
- Change the necessary details like resource type, threshold values, or contacts.
- Click **Update Alert** to save your changes.

## 5. **How do I delete a resource alert? ❌**
If you want to remove an alert, follow these steps:
- Go to the **Resource Alerts** tab.
- Find the alert you want to delete.
- Click on the **Delete** button next to the alert.
- A confirmation popup will appear. Click **OK** to confirm the deletion.

## 6. **What types of resources can I monitor? ⚙️**
You can monitor various types of resources, including:
- **CPU Utilization**: Monitor the percentage of CPU usage.
- **Memory Utilization**: Track the percentage of memory usage.
- **Disk Read/Write**: Monitor data read or written to disk.
- **Network Traffic**: Track incoming and outgoing network data.

## 7. **How can I set the alert threshold? ⚖️**
When creating or updating an alert, you can set the threshold for when the alert should be triggered. You can specify conditions like:
- **Is Below**: Trigger the alert when the value drops below the threshold.
- **Is Equal To**: Trigger the alert when the value exactly matches the threshold.
- **Is Above**: Trigger the alert when the value exceeds the threshold.

## 8. **Can I monitor multiple cloud instances with a single alert? 🌐**
Yes! You can select multiple cloud instances to be monitored under a single resource alert. Simply select the desired instances from the dropdown when creating or updating the alert.

## 9. **How do I manage notifications for my alerts? 📲**
You can add monitoring contacts to your alerts to receive notifications when the alert is triggered. Ensure that you’ve set up your monitoring contacts beforehand. When creating or updating an alert, select the appropriate contacts to be notified.

## 10. **Can I set an alert duration? ⏳**
Yes! When creating or updating an alert, you can set the duration for how long the condition should persist before the alert is triggered. Available options include 5 minutes, 10 minutes, 30 minutes, and more.

## 11. **Can I create alerts for different types of metrics? 📊**
Yes, you can create alerts for various metrics like CPU, memory, disk write/read speeds, and network usage. These metrics help you keep track of the performance and health of your cloud resources.

## 12. **What happens if an alert is triggered? 🚨**
When an alert is triggered, you will receive notifications via the monitoring contacts you have set up. These notifications will inform you about the resource that triggered the alert and the condition that caused it.

## 13. **What happens if I don't respond to an alert? ⚠️**
If you do not respond to an alert, the system will continue monitoring the resource. However, it’s important to investigate and address the issue to avoid any potential service disruptions or performance issues.

## 14. **Can I have different alert settings for different users? 🧑‍💻**
Yes! You can assign monitoring contacts to specific resource alerts, allowing different users to receive notifications for various alerts. This way, you can ensure that the right people are notified about specific issues.

