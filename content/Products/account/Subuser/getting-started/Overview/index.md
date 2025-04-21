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
aliases: ["/products/account/Subuser/getting-started/Overview"]
icon: guides
tab: true
---
# OVERVIEW

### What is a Subuser in Cloud Services?

 A **Subuser** refers to an additional user account that is created under the main (primary) account, typically to help manage cloud resources and services. Subusers are assigned specific roles and permissions within the cloud platform, allowing them to perform actions like managing virtual machines, storage, databases, or network configurations based on their assigned access levels.

Cloud providers like AWS, Azure,Google Cloud and **UTHO** offer the ability to create subusers to facilitate the management of cloud resources by teams, departments, or external partners, without granting full access to the primary account or its sensitive information.

### Purpose of Subuser in Cloud

1. **Delegation of Cloud Management** : Subusers allow administrators to delegate specific cloud management tasks, such as managing databases or setting up virtual machines, to other users without giving them access to sensitive information like billing or account settings.
2. **Team Collaboration** : Cloud services often require multiple users to collaborate on infrastructure setup, application development, or deployment. Subusers enable secure teamwork while maintaining clear boundaries between different users’ roles.
3. **Granular Access Control** : Subusers help enforce the principle of **least privilege** by restricting access to only the resources or services they need to manage. This minimizes the risk of unauthorized access or accidental changes to critical cloud infrastructure.
4. **Separation of Duties** : For compliance or operational reasons, certain responsibilities need to be separated. Subusers enable this separation, ensuring that different people or teams manage distinct parts of the cloud environment (e.g., security, development, and operations).
5. **Simplified Account Management** : Subusers streamline the process of managing access to cloud resources by allowing administrators to control permissions and access levels through a central interface, instead of needing to manage individual user permissions across various services.

### Key Features of Subuser in Cloud

1. **Role-Based Access Control (RBAC)** : Cloud platforms often provide role-based access models, where subusers are assigned specific roles with predefined permissions (e.g., Viewer, Developer, Admin). This allows for fine-grained access control.
2. **Customizable Permissions** : Cloud administrators can configure permissions for each subuser, determining what resources or services they can access or modify, ensuring that subusers only have access to what is necessary for their job function.
3. **Multi-Factor Authentication (MFA)** : To enhance security, cloud platforms often require subusers to use multi-factor authentication (MFA) when accessing their accounts, ensuring that only authorized individuals can access sensitive resources.
4. **Access to Specific Services or Projects** : Subusers can be granted access to specific projects, virtual machines, storage accounts, or cloud services, isolating them from the broader infrastructure to ensure that their role is clearly defined.

### Use Cases of Subuser in Cloud

1. **Cloud Infrastructure Management** : In a cloud environment like AWS, subusers can be assigned to manage virtual machines, storage, networking, or databases, without having access to account billing or administrative features.
2. **Collaborative Development and Deployment** : In cloud-based CI/CD (Continuous Integration/Continuous Deployment) pipelines, developers or DevOps teams can be given access to specific development environments or applications, allowing them to deploy, manage, and troubleshoot applications without compromising other resources.
3. **Security Management** : In security-focused cloud environments, subusers can be assigned roles like **Security Auditor** or **Security Admin** to monitor and secure cloud resources, without giving them full access to other infrastructure or sensitive data.
4. **Cost and Billing Control** : Cloud administrators can create subusers with specific access to billing and cost management tools. This allows finance or operations teams to monitor cloud usage and spending, without modifying infrastructure settings or configurations.
5. **Customer Support and Collaboration with External Partners** : External consultants or contractors can be given temporary subuser access to specific cloud resources, enabling them to work on a project or troubleshoot an issue, without having access to the entire cloud environment.
6. **Compliance and Auditing** : Cloud services that require compliance with regulations (such as GDPR, HIPAA) often use subusers to restrict access to certain resources and maintain clear accountability for actions performed within the cloud environment.
