---
weight: 40
title: "troubleshooting"
title_meta: "troubleshooting"
description: "Learn how to troubleshoot common SQS issues on the Utho platform, ensuring seamless SQS deployment and management."
keywords: ["sqs", "troubleshooting", "security"]
tags: ["utho platform","sqs"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/storage/sqs/troubleshooting"]
icon: "api"
tab: true
---
This section provides solutions to common issues users may encounter while using our SQS service. If you encounter problems, follow these steps to resolve them or contact our support team for assistance.

# Troubleshooting for Simple Queue Service (SQS) in Utho Cloud

## Common Issues

### 1. Message Delivery Failures
**Problem:** Messages are not being delivered to the queue or are being lost.

**Solution:**
- **Check Permissions:** Ensure that the role or user sending messages to the queue has the necessary permissions.
- **Queue Policy:** Verify that the queue policy allows access from the expected sources.
- **Dead-Letter Queue:** Configure a Dead-Letter Queue (DLQ) to capture undeliverable messages for further analysis.
- **Message Size:** Ensure that the message size does not exceed the limit set by Utho Cloud.

### 2. Permissions Issues
**Problem:** Access denied errors when interacting with SQS.

**Solution:**
- **Role Permissions:** Ensure that the roles assigned to users have the required permissions.
- **Queue Policy:** Check the queue policy for any restrictions that might be blocking access.
- **Utho Cloud Account:** Confirm that your Utho Cloud account has not reached any limits that would prevent access to SQS resources.

### 3. Queue Performance Bottlenecks
**Problem:** Slow message processing or delays in message delivery.

**Solution:**
- **Throughput Limits:** Check if the queue is hitting throughput limits (e.g., request rate limits).
- **Batch Processing:** Use batch operations to improve efficiency.
- **Visibility Timeout:** Adjust the visibility timeout to ensure messages are not processed multiple times.
- **Scaling:** Consider using multiple queues or sharding messages across multiple queues to distribute the load.

### 4. Messages Not Being Deleted
**Problem:** Messages remain in the queue after being processed.

**Solution:**
- **DeleteMessage API:** Ensure that your application calls the `DeleteMessage` API after processing messages.
- **Receipt Handle:** Verify that the correct receipt handle is being used when calling `DeleteMessage`.
- **Visibility Timeout:** If the visibility timeout is too short, messages might become visible again before being deleted. Increase the visibility timeout as needed.

## Error Messages

### 1. AccessDenied
**Error:** `AccessDenied`

**Solution:**
- **Check Role Permissions:** Ensure the role or user has the appropriate permissions.
- **Queue Policy:** Verify the queue policy allows the necessary actions from your role or user.

### 2. MessageTooLong
**Error:** `MessageTooLong`

**Solution:**
- **Reduce Message Size:** Ensure the message size does not exceed the limit set by Utho Cloud. Consider breaking up large messages into smaller ones or using Utho Cloud storage for large payloads and sending storage object references in the SQS message.

### 3. QueueDoesNotExist
**Error:** `QueueDoesNotExist`

**Solution:**
- **Queue URL:** Verify that the correct queue URL is being used.
- **Region:** Ensure that the correct Utho Cloud region is specified if accessing the queue programmatically.

### 4. OverLimit
**Error:** `OverLimit`

**Solution:**
- **Throttle Requests:** Reduce the rate of API requests to avoid hitting rate limits.
- **Batch Operations:** Use batch operations to minimize the number of API requests.

## Support

If you continue to experience issues or need further assistance, please refer to the following resources:

<!-- ### Utho Cloud Documentation
- **SQS Documentation:** [Utho Cloud SQS Documentation](#) *(Replace with actual link)* -->

### Utho Cloud Support
- **Contact Utho Cloud Support:** Access support through the Utho Cloud Management Console.

### Community Resources
- **Utho Cloud Forums:** Participate in discussions and seek advice from the Utho Cloud community in the Utho Cloud Forums.

For immediate assistance, you can also contact Utho Cloud Support via your Utho Cloud Management Console. 

By following these troubleshooting steps and solutions, you should be able to resolve common issues encountered with Utho Cloud Simple Queue Service (SQS).

* **Email:** [support@utho.com](support@utho.com)
* **Phone:**  +91 (120) 484-0000, 1800-103-3422 (Toll Free India)
* **Live Chat:** Available on our website

Our goal is to ensure that your SQS experience is smooth and hassle-free. Don't hesitate to reach out for help if needed.

