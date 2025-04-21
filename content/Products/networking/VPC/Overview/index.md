---
weight: 40
title: "Overview"
title_meta: "Overview"
description: "Overview of VPC in utho cloud platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/Overview"]
icon: "globe"
tab: true
---

### **VPC Overview in Utho Cloud**

A **VPC (Virtual Private Cloud)** in Utho Cloud is a dedicated, isolated network within the cloud environment. It allows you to launch and manage resources such as instances, databases, and load balancers in a secure and isolated environment. The VPC provides control over your network configurations, including IP address ranges, subnets, route tables, and network gateways.

Within a VPC, you can configure **subnets** for segmenting your network, create **NAT gateways** for secure internet access, allocate **Elastic IPs** for static public addresses, and establish **peering connections** to enable private network communication between different VPCs.

### **Common Uses of VPC and Related Sub-products**

* **Network Isolation:** VPC allows users to define isolated networks for their resources, ensuring secure access and network segmentation.
* **Scalable Network Configuration:** The combination of subnets, NAT gateways, and VPC routing allows for scalable network architectures.
* **High Availability:** Elastic IPs and peering connections can help ensure that your services are always reachable, even in the event of network changes or failures.
* **Security & Traffic Control:** With VPC, you can control which resources are exposed to the internet and which remain private, ensuring secure, traffic-filtered access.

---

### **VPC Sub-products in Utho Cloud**

1. **Subnets**
   * Subnets are smaller network segments within a VPC that define the address range for your resources. By splitting your VPC into subnets, you can isolate resources and manage traffic flow more efficiently.
   * **Key Features:**
     * Public and private subnets for secure network segmentation.
     * Ability to allocate specific IP ranges for each subnet.
2. **NAT Gateways**
   * A **NAT Gateway** allows private resources within your VPC to securely access the internet while remaining unreachable from outside the VPC. This is essential for security when you have instances that don't need to be directly exposed to the internet.
   * **Key Features:**
     * Enables secure internet access for private instances.
     * Ensures that outbound traffic from private subnets is routed correctly.
3. **VPCs**
   * The VPC itself is the overarching network container within which subnets, routing, and network configurations are defined. It defines the IP address range, DNS settings, and other network-level configurations.
   * **Key Features:**
     * Full control over the network's CIDR block.
     * Ability to create isolated networks for different environments or applications.
4. **Elastic IP**
   * **Elastic IPs** are static IP addresses that can be associated with resources in your VPC. They provide a persistent IP address that can be remapped to different resources within the VPC, ensuring consistent external access to services.
   * **Key Features:**
     * Persistent public IP addresses for cloud instances.
     * Flexible reallocation across resources for load balancing or failover.
5. **Peering Connections**
   * **Peering Connections** allow different VPCs (even in different regions) to communicate with each other privately and securely. This helps in creating a hybrid network or connecting multiple VPCs for high-availability and distributed applications.
   * **Key Features:**
     * Private communication between VPCs.
     * Secure routing for traffic between peered VPCs.

---

Now that we have the general overview and understanding of the VPC and its sub-products, we can go ahead and create the documentation on how to use these products within Utho Cloud. Would you like to start with the specific guides on how to create, manage, and delete each sub-product (subnets, NAT gateways, etc.) or need help structuring the documentation further? Let me know!
