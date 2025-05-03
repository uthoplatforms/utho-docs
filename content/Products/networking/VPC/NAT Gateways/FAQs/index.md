---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "FAQs on NAT Gateways in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/NAT Gateways/FAQs"]
icon: "globe"
tab: true
---

# **NAT Gateways : Frequently Asked Questions (FAQ)**

## **1. What is a NAT Gateway in Utho Cloud? 🤔**

A NAT Gateway allows resources in a private subnet to access the internet securely while preventing inbound internet traffic. It's commonly used to enable outbound communication from instances in private subnets, like software updates or API calls.

## **2. How do I view all the NAT Gateways in my Utho Cloud account? 👀**

To view all the NAT Gateways in your account:
1. [**Login**](https://console.utho.com/login) to the Utho Cloud Platform.
2. Navigate to the **VPC** section.
3. Select **NAT Gateways** to view the [**listing**](https://console.utho.com/vpc/natgateways) of all your NAT Gateways.

## **3. How can I view the attached subnets to a NAT Gateway? 🌐**

To view attached subnets:
1. Go to the [**NAT Gateway Listing Page**](https://console.utho.com/vpc/natgateways).
2. Select the NAT Gateway you want to manage.
3. Click on the **Manage** button.
4. In the **NAT Gateway Manage Page**, go to the **Subnet Tab** to view all the attached subnets.

## **4. How can I destroy a NAT Gateway in Utho Cloud? 💥**

To destroy a NAT Gateway:
1. Navigate to the **NAT Gateway Manage Page**.
2. Go to the **Destroy Tab**.
3. Click on the **Destroy NAT Gateway** button.
4. Confirm the destruction by entering the NAT Gateway's exact name.

## **5. Can I attach new subnets to an existing NAT Gateway? 🔗**

Yes, you can attach new subnets to an existing NAT Gateway:
1. Go to the **NAT Gateway Manage Page**.
2. Select the **Subnets Tab**.
3. Click on **Attach Subnets** and follow the instructions to attach a new subnet to the gateway.

## **6. Can I detach subnets from a NAT Gateway? ❌**

Yes, you can detach subnets:
1. On the **NAT Gateway Manage Page**, go to the **Subnets Tab**.
2. Click on **Detach** next to the subnet you want to remove.
3. The subnet will be detached immediately without requiring confirmation.

## **7. How do I know if a subnet is the default one for my NAT Gateway? 🔍**

On the **Subnets Tab** in the **NAT Gateway Manage Page**, you’ll see the **Default** column indicating whether a subnet is set as the default. If it’s marked "Yes," it means it's the primary subnet for the NAT Gateway.

## **8. How can I troubleshoot if my NAT Gateway is not working? 🛠️**

If your NAT Gateway is not working:
1. Check the **Status** field in the **NAT Gateway Manage Page**. If it's marked as **Inactive**, try restarting or recreating the gateway.
2. Ensure that subnets are properly attached and active.
   

## **9. What is the difference between "Active" and "Inactive" status for a NAT Gateway? 🧐**

- **Active**: The NAT Gateway is functioning and can route internet traffic.
- **Inactive**: The NAT Gateway is not operational, and subnets attached to it will not have internet access until it is activated.

---
