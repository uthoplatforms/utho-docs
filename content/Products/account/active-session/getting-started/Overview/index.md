---
weight: 30
title: "Overview"
title_meta: "Overview"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/account/active-session/getting-started/Overview"]
icon: guides
tab: true
---
# OVERVIEW

### **Active Session in Utho Cloud**

An **Active Session** in the Utho Cloud refers to an ongoing, authenticated connection between a user and the cloud platform, where the user is engaged in performing operations, managing resources, or interacting with services on the platform. It typically represents the time period during which a user is actively working within the Utho Cloud environment, such as interacting with Elastic Block Storage (EBS), virtual machines (VMs), or other cloud services.

---

### **Purpose of Active Session**

The primary purpose of an Active Session in Utho Cloud includes:

1. **User Authentication & Authorization:**
   * An active session ensures that the user is securely authenticated and authorized to access the services and resources they need on the cloud platform.
   * It acts as a secure bridge between the user and the cloud infrastructure, ensuring that only the right permissions are applied to the user’s actions.
2. **Monitoring and Logging:**
   * Active sessions enable real-time monitoring and logging of user activities for security and auditing purposes. This allows administrators to track what users are doing within the platform.
   * It also helps in tracking performance metrics related to specific users or tasks.
3. **Resource Management:**
   * Active sessions help in resource management, such as provisioning, scaling, and de-provisioning resources (e.g., creating new instances, resizing storage, etc.), by linking the user’s interaction with the resources in real-time.
4. **Session State Maintenance:**
   * During an active session, the platform maintains the state of the user's ongoing tasks. This ensures that the user doesn't lose data or progress if they pause their actions or if a temporary disconnection happens.

---

### **Key Features of Active Sessions in Utho Cloud**

1. **Real-Time Activity Tracking:**
   * Active sessions track real-time user interactions, including changes to virtual machines, storage, or any other resources.
   * Administrators can monitor the session logs to detect any unusual activities or potential security threats.
2. **Session Timeout & Expiry:**
   * Utho Cloud can automatically end an active session after a period of inactivity (configurable time-out), ensuring that unused sessions do not remain open indefinitely.
   * This enhances security by minimizing the risk of unauthorized access if a user forgets to log out.
3. **Role-Based Access Control (RBAC):**
   * Active sessions are typically tied to the role and permissions of the user. Each session enforces the roles assigned to the user (e.g., admin, read-only, or custom roles), ensuring that the user can only perform actions they are authorized to do.
   * Permissions can be fine-tuned to allow or deny certain actions based on the session’s user role.
4. **Session Monitoring and Logs:**
   * Utho Cloud logs every action performed during the active session (e.g., creating an EBS volume, starting a virtual machine, etc.).
   * This allows both administrators and users to review the history of interactions for troubleshooting or auditing purposes.
5. **Multi-Factor Authentication (MFA) Support:**
   * Active sessions can be configured to require additional security, such as Multi-Factor Authentication (MFA), to add an extra layer of protection against unauthorized access.
   * This ensures that sensitive tasks are performed only after verifying the user’s identity with another authentication factor (like a phone or authentication app).
6. **Session Isolation:**
   * Each user session is isolated to ensure that resources, tasks, and configurations cannot be interfered with by other users.
   * This guarantees that one user’s actions don’t affect the workflow or security of another user’s session.
7. **Concurrent Sessions:**
   * Users may have multiple active sessions at the same time, allowing them to manage different resources or tasks in parallel. Utho Cloud can monitor and control these concurrent sessions.

---

### **Use Cases of Active Sessions in Utho Cloud**

1. **Resource Management and Provisioning:**
   * A user may have an active session while they are provisioning new cloud resources, such as creating virtual machines (VMs), provisioning Elastic Block Storage (EBS) volumes, or launching other services like databases or load balancers.
   * Real-time session tracking ensures that each change is logged, and any required configuration or permission changes are properly applied.
2. **Security Auditing and Compliance:**
   * Active sessions allow security teams to track and audit user actions in real-time to ensure that no unauthorized actions are being performed.
   * This is especially useful for compliance purposes where it is critical to maintain detailed records of who did what and when within the cloud environment.
3. **Troubleshooting and Issue Resolution:**
   * If a user experiences an issue while using the cloud platform (e.g., a service failing or a VM not responding), the active session logs can be reviewed to troubleshoot the cause.
   * Monitoring tools can track session metrics to identify performance bottlenecks or errors, allowing the support team to assist users effectively.
4. **Session-Based Billing & Usage Monitoring:**
   * Utho Cloud can use active sessions to track resource usage over the session duration, such as CPU usage, storage consumption, and network bandwidth.
   * Billing systems can then calculate costs based on the active sessions, ensuring users are charged only for the resources they actually use during their session.
5. **Multi-Tenant Cloud Environments:**
   * In a multi-tenant environment, multiple users or organizations may be interacting with shared resources. Active sessions provide a way to isolate and track the usage of each tenant.
   * This isolation ensures that one tenant’s actions do not affect the other tenants in a shared cloud environment.
6. **Scaling and Auto-Scaling Services:**
   * During an active session, users may interact with cloud services that need to be scaled up or down based on real-time demand. For example, a user could be resizing a storage volume or increasing the number of instances running to meet a spike in traffic.
   * Active session tracking helps to ensure that the scaling actions are correctly logged and applied.
7. **User Workflow Automation:**
   * Active sessions can be part of automated workflows in Utho Cloud. For instance, if a user’s session is linked to a deployment pipeline, each action they perform (like pushing new configurations to a VM) is part of an automated deployment process.
   * This improves the efficiency and consistency of resource management and deployment workflows.
8. **Support and Customer Service:**
   * Support agents can access and view an active session to help users resolve problems. This feature allows agents to directly assist users by providing them with information on their current session, checking resource status, and providing guidance.
