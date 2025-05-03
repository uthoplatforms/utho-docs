---
weight: 40
title: "Overview"
title_meta: "Overview"
description: "Overview"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "monitoring", "resource alerts", "cloud monitoring", "alert contacts"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/Monitoring/Getting Started/Overview"]
icon: "globe"
tab: true
---

## **Introduction**

Monitoring in Utho Cloud Platform allows users to track the performance and health of their cloud resources, such as servers, and receive alerts when certain thresholds are met. The platform provides two key functionalities: **Resource Alerts** and  **Monitoring Contacts** .

Resource Alerts enable users to monitor key metrics like CPU utilization, memory usage, and disk activity, ensuring that any performance issues are detected early. Monitoring Contacts ensure that the right individuals or teams are notified when these issues arise.

## **Purpose**

The primary purpose of the **Monitoring** service in Utho Cloud is to provide proactive insights into the performance of cloud resources. By setting up resource alerts, users can:

* Monitor critical server metrics in real-time.
* Receive notifications when server performance thresholds are breached.
* Ensure that appropriate personnel are notified via Monitoring Contacts when issues occur.

With this service, users can avoid performance degradation, potential downtime, and optimize their resources.

## **Benefits of Using Monitoring**

1. **Proactive Performance Management** :

* By setting up alerts for critical metrics, you can be notified immediately if there are any performance issues, such as high CPU utilization or excessive memory usage, allowing you to take action before the problem affects operations.

2. **Customization** :

* Users can customize alerts based on various performance indicators, such as CPU, memory, disk I/O, and more. This allows for tailored monitoring based on individual needs.

3. **Resource Optimization** :

* Monitoring helps in identifying underutilized or overutilized resources, enabling you to optimize cloud resource allocation. For example, scaling up or down based on the utilization data.

4. **Seamless Integration** :

* Integrated into the Utho Cloud platform, monitoring is a seamless and automated feature that works across all deployed cloud resources.

5. **Improved Security** :

* Monitoring helps track abnormal activities and ensures the health of your cloud resources, which contributes to the security of your cloud environment.

## **How Utho’s Monitoring Service Works**

### **1. Resource Alerts Setup**

* **Monitor Performance** : The **Resource Alerts** feature allows users to define specific metrics (like CPU usage, memory consumption, etc.) and set thresholds. When the resource's performance surpasses or falls below these thresholds for a specified duration, the system triggers an alert.
* **Alert Conditions** : Users can specify:
* **Metric Type** : Select from CPU, Memory, Disk I/O, etc.
* **Comparison Condition** : Set conditions like "is below," "is equal to," or "is above" a specific value.
* **Resource Instance** : Choose specific cloud servers to monitor.
* **Alert Duration** : Define how long the threshold must be exceeded before an alert is triggered.
* **Notifications** : When the condition is met, the system sends notifications to the monitoring contacts, allowing quick response and issue resolution.

### **2. Monitoring Contacts**

* **Create Contacts** : In the **Monitoring Contacts** section, you can create contacts by providing their email, name, and phone number. This ensures that the right people are notified if an alert is triggered.
* **Notifying the Right Person** : Alerts from the resource monitoring can be sent to one or more of the created contacts, ensuring timely interventions.

### **3. Continuous Monitoring**

* **Real-Time Data** : The monitoring system continuously gathers and tracks data, providing users with real-time performance insights and alerting them about any unusual patterns.

### **4. Customizable Alerts**

* **Flexibility** : Users have full control over the configuration of alerts, choosing metrics, thresholds, and the duration for which the conditions must be met before triggering an alert.
* **Resource-Specific Monitoring** : Resource alerts can be applied individually to each cloud server, providing tailored monitoring for different resources in the cloud environment.

## **System Requirements**

To fully leverage the monitoring features in Utho Cloud, the following system requirements should be considered:

* **Cloud Platform Access** : A valid Utho Cloud account with the necessary permissions to manage resources and configure alerts.
* **Supported Resources** : The monitoring system supports any deployed cloud server in Utho’s infrastructure. Make sure your cloud servers are properly deployed and accessible for monitoring.
* **Alert Configuration** : Set up the necessary alert parameters in the "Resource Alerts" section to ensure accurate and relevant performance monitoring.
* **Network Connectivity** : A stable internet connection is required for real-time monitoring and the efficient transmission of alert notifications.
* **Email and Phone Setup for Monitoring Contacts** : Ensure that you have the correct contact details for the people who will be notified about critical performance issues.

## **Conclusion**

Utho’s Monitoring service empowers users to keep their cloud infrastructure healthy and efficient by providing real-time insights into the performance of their resources. With the ability to create resource alerts and manage monitoring contacts, users can ensure that performance issues are quickly addressed, reducing the risk of downtime and enhancing resource optimization.

By utilizing Utho's Monitoring service, you can maintain a proactive approach to managing cloud resource health, improving both operational efficiency and security.
