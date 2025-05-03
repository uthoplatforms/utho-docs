---
:weight: 20
title: "FAQs"
title_meta: "FAQs"
description: "A Container Registry is a storage and distribution system for container images, such as Docker images.It allows developers to store, manage, and share container images used in building and deploying applications."
keywords: ["cloud", "instances",  "ec2", "server", "Container Registry", "container registry"]
tags: ["utho platform","cloud","deploy", "Container Registry"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/storage/Container Registry/FAQs"]
icon: "FAQs"
homecard: true
tab: true
---

# 📦 Container Registry

### 1. What is Container Registry?
Utho Cloud Container Registry is a secure and scalable cloud service for storing, managing, and distributing container images. It supports both public and private repositories and integrates with modern DevOps tools and Kubernetes.

### 2. What are the benefits of using Utho Cloud Container Registry?
Key benefits include:
- High availability and scalability
- Secure image storage with encryption
- Vulnerability scanning
- Access control via RBAC
- Integration with CI/CD and orchestration platforms

### 3. How is Utho Cloud Container Registry priced?
It follows a pay-as-you-go pricing model based on:
- Storage used by images
- Optional features like image scanning
- Data transfer (in/out) usage

### 4. How do I push an image to Utho Container Registry?
You can push a container image using Docker CLI:
```bash
docker tag myimage utho-registry.com/myrepo/myimage:tag
docker login utho-registry.com
docker push utho-registry.com/myrepo/myimage:tag
```

### 5. Can I make my repository public?
Yes. Public repositories are accessible to everyone.  
Anyone can pull images without authentication, making them ideal for open-source projects.

### 6. Can I make my repository private?
Yes. Private repositories are restricted to users you explicitly authorize.  
They are ideal for internal projects or proprietary images.

### 7. How do I set repository visibility?
From the Utho dashboard:
1. Go to the **Container Registry** section.
2. Select a repository.
3. Choose **Public** or **Private** under visibility settings.

### 8. What is the default visibility of a new repository?
By default, all newly created repositories are set to **Private** to ensure secure image access.

### 9. Can I change the visibility of a repository after creation?
Yes, visibility can be changed at any time in the repository settings without affecting existing images.

### 10. How do I delete a repository?
Steps:
1. Go to your **Utho Cloud Dashboard**.
2. Navigate to your registry and select the repository.
3. Click **Delete Repository**.  
   ⚠️ This permanently deletes all images in that repository.

### 11. What happens to images when I delete a repository?
All images and tags within the repository are deleted and cannot be recovered.  
Make sure to back up any important images before deletion.

### 12. How do I view my repository credentials?
Navigate to:  
**Utho Cloud Dashboard → Container Registry → View Credentials**

You can:
- View current login tokens  
- Generate new tokens  
- Revoke or rotate credentials  

--- 
