---
:weight: 20
title: "FAQs"
title_meta: "FAQs"
description: "A **Target Group** in **Utho Cloud** is a logical collection of backend resources. It helps manage traffic distribution and health monitoring, ensuring that only healthy, available resources receive incoming requests. Target Groups are essential for achieving scalability, reliability, and efficient load balancing in your cloud infrastructure."
keywords: ["cloud", "instances",  "ec2", "server", "Target Group", "target group"]
tags: ["utho platform","cloud","deploy", "Target Group"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/networking/Target Group/FAQs"]
icon: "FAQs"
homecard: true
tab: true
---

# Target Groups

### 1. **What is a Target Group?**
   - A target group is a set of servers that you want to monitor and balance traffic across. It allows you to group servers that share similar characteristics for efficient load balancing.

### 2. **How do I create a Target Group?**
   - To create a target group, go to the "Create Target Group" section, provide the required information such as the target group name, health check path, health check interval, and healthy/unhealthy thresholds. Then click "Create Target Group."

### 3. **What are the different protocols available for Target Groups?**
   - You can choose between HTTP and HTTPS protocols for your target group. Select the appropriate protocol based on your use case.

### 4. **What is a Health Check Path?**
   - The health check path is the URL path that the load balancer will use to monitor the health of the servers in the target group. It is important to set a path that will respond when the server is healthy, for example, `/health`.

### 5. **What are Health Check Interval and Timeout?**
   - **Health Check Interval**: This is the time period between two consecutive health checks. By default, this is set to 300 seconds.
   - **Health Check Timeout**: This is the time allowed for the health check to complete. If it takes longer than this, the check is considered failed. It is also set to 300 seconds by default.

### 6. **What is the Healthy Threshold?**
   - The healthy threshold defines how many consecutive successful health checks are required before a target is considered healthy.

### 7. **What is the Unhealthy Threshold?**
   - The unhealthy threshold defines how many consecutive failed health checks are required before a target is considered unhealthy.

### 8. **Can I modify the Target Group after creation?**
   - Yes, you can update the target group by modifying its settings such as health check path, thresholds, or protocol under the "Update Target Group" section.

### 9. **How can I manage the targets in a target group?**
   - You can add or remove targets (servers) from the target group by going to the "Targets" tab and clicking the "Add Target" button to add new servers or delete existing ones.

### 10. **How do I add targets to a Target Group?**
   - To add targets, click the "Add Target" button. You can add servers either by selecting an existing backend server or entering a custom IP. You also need to choose the protocol and port for each target.

### 11. **Can I use Cloud Instances as Targets?**
   - Yes, you can add cloud instances as targets in your target group by selecting the "Cloud Instance" option during the target addition process.

### 12. **What is the purpose of adding a Kubernetes Cluster as a Target?**
   - Adding a Kubernetes Cluster as a target allows you to distribute traffic to your Kubernetes services. You can also select a specific node pool in the cluster for traffic routing.

### 13. **How do I destroy a Target Group?**
   - To destroy a target group, go to the "Destroy" tab and click the "Destroy" button. This will permanently delete the target group, but it will not affect the associated servers.

### 14. **What happens when I destroy a Target Group?**
   - Destroying a target group will remove it permanently, and any servers associated with it will be disconnected from receiving distributed traffic. However, the servers themselves will not be destroyed.

### 15. **Can I add multiple targets to a Target Group?**
   - Yes, you can add multiple targets (servers) to your target group, whether it's cloud instances or Kubernetes clusters.

### 16. **What is the difference between Cloud Instance and Kubernetes Cluster in Target Groups?**
   - A **Cloud Instance** refers to a traditional virtual machine or cloud server, while a **Kubernetes Cluster** allows you to target containerized applications running in Kubernetes.

### 17. **How do I view the health status of my Target Group?**
   - You can monitor the health of your target group under the "Targets" tab where you will see details about each target's health, including the number of healthy and unhealthy checks.

### 18. **How do I update the health check configuration for my Target Group?**
   - You can update the health check configuration by going to the "Configuration" tab and adjusting parameters like the health check path, interval, timeout, and thresholds.

### 19. **What should I do if a target becomes unhealthy?**
   - If a target becomes unhealthy, review the server logs and the health check path to ensure the server is responding correctly. Adjust the health check settings if needed.

### 20. **Can I modify the backend protocol for a target after it's added?**
   - Yes, you can modify the backend protocol and port for a target after it has been added by selecting the "Manage" option for the target.

---