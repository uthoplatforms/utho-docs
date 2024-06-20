---
weight: 50
title: FAQ
title_meta: "Frequently Asked Questions about SQS on the Utho Platform"
description: "Learn how Utho makes SQS deployment simple and easy, and get answers to frequently asked questions about our SQS service."
keywords: ["sqs", "security"]
tags: ["utho platform", "sqs"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/SQS/FAQs/']
icon: "faq"
tab: true
---

# FAQ for Simple Queue Service (SQS) in Utho Cloud

## General Questions

### 1. What is Utho Cloud Simple Queue Service (SQS)?
**Answer:** Utho Cloud Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. It allows you to transmit any volume of data at any level of throughput without losing messages.

### 2. How do I create a new SQS queue?
**Answer:** 
1. Log in to your Utho Cloud Management Console.
2. Navigate to the **SQS** section from the sidebar menu.
3. Click on **Create New SQS**.
4. Provide the necessary details such as Queue Name, Data Center Location, and Plan Type.
5. Click **Create SQS** to deploy your queue.

### 3. What are the benefits of using SQS?
**Answer:**
- **Scalability:** Automatically scales to handle any volume of messages.
- **Reliability:** Ensures messages are not lost and are delivered at least once.
- **Flexibility:** Supports both standard and FIFO (First-In-First-Out) queues.
- **Integration:** Easily integrates with other Utho Cloud services and third-party applications.

### 4. How is SQS billed?
**Answer:** SQS billing is based on the number of requests and the amount of data transferred. There may also be charges for additional features such as long polling or message retention. Please refer to the [Utho Cloud pricing page](#) for detailed pricing information.

### 5. What is the maximum message size allowed in SQS?
**Answer:** The maximum message size allowed in Utho Cloud SQS is 256 KB. If your message payload exceeds this size, consider breaking it into smaller messages or storing the payload in Utho Cloud storage and sending a reference to the object in the SQS message.

## Technical Questions

### 1. How do I send a message to an SQS queue?
**Answer:**
1. Use the Utho Cloud Management Console or API to access your SQS queue.
2. Select the **Send Message** option.
3. Enter your message body and any optional attributes.
4. Click **Send Message** to queue the message.

### 2. How do I receive messages from an SQS queue?
**Answer:**
1. Use the Utho Cloud Management Console or API to access your SQS queue.
2. Select the **Receive Message** option.
3. Specify the maximum number of messages to retrieve and the wait time (for long polling).
4. Click **Receive Message** to get the messages from the queue.

### 3. What is the visibility timeout in SQS?
**Answer:** The visibility timeout is the period during which a message is invisible to other consumers after a consumer retrieves it from the queue. If the message is not deleted or processed within this period, it becomes visible again for other consumers. This ensures that messages are processed at least once.

### 4. How do I configure a Dead-Letter Queue (DLQ)?
**Answer:**
1. Navigate to your SQS queue settings in the Utho Cloud Management Console.
2. Select the option to configure a Dead-Letter Queue.
3. Choose or create a queue to act as the DLQ.
4. Set the redrive policy, including the maximum number of receive attempts before a message is moved to the DLQ.
5. Save your settings.

### 5. How can I secure my SQS queues?
**Answer:**
- **IAM Policies:** Use IAM roles and policies to control access to your SQS queues.
- **Queue Policies:** Configure queue policies to specify who can perform actions on your queues.
- **Encryption:** Enable server-side encryption to protect your messages at rest.
- **VPC Endpoints:** Use VPC endpoints to securely access SQS from within your VPC without traversing the internet.

### 6. What are the differences between Standard and FIFO queues?
**Answer:**
- **Standard Queues:** Offer best-effort ordering and at-least-once delivery. They are suitable for scenarios where the order of messages is not critical.
- **FIFO Queues:** Guarantee the order of messages and exactly-once processing. They are suitable for scenarios where the order of operations and deduplication of messages are important.

## Contact Information

### Customer Support
For further assistance, you can contact Utho Cloud Support through the Utho Cloud Management Console or email support@utho.com.

<!-- ### Feedback
We value your feedback! Please share your suggestions and comments with us at feedback@utho.com to help us improve the Utho Cloud SQS product. -->

