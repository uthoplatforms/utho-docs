---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "FAQs on Peering Connections in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/Peering Connections/FAQs"]
icon: "globe"
tab: true
---
# **Peering Connections : Frequently Asked Questions (FAQ)**

---

### 1. **What is a Peering Connection and why would I need one? 🤔**
A Peering Connection allows two Virtual Private Clouds (VPCs) to communicate directly using private IP addresses, without going over the public internet. It’s useful when you want to enable secure, low-latency connectivity between different environments or services hosted in separate VPCs.


### 2. **How do I create a Peering Connection in Utho Cloud? 🛠️**
You can create a peering connection by navigating to [**Peering Connections**](https://console.utho.com/vpc/peeringconnection), clicking on **"Create Peering Connection"**, and selecting the relevant requester and accepter VPCs (and subnets, if available). Fill out the form and submit—your connection will appear in the list once it's created.


### **Where can I see all my Peering Connections? 📋**
Navigate to **Peering Connections** listing page. You’ll see a table with all connections, their requester/accepter details, status, and creation date.


### **How do I know if a peering connection is active or not? 🔍**
In the **Peering Connections Listing Page**, there’s a **Status** column that shows whether a connection is **Active** or **Inactive**. If it's inactive, it may mean one side hasn’t accepted the connection or there’s a configuration issue.


### **Can I delete a peering connection if I no longer need it? 🗑️**
Absolutely. Head to the **Peering Connections Listing Page**, find the connection you want to delete, and click the **Delete** button. Confirm your choice in the popup, and it’ll be removed from the list.


### **Will deleting a peering connection interrupt services? ⚠️**
Yes, any resources relying on that peering link for communication will lose connectivity. Make sure to update your architecture or reroute traffic before deleting a peering connection.


### **Do I need to configure routes for Peering Connections to work? 🧭**
Yes. After establishing the connection, you’ll need to update the **Route Tables** in both VPCs to allow traffic to flow between them. Without this step, the VPCs won’t be able to communicate.


### **Is there any cost associated with creating Peering Connections? 💰**
As of now, Utho Cloud does not charge for creating peering connections. However, regular data transfer fees between VPCs might apply. It's best to check the pricing page or contact support for details.


### **Can I peer more than two VPCs together? 🔄**
Peering is a one-to-one relationship. If you need a full mesh between multiple VPCs, you’ll need to create multiple peering connections or consider using a transit gateway solution if supported.


### **Can I peer with another user’s VPC? 👥**
Currently, peering is supported only within the same Utho Cloud account. Cross-account peering may be considered in future roadmap updates.


### **Do subnets matter in Peering Connections? 🧩**
Yes. If your VPCs have subnets, you can choose specific subnets to associate with the peering connection during setup. This lets you scope connectivity more precisely.


### **What happens if I pick the wrong VPC or subnet while creating a connection? 🛑**
You’ll need to delete the existing connection and recreate it with the correct parameters, as there's no edit option for VPC/subnet selection after creation.


### **How long does it take for a Peering Connection to become active? ⏱️**
Most peering connections become active almost instantly after both sides are configured correctly. However, delays might occur if subnet routing or network ACLs are not properly set.


### **Is there a way to audit who created or deleted a Peering Connection? 📜**
Not directly from the listing page. For auditing and logs, please refer to the platform's **Activity Logs** or enable **Monitoring & Alerts** for better tracking.


### **Still have questions? Reach out to Utho Support anytime! 🧑‍💻**