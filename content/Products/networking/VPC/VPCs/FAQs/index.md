---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "FAQs on VPC in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/VPCs/FAQs"]
icon: "globe"
tab: true
---

# Virtual Private Clouds (VPCs) : Frequently Asked Questions (FAQ)
## 1. What is a Virtual Private Cloud (VPC)? 🤔
A Virtual Private Cloud (VPC) is a private network within the Utho Cloud platform, allowing you to isolate your resources like virtual servers, storage, and other services. It gives you full control over your network configurations, such as IP address ranges, subnets, and routing.

---

## 2. How can I create a VPC in Utho Cloud? 🛠️
To create a VPC in Utho Cloud:
1. [**Login**](https://console.utho.com/login) to your Utho Cloud account.
2. Navigate to the VPC [**Listing Page**](https://console.utho.com/vpc).
3. Click on **Create VPC** and follow the steps to set the VPC’s name, network CIDR block, and region.
4. Once configured, click on **Create** to deploy your VPC.

---

## 3. How do I view the resources attached to my VPC? 👀
To view the resources attached to a VPC:
1. Go to the [**VPC Listing Page**](https://console.utho.com/vpc).
2. Select the VPC whose resources you want to view.
3. On the VPC management page, click on the **Resources** tab to see all the resources like cloud servers attached to the VPC.

---

## 4. Can I destroy a VPC after it's created? 💥
Yes, you can destroy a VPC at any time. To destroy a VPC:
1. Go to the **VPC Management Page**.
2. Click on the **Destroy VPC** tab.
3. Enter the VPC name for confirmation and click **Destroy**.

Please note that this action is permanent and cannot be undone, so ensure that you’ve backed up all necessary data before proceeding.

---

## 5. How do I view the subnets attached to a VPC? 🌐
To view the subnets attached to your VPC:
1. From the [**VPC Listing Page**](https://console.utho.com/vpc), select the VPC.
2. On the **VPC Management Page**, click the **Subnet** tab.
3. This will display all the subnets, including their type (public/private) and status (active/inactive).

---

## 6. What is the purpose of a subnet in a VPC? 🌳
A subnet is a smaller network within your VPC. It helps organize your cloud resources by grouping them based on specific needs (e.g., public-facing or internal). Subnets improve security and traffic management within your VPC by isolating network segments.

---

## 7. Can I have both public and private subnets in a VPC? 🏠
Yes, you can have both public and private subnets within a VPC. Public subnets are accessible from the internet, while private subnets are isolated from direct internet access. This setup allows you to control the accessibility and security of your resources.

---
## 8. How can I modify or delete a VPC in Utho Cloud? ✏️❌  
To modify or delete a VPC in Utho Cloud:  
1. Go to the [**VPC Listing Page**](https://console.utho.com/vpc).  
2. Click on the **Action** button next to the VPC you want to manage.  
3. From there, you can edit the VPC settings, view detailed configurations, or delete the VPC if necessary.
---

## 9. Can I change the CIDR block of a VPC after creation? 🔄
No, once a VPC is created with a specific CIDR block, it cannot be changed. If you need a different range, you must create a new VPC and migrate your resources accordingly.

---

## 10. How do I know which data center my VPC is deployed in? 🏢
The data center location for your VPC is displayed on the VPC management page under **DC Location**. This shows the physical region or data center where your VPC and its resources are hosted.

---

## 11. How do I manage the resources in my VPC? ⚙️
To manage resources:
1. Navigate to the **Resources** tab within the **VPC Management Page**.
2. From there, you can view, update, or delete the cloud servers, storage, and other services attached to your VPC.

---

## 12. What happens if I accidentally destroy a VPC? ⚠️
Once a VPC is destroyed, it cannot be recovered. All attached resources, including cloud servers and subnets, are permanently deleted. Always double-check the VPC name before confirming the destruction to avoid accidental deletions.

---

## 13. How can I delete a subnet from a VPC? ❌
To delete a subnet:
1. Go to the **Subnet** tab on the **VPC Management Page**.
2. Select the subnet you want to delete and click on the **Manage** button.
3. From the subnet’s management page, you can delete the subnet if it’s no longer in use.

---

## 14. How do I check the status of my VPC? 🔍
You can check the status of your VPC on the [**VPC Listing Page**](https://console.utho.com/vpc) or within the **VPC Management Page**. The status will indicate whether the VPC is **Active** or **Inactive**, helping you monitor its availability.

---

## 15. How do I modify a VPC after it's created? 🛠️
To modify a VPC:
1. Select the VPC from the [**VPC Listing Page**](https://console.utho.com/vpc).
2. Go to the **VPC Management Page**.
3. Use the available options to change configurations like subnets.

---

## 16. What is the VPC range and why is it important? 🌐
The **VPC range** refers to the IP address range (CIDR block) assigned to your VPC. It’s essential because it determines the pool of IP addresses available for your resources, such as cloud servers, and ensures they don’t overlap with other VPCs in your network.

---

Feel free to reach out if you need more detailed help or have additional questions! 😊
