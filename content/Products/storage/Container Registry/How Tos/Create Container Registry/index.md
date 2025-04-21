---
weight: 30
title: "Create Container Registry"
title_meta: "Create Container Registry"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/storage/Container Registry/How Tos/Create Container Registry"]
icon: guides
tab: true
---
# **How to create Container Registry**

## **Login or Sign Up**

1. Go to the **Utho Cloud Platform** [login](https://console.utho.com/login) page.
2. Enter your credentials and click  **Login** .
3. If you don't have an account, sign up [here](https://console.utho.com/signup).

---

## **Accessing Elastic Block Storage**

1. Open the **Utho Cloud Platform** dashboard.
2. Click on **"Container Registry"** in the sidebar.
3. You will be redirected to the **Container Registry** listing page.
4. Click on **[Create Repository](https://console.utho.com/container-registry ".")** to open the deployment page.

On the  **Deploy Page** , configure the following:

1. - After clicking on **Create Container Registry**  deploy page of CR will open.
   - **Select DC Location**:![1743763669859](image/index/1743763669859.png)**DC location** in a **container registry** refers to the **data center location** where the container images are stored. It determines the physical or geographical region of the registry, impacting latency and performance. Choosing the right DC location helps optimize speed, reduce costs, and ensure compliance with data residency regulations.
   - **Container Registry Type:** There are 2 types of container registry![1743763700408](image/index/1743763700408.png)
   - **Public Container Registry** : This type of registry allows anyone to access, pull, and use container images. Examples include Docker Hub and Google Container Registry (GCR). It's ideal for open-source projects or sharing images with a wide audience.
   - **Private Container Registry** : This registry restricts access to authorized users or services only. It is used for storing sensitive or proprietary images that need to be kept secure. Examples include AWS Elastic Container Registry (ECR) and Azure Container Registry (ACR).
   - **Name Your Registry Container:![1743763732522](image/index/1743763732522.png)**

   1. **Organization** : It helps organize and categorize container images, making it easier to manage and identify different projects, environments, or application versions.
   2. **Access Control** : Naming conventions can be used to enforce access policies, like differentiating between public and private images or separating different teams' images within the same registry.
   3. **Clarity** : A well-named container registry clearly indicates its purpose (e.g.,  **production** ,  **staging** ,  **dev** ), helping teams understand where to push or pull images for specific environments.
   4. **Versioning** : The naming structure can include versioning information, ensuring different versions of the same image can be stored and retrieved easily.
2. Click on the **Create Registry** button to deploy the container registry into your account container registry.

   ![1743763753436](image/index/1743763753436.png)
3. 
4. **Verify Registry Addition:**

   - Once added, the new container registry should appear in the manage section or in the list of selected (public/private) tab based on the container type selected at deployment time.

![1743763831447](image/index/1743763831447.png)
