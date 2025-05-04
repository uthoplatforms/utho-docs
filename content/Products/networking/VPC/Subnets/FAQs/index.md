---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "Overview of NAT Gateways in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/Subnets/FAQs"]
icon: "globe"
tab: true
---

# **Subnet - Frequently Asked Quesitons (FAQ)**

### **1. What is a subnet in Utho Cloud?** 🤔  
A subnet in Utho Cloud is a segment of your Virtual Private Cloud (VPC) that divides the network into smaller, manageable sections. It helps in organizing resources and controlling network traffic.

### **2. How do I create a subnet in Utho Cloud?** 🛠️  
You can create a subnet by navigating to the subnets listing page in Utho Cloud. There, you’ll find a "Create Subnet" button. Clicking on this button opens a drawer where you can enter your subnet's configurations and create it.


### **3. How do I view the configuration of my subnet?** 👀  
To view the configuration, go to the Subnet's Manage Page in the Utho Cloud console and click on the "Configuration" tab. There, you can see details like the subnet’s name, VPC ID, IP range, and type.

### **4. Can I attach multiple resources to a single subnet?** 📦  
Yes, you can attach multiple resources like cloud servers or network interfaces to a single subnet. These resources can be managed through the "Resources" tab in the subnet’s Manage Page.

### **5. What does the "Auto Assign Public IPV4 Address" option mean?** 🌐  
This option controls whether public IP addresses are automatically assigned to resources created in the subnet. Enabling it means that each resource will automatically receive a public IP.

### **6. How can I view the resources attached to my subnet?** 🔍  
To view the attached resources, go to the "Resources" tab on the subnet's Manage Page. It will display all the resources currently connected to the subnet.

### **7. What is the difference between public and private subnets?** 🌍  
A public subnet allows resources to communicate directly with the internet, while a private subnet restricts internet access. Public subnets typically host web servers, while private subnets contain databases or sensitive resources.

### **8. Can I change the subnet's CIDR block after creation?** 🔄  
No, you cannot change the CIDR block (IP range) of a subnet after it has been created. If you need a different range, you’ll have to create a new subnet.

### **9. How do I delete a subnet in Utho Cloud?** 🗑️  
To delete a subnet, navigate to its Manage Page, click on the "Destroy Subnet" tab, and confirm the deletion. Be aware that this action is permanent and will remove all resources within the subnet.

### **10. Can I attach a NAT Gateway to my subnet?** 🌐  
Yes, you can attach a NAT Gateway to a subnet to enable outbound internet access for resources in a private subnet. This is especially useful for instances that need to download updates or access external services.

### **11. Can I have subnets in different availability zones?** 📍  
Yes, Utho Cloud allows you to create subnets in different availability zones, providing high availability and fault tolerance for your resources.

### **12. Can I modify the default subnet?** 🛠️  
The default subnet can be modified in terms of its associated resources or routing, but you cannot change its fundamental attributes, such as IP range or VPC ID.

### **13. What is the "Subnet Type" in the configuration, and how do I choose between public and private?** 🏷️  
The "Subnet Type" determines whether your subnet is public or private. Public subnets are used for resources that need internet access, while private subnets are used for resources that don't require internet exposure.
