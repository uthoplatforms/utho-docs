---
:weight: 20
title: "FAQs"
title_meta: "FAQs"
description: "A subuser in the cloud refers to an additional user account created under a primary account with specific permissions and roles. Subusers allow organizations to delegate access and control over resources without sharing the primary account credentials."
keywords: ["cloud", "instances",  "ec2", "server", "deploy", "Subuser"]
tags: ["utho platform","cloud","deploy", "Subuser"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/account/Subuser/FAQs"]
icon: "FAQs"
homecard: true
tab: true
---
# Subuser

### 1. **What is a subuser in the cloud?**
A subuser is an additional user account created under a primary cloud account with its own set of permissions. Subusers can access specific cloud resources without needing access to the primary account. This enables better control over who can interact with cloud services and data.

### 2. **Why should I create subusers in the cloud?**
Subusers allow you to delegate responsibilities without giving full access to the primary account. This helps enhance security by restricting permissions and improving access control management. It’s useful for teams, third-party collaborators, or service automation.

### 3. **How do I create a subuser in the cloud?**
You can create a subuser through the cloud platform’s management console or API. During the creation process, you'll assign a username, set permissions, and possibly configure multi-factor authentication (MFA). The subuser will then be able to log in with limited access.

### 4. **What permissions can I assign to a subuser?**
Permissions can vary based on roles and cloud services, allowing specific access to storage, compute instances, or databases. You can define whether the subuser has read-only access or full administrative privileges. Custom roles can also be created to fit specific needs.

### 5. **Can subusers access all cloud resources?**
No, subusers only have access to the resources and services granted by their assigned roles. Cloud platforms provide granular access control, allowing you to assign permissions based on the principle of least privilege. This ensures security by limiting access to only what's needed.

### 6. **Can subusers manage other subusers?**
Depending on the permissions granted, some subusers can manage other subusers, such as adding, modifying, or deleting accounts. Typically, this requires a higher level of administrative privilege, such as assigning or changing permissions for other subusers.

### 7. **Can a subuser have administrative privileges?**
Yes, subusers can be granted administrative privileges, enabling them to manage resources, configure settings, and view logs across the cloud environment. It’s important to limit administrative access to trusted users to avoid accidental or malicious configuration changes.

### 8. **Can I revoke or modify subuser permissions?**
Yes, permissions for subusers can be modified or revoked at any time through the cloud management interface. Revoking permissions immediately restricts the subuser’s access, which is useful for adjusting roles or in case of security concerns. Modifications ensure that access remains aligned with needs.

### 9. **How can I secure subuser accounts?**
Subuser accounts should be secured by enabling multi-factor authentication (MFA) and requiring strong passwords. Limiting permissions to only what is necessary for each subuser and regularly reviewing access helps prevent unauthorized use. Additionally, using secure channels for access (like VPNs) increases security.

### 10. **Can a subuser access billing and account settings?**
Typically, subusers do not have access to billing or account settings unless explicitly granted. Cloud platforms often restrict these settings to the primary account owner or specific billing roles to protect sensitive financial information. This helps limit exposure to only authorized users.

### 11. **Can a subuser access APIs?**
Yes, subusers can be granted access to APIs by assigning specific permissions. API access often involves assigning API keys or tokens to the subuser, which allows them to interact with cloud services programmatically. However, access should be carefully controlled to avoid misuse of resources.

### 12. **How can I track subuser activity?**
You can track subuser activity through detailed logs and audit trails available in most cloud platforms. These logs capture actions such as resource access, changes made, and commands executed by subusers. Regular monitoring helps in identifying unauthorized actions or suspicious behavior.

### 13. **Can subusers work on multiple projects or environments?**
Yes, subusers can be assigned access to multiple projects or environments within the cloud platform. The level of access is controlled by the permissions given for each project, which allows subusers to work across various environments, such as development, staging, or production.

### 14. **Can subusers be assigned different roles in different cloud services?**
Yes, subusers can be assigned different roles for different cloud services, such as storage, compute, or networking. This allows more granular control, so a subuser may have administrative access to storage but read-only access to compute services, tailoring their capabilities.

### 15. **Can I set an expiration date for subuser access?**
Some cloud platforms allow you to set an expiration date for subuser access, which can help manage temporary access. This feature is particularly useful for contractors or collaborators who only need limited-time access. Once expired, the subuser's permissions will automatically be revoked.

### 16. **Can subusers log in simultaneously from different devices?**
Yes, subusers can log in from different devices as long as they have the correct credentials and permissions. Cloud platforms typically allow users to access their accounts from multiple locations, making it easier for remote teams or individuals to work across various devices securely.

### 17. **Can a subuser change their password or settings?**
Subusers can usually change their password, but they may be restricted from modifying other settings, such as roles or permissions. The ability to change settings is typically restricted to higher-level roles, such as administrators, to ensure proper access control and security.

### 18. **How can I delegate access without giving full control to subusers?**
You can delegate access by assigning roles that grant specific permissions to subusers. These roles can limit the subuser's actions to only what’s necessary for their tasks, such as managing specific resources or viewing data, without granting full administrative privileges.

### 19. **What happens if a subuser's account is compromised?**
If a subuser’s account is compromised, you should immediately revoke their access and change any associated credentials. It’s also essential to audit the actions taken by the compromised account to detect any potential security breaches. Enabling MFA and using strong passwords can help prevent such incidents.

--- 
