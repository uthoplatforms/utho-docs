---
:weight: 20
title: "FAQs"
title_meta: "FAQs"
description: "Cloud Instance FAQs answer common questions about creating, managing, and optimizing virtual machines on Utho."
keywords: ["cloud", "instances",  "ec2", "server", "deploy", "deploy utho server"]
tags: ["utho platform","cloud","deploy", "how to deploy"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/compute/cloud/FAQs"]
icon: "FAQs"
homecard: true
tab: true
---
# Cloud Instance

### 1. **What is a cloud instance?**  
A cloud instance is a virtual server that runs in the cloud, providing computing power, storage, and networking resources. It is highly scalable, allowing you to deploy applications and services without managing physical hardware. Cloud instances are flexible and can be resized or deleted as needed.

### 2. **How do I launch a new instance?**  
To launch a new instance, go to the Utho dashboard, select "Instances," and click on "Create Instance." Choose your desired operating system, instance type, region, and configure the networking options like VPC and security groups. After reviewing, click "Deploy Server" to start the instance.

### 3. **What are instance types?**  
Instance types define the amount of resources (CPU, RAM, storage) allocated to your cloud server. Utho offers various types based on use cases:  
- **General Purpose**: Suitable for a variety of workloads.  
- **Compute-Optimized**: Best for CPU-heavy applications like data processing.  
- **Memory-Optimized**: Ideal for memory-intensive tasks like databases.

### 4. **Why can't I access my instance?**  
If you're unable to access your instance, check the following:  
- Ensure the instance is running and the public IP is correct.  
- Verify that the firewall rules (security groups) allow SSH access on port 22.  
- Confirm that you are using the correct SSH key and the key permissions are set properly.

### 5. **How do I resize an instance?**  
To resize an instance, stop it first, then go to the instance settings in the Utho console. Choose the "Resize" option, select a new instance type (with more or fewer resources), and restart the instance. Always back up your data before resizing to avoid any potential data loss.

### 6. **How do I monitor resource usage?**  
You can monitor the resource usage (CPU, RAM, disk space) using Utho’s built-in monitoring tools in the console. You can also install third-party monitoring agents like `Netdata`, `Datadog`, or `Prometheus` to track performance metrics in real-time. Set up alerts for critical thresholds to keep track of resource consumption.

### 7. **Can I back up my instance?**  
Yes, you can create snapshots of your instance to back up its current state. Snapshots capture the disk data and settings at the time of creation. To create a snapshot, go to the Utho console, select your instance, and choose "Create Snapshot." You can later restore the instance from this snapshot if needed.

### 8. **How do I restore from a snapshot?**  
To restore from a snapshot, go to your Utho dashboard and select the snapshot you want to restore. You can either create a new instance from the snapshot or attach it as a new volume to an existing instance. If restoring to a new instance, ensure the new instance is configured with the same network settings.

### 9. **What are basic security best practices?**  
Follow these security best practices:  
- Use SSH key pairs for authentication, and disable password login.  
- Apply the principle of least privilege by limiting access to only necessary users.  
- Regularly update your system with the latest patches and use firewalls to restrict access.

### 10. **How do I assign a static IP?**  
To assign a static IP, reserve an Elastic IP (EIP) in the Utho console and associate it with your instance. This ensures that the instance has a fixed IP address even if it's restarted. You can manage the IP under the "Networking" section in the instance settings.

### 11. **What’s the difference between stop and terminate?**  
- **Stop**: Halts the instance but retains the disk and other resources, allowing you to restart later.  
- **Destroy**: Deletes the instance and all associated resources (such as disks) permanently. Be sure to back up data before terminating to avoid data loss.

### 12. **Can I enable auto-scaling?**  
Yes, Utho supports auto-scaling by configuring an Auto Scaling Group. Define scaling policies based on metrics like CPU utilization or memory usage. This ensures that your cloud resources can automatically scale up or down to meet demand, improving efficiency and cost management.

### 13. **How to schedule instance uptime?**  
To schedule instance start/stop times, use the Utho scheduler or create scripts with the API to manage the uptime. You can define specific times to automatically start or stop instances based on your operational needs, reducing costs when the instance is not in use.

### 14. **How do I add more storage?**  
You can add additional storage by creating and attaching new volumes from the Utho console. Once attached, log in to your instance and navigate to storage section and see the added storage.

### 15. **How do I secure my instance?**  
Secure your instance by enabling firewalls to control inbound and outbound traffic. Always use SSH keys for authentication, regularly update software, and disable unused ports. Additionally, consider enabling two-factor authentication (2FA) for extra protection.

### 16. **How can I manage costs?**  
To manage costs, regularly monitor your usage and instance activity through the Utho billing dashboard. Identify unused or underutilized instances and shut them down. Implement auto-scaling to adjust resources based on demand, which helps reduce costs during periods of low usage.

### 17. **How do I clone an instance?**  
To clone an instance, create a snapshot of the current instance, which captures its state. Then, use the snapshot to launch a new instance with the same configuration. You can also clone by copying volumes or replicating configuration files across new instances.

---


