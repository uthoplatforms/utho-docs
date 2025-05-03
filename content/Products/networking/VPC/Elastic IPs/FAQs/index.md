---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "Guide on how to View Elastic IPs in utho cloud platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/VPC/Elastic IPs/FAQs"]
icon: "globe"
tab: true
---

# **Elastic IPs : Frequently Asked Questions (FAQ)**

## **1. What is an Elastic IP in Utho Cloud?** 🌐  
An **Elastic IP** is a static public IP address that can be associated with cloud resources, such as **NAT Gateways** or **Instances**, in Utho Cloud. It allows for consistent IP addressing, even when resources are stopped or restarted.

## **2. How do I view the Elastic IPs in my account?** 👀  
You can view all your allocated Elastic IPs by navigating to the **Elastic IPs Listing Page** in the Utho Cloud console under the **VPC** section.

## **3. How do I allocate a new Elastic IP?** ➕  
To allocate a new Elastic IP, go to the **Elastic IP Listing Page**, click on the **"Add Elastic IP"** button, select a data center location, and then click on **"Add Elastic IP"** to allocate the IP.

## **4. Can I change the location of an existing Elastic IP?** 🔄  
No, the location of an Elastic IP is fixed once allocated. You will need to deallocate the current Elastic IP and allocate a new one in the desired data center.

## **5. How do I assign an Elastic IP to my resources?** 🖧  
After allocating an Elastic IP, you can assign it to resources such as **NAT Gateways** or **Instances** during their creation or update process by selecting the Elastic IP from the dropdown list.

## **6. Can I delete (deallocate) an Elastic IP that is in use?** ❌  
No, an Elastic IP must not be associated with any resource when you attempt to delete (deallocate) it. Ensure the IP is not in use before proceeding with deletion.

## **7. How do I delete (deallocate) an Elastic IP?** 🗑️  
To delete an Elastic IP, go to the **Elastic IP Listing Page**, click the **"Deallocate"** button next to the IP, and confirm the deletion.

## **8. How do I verify if an Elastic IP has been successfully allocated?** ✅  
After allocation, you can verify the new Elastic IP by returning to the **Elastic IP Listing Page**. The new IP should appear in the list along with its associated details.

## **9. How long does it take to allocate an Elastic IP?** ⏳  
Allocating an Elastic IP usually takes just a few moments. Once allocated, it will immediately appear in the **Elastic IP Listing Page**.

## **10. What should I do if I face issues with my Elastic IP?** 🆘  
If you experience any issues with your Elastic IP, you can reach out to the **Utho Cloud support team** through the console or via email for assistance.

---

Feel free to contact support if you have any other questions or need further assistance! 😊
