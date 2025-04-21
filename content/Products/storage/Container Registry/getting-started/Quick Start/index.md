---
weight: 30
title: "Quick Start"
title_meta: "Quick Start"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/storage/Container Registry/getting-started/Quick Start"]
icon: guides
tab: true
---
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

1. - **Select DC Location**: **DC location** in a **container registry** refers to the **data center location** where the container images are stored. It determines the physical or geographical region of the registry, impacting latency and performance. Choosing the right DC location helps optimize speed, reduce costs, and ensure compliance with data residency regulations.
   - **Container Registry Type:** There are 2 types of container registry
   - **Public Container Registry** : This type of registry allows anyone to access, pull, and use container images. Examples include Docker Hub and Google Container Registry (GCR). It's ideal for open-source projects or sharing images with a wide audience.
   - **Private Container Registry** : This registry restricts access to authorized users or services only. It is used for storing sensitive or proprietary images that need to be kept secure. Examples include AWS Elastic Container Registry (ECR) and Azure Container Registry (ACR).
   - **Name Your Registry Container:**

   1. **Organization** : It helps organize and categorize container images, making it easier to manage and identify different projects, environments, or application versions.
   2. **Access Control** : Naming conventions can be used to enforce access policies, like differentiating between public and private images or separating different teams' images within the same registry.
   3. **Clarity** : A well-named container registry clearly indicates its purpose (e.g.,  **production** ,  **staging** ,  **dev** ), helping teams understand where to push or pull images for specific environments.
   4. **Versioning** : The naming structure can include versioning information, ensuring different versions of the same image can be stored and retrieved easily.
2. Click on the **Create Registry** button to deploy the container registry into your account container registry.
3. **Verify Registry Addition:**

   - Once added, the new container registry should appear in the public/private tab based on the container type selected at deployment time.

## Manage Container Registry

At the top of the Manage section, users can view the configuration information of the selected Container Registry. This includes:

* **Container Registry Name:** The unique name assigned to the Container Registry.
* **Make Private/Public:** Click the **Make Private/Public** button to change the visibility of your container registry.
* **View Credentials:** Click the **View Credentials** button to open a modal where you can copy the username and password.
* **View Push Commands:** Click the **View Push Commands** button to open a drawer where user can see and copy the commands.

---

The **"View Credential"** in a **container registry** allows you to access the authentication credentials needed to interact with the registry. These credentials are essential for securely pulling or pushing container images to and from the registry. Here's how it's useful:

1. **Access Management** : It helps you retrieve the credentials (like username/password or access tokens) required to authenticate your identity when accessing private container images.
2. **Secure Interactions** : Ensures that only authorized users or services can upload or download images from the registry, maintaining security.
3. **Automation** : The credentials provided can be used in **CI/CD pipelines** or automated deployment scripts to enable seamless and secure interaction with the container registry.

The **"View Push Commands"** in a **container registry** provides the necessary commands to upload (push) container images to the registry. It is used to guide users or automation tools in pushing their locally built or modified container images to the registry for storage and deployment. Here's how it's useful:

1. **Simplifies Workflow** : It provides the exact **Docker CLI** commands or tools for pushing images, making it easier for developers to upload images without having to remember complex syntax.
2. **Automation** : The push commands can be integrated into  **CI/CD pipelines** , allowing for automated pushing of container images during build and deployment processes ayush.
3. **Security & Authentication** : The commands typically include necessary authentication steps (e.g., login credentials or token usage), ensuring secure interaction with the container registry.

---

## Manage Repository

In the Manage Repository section, users can view the Repositories and remove the repository. This section provides the following functionalities:

**Pull Count** refers to the number of times a specific container image has been pulled (downloaded) from the registry by users or systems. It provides insight into how frequently the image is being accessed or used in deployments, updates, or development environments.

### Benefits:

1. **Usage Insights** : It helps understand the popularity or demand for a particular container image.
2. **Monitoring and Analytics** : It can assist in tracking which images are actively being used and which may be outdated or underutilized.
3. **Performance Optimization** : High pull counts may indicate frequent deployment or testing, which could prompt optimizations in access speed or caching strategies.

* **Delete:** Click the **Delete** button to remove the repository from the container registory.

## Destroy

In the Destroy section, users can terminate the Container Registry instance. This action is irreversible and will permanently delete the Container Registry and all associated data. To destroy a Container Registry

Click the **Destroy Container Registry** button.

##### **Confirmation:**

A confirmation dialog will appear. Copy the name of the Container Registry and paste it into the input box. Confirm the action to proceed with destroying the Container Registry.

When you provide the confirmation then your Container Registry will destroy.
