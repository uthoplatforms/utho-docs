---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "FAQS on Auto Scaling in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["auto scaling", "cloud auto scaling", "scaling groups", "load balancing", "automatic resource scaling"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/compute/Auto Scaling/FAQs"]
icon: "globe"
tab: true
---

# **Auto Scaling - Frequently Asked Questions (FAQ)**

## **1. What is Auto Scaling? 🚀**
**Auto Scaling** is a feature that automatically adjusts the number of running instances in your cloud environment based on your application’s traffic and performance demands. It helps maintain optimal performance and reduces operational costs by scaling resources up or down as needed.

## **2. How do I create an Auto Scaling instance? 🛠️**
To create an Auto Scaling instance, follow these steps:
1. [**Log in**](https://console.utho.com/login) to your account.
2. Navigate to the [**Auto Scaling**](https://console.utho.com/auto-scaling) section.
3. Click on **Create New**, configure your instance by specifying the desired scaling configurations (like min, max, and desired instance size), and choose the deployment environment (stack and image).
4. After confirming your settings, click **Deploy Autoscaling** to deploy the instance.

For detailed steps, refer to the guide on **Creating an Auto Scaling Instance**.

## **3. How do I update the scaling configuration of my Auto Scaling instance? ⚙️**
You can update your scaling configuration directly from the **Manage** page of your Auto Scaling instance:
1. Navigate to the  [**Auto Scaling**](https://console.utho.com/auto-scaling) listing page.
2. Select the instance you wish to modify.
3. In the **Overview** section, click the **Update Scaling Configuration** button.
4. Modify parameters like **Min Size**, **Max Size**, and **Desired Size** according to your new requirements.
5. Click **Update** to save your changes.

For more information, check the guide on **Updating Scaling Configuration**.

## **4. Can I change the scaling policy for my Auto Scaling instance? 🔄**
Yes, you can change the scaling policy to modify the conditions under which your instances scale up or down. To do so:
1. Navigate to the **Manage** page of your Auto Scaling instance.
2. Scroll to the **Scaling Policy** section.
3. Click on the **Edit** icon for the desired scaling policy.
4. Update the **threshold values**, such as CPU or RAM utilization, scaling actions, and cooldown period.
5. Click **Update Policy** to apply the new settings.

For more details, refer to **Updating Scaling Policy**.

## **5. How do I detach a target group from an Auto Scaling instance? 🚫**
To detach a target group from your Auto Scaling instance:
1. Go to the  [**Auto Scaling**](https://console.utho.com/auto-scaling)  listing page.
2. Select the instance and click **Manage**.
3. In the **Load Balancers** section, find the attached target group.
4. Click the **Detach** button next to the target group you want to remove.
5. Confirm the detachment, and the target group will be removed from the instance.

## **6. Can I schedule scaling actions at specific times? 📅**
Yes, you can configure scaling schedules to automatically scale your instances based on a predefined time and recurrence. To update or create a scaling schedule:
1. Navigate to the **Scaling Schedules** section in the **Manage** page.
2. Click on the **Edit** icon for the desired schedule.
3. Modify the **Desired Size**, **Time Zone**, **Recurrence**, and other configurations.
4. Click **Update Schedule** to save the changes.

## **7. How can I view the scaling history for my Auto Scaling instance? 📜**
To view the scaling history of your instance:
1. Navigate to the **Manage** page for the selected Auto Scaling instance.
2. Scroll down to the **History** section.
3. Review the log of past actions, such as usage time, creation date and deletion date.


## **8. How do I update the deployment configuration of my Auto Scaling instance? 🔧**
You can modify the deployment configuration to change the stack, image, and snapshots associated with your Auto Scaling instance:
1. Go to the **Manage** page of your instance.
2. In the **Overview** section, click the **Deployment Configuration** button.
3. Select the new **Stack** and **Image**, and optionally choose a snapshot for recovery.
4. Click **Update** to apply the changes.

For step-by-step instructions, check the **Update Deployment Configuration** guide.

## **9. What happens when I update the scaling configuration? 🔄**
Updating the scaling configuration allows you to adjust the minimum, maximum, and desired number of instances in your Auto Scaling group. This ensures that your application has the right amount of resources based on the current demand.

Changes to scaling parameters may:
- Increase or decrease resource availability.
- Impact costs based on the number of instances required to meet the new configuration.


## **10. What are the impacts of updating a scaling policy? ⚖️**
When you update a scaling policy, you modify the rules that dictate when the system will add or remove instances based on resource utilization (such as CPU or RAM). The main impacts include:
- **Optimized scaling**: Adjusting thresholds and scaling actions ensures that your instance can better handle peak demand or reduce resources during low demand.
- **Cost control**: Fine-tuning the scaling policy can help minimize unnecessary resource consumption and avoid high costs during idle periods.

## **11. How can I destroy an Auto Scaling instance? 💥**
To permanently destroy an Auto Scaling instance:
1. Navigate to the **Manage** page of the instance you want to delete.
2. Scroll to the **Destroy** section.
3. Click **Destroy** to permanently remove the instance and all associated configurations.

**Note:** This action is irreversible. Make sure to back up any critical data before proceeding.


## **12. How do I ensure high availability with Auto Scaling? 🌐**
To ensure high availability, you should:
- Set a **minimum size** for your instances to guarantee a baseline of resources are always available.
- Use **scaling policies** that scale up based on resource usage or scaling schedules during peak times.
- Attach **load balancers** to distribute traffic across your instances for better availability and performance.

## **13. What is the difference between **Desired Size**, **Min Size**, and **Max Size** in Auto Scaling? 📊**
- **Desired Size**: The ideal number of instances you want to maintain. Auto Scaling will try to keep this number of instances running based on demand.
- **Min Size**: The minimum number of instances that should be running, ensuring your application is always available even during low traffic periods.
- **Max Size**: The maximum number of instances that can run, limiting costs and preventing resource over-provisioning during periods of high demand.

## **14. How do I handle scaling issues or unexpected behavior? ⚠️**
If your instances are not scaling as expected:
1. Check the **scaling policies** and ensure the resource thresholds (e.g., CPU, RAM) are correctly configured.
2. Review the **scaling schedules** to confirm that the instance is scaling at the right times.
3. Ensure that there is no conflict with other settings like **load balancers** or **target groups**.
4. If necessary, contact support for further troubleshooting.
