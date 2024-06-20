---
weight: 50
title: FAQ
title_meta: "Frequently Asked Questions about LoadBalancer on the Utho Platform"
description: "Learn how Utho makes LoadBalancer deployment simple and easy, and get answers to frequently asked questions about our LoadBalancer service."
keywords: ["LoadBalancer", "security"]
tags: ["utho platform", "LoadBalancer"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/LoadBalancer/FAQs/']
icon: "faq"
tab: true
---

# FAQs for Utho Cloud Load Balancer

## General Questions

### 1. What is a load balancer?

A load balancer is a device or service that distributes network or application traffic across multiple servers to ensure no single server becomes overwhelmed. This helps improve application availability, reliability, and performance by balancing the load and providing redundancy.

### 2. What features does the Utho Cloud Load Balancer offer?

The Utho Cloud Load Balancer provides several features, including:
- **Traffic Distribution:** Evenly distributes incoming traffic across multiple backend servers.
- **Health Checks:** Regularly monitors the health of backend servers to ensure traffic is only sent to healthy servers.
- **SSL Termination:** Offloads SSL decryption/encryption from backend servers to improve performance.
- **Sticky Sessions:** Ensures that a user’s session is consistently routed to the same backend server.
- **Scalability:** Automatically scales to handle varying levels of traffic.

### 3. Why should I use a load balancer?

Using a load balancer improves your application's performance, reliability, and scalability by:
- **Preventing Overload:** Distributing traffic to avoid overloading any single server.
- **Enhancing Availability:** Providing redundancy and failover capabilities.
- **Improving Response Time:** Ensuring efficient use of resources and faster response times.

## Technical Questions

### 1. How do I set up a load balancer in Utho Cloud?

To set up a load balancer in Utho Cloud:
1. Log in to your Utho Cloud account and navigate to the Load Balancer section.
2. Click on the **Create Load Balancer** button.
3. Enter the required details such as Load Balancer Name, Protocol, and Port.
4. Select the backend servers to be included in the load balancer pool.
5. Configure health check settings and other advanced options as needed.
6. Click **Create** to deploy the load balancer.

### 2. What types of traffic can the Utho Cloud Load Balancer handle?

The Utho Cloud Load Balancer can handle various types of traffic, including HTTP, HTTPS, TCP, and UDP. You can configure it to suit the specific needs of your applications and services.

### 3. How does the health check feature work?

The health check feature periodically sends requests to your backend servers to verify their status. If a server fails a health check, it is temporarily removed from the load balancer pool until it passes subsequent health checks. This ensures traffic is only routed to healthy servers.

## Troubleshooting

### 1. Why is my load balancer not distributing traffic evenly?

Possible reasons for uneven traffic distribution include:
- **Server Health:** Some backend servers may be failing health checks and are not receiving traffic.
- **Sticky Sessions:** If sticky sessions are enabled, user sessions are consistently routed to the same server.
- **Rule Configuration:** Review the load balancing rules and ensure they are configured correctly.

### 2. How do I troubleshoot connectivity issues with my load balancer?

To troubleshoot connectivity issues:
- **Check Server Health:** Ensure all backend servers are healthy and passing health checks.
- **Firewall Rules:** Verify that firewall rules allow traffic to and from the load balancer.
- **Network Configuration:** Ensure network configurations and routing settings are correct.
- **Logs and Monitoring:** Use logs and monitoring tools to identify potential issues.

### 3. What should I do if SSL termination is not working?

If SSL termination is not working:
- **Certificate Configuration:** Ensure the SSL certificate is correctly configured and uploaded to the load balancer.
- **Protocol Settings:** Verify that the load balancer is configured to handle HTTPS traffic.
- **Health Checks:** Ensure backend servers are correctly configured to receive unencrypted traffic if SSL termination is enabled.

## Support

For further assistance or additional questions, please contact Utho Cloud Support through:
- Utho Cloud Management Console
- Email: support@utho.com

<!-- ## Feedback

We value your feedback! Please share your suggestions and comments regarding the Utho Cloud Load Balancer at feedback@uthocloud.com. Your input helps us improve our services and products. -->


