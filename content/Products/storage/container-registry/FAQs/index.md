---
weight: 50
title: FAQ
title_meta: "Frequently Asked Questions about Container Registry on the Utho Platform"
description: "Learn how Utho makes SQS deployment simple and easy, and get answers to frequently asked questions about our Container Registry service."
keywords: ["container registry", "security"]
tags: ["utho platform", "container registry"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/storage/container-registry/FAQs/']
icon: "faq"
tab: true
---

# FAQs for Utho Cloud Container Registry

## General Questions

### 1. What is Utho Cloud Container Registry?
Utho Cloud Container Registry is a secure and scalable service designed to store, manage, and deploy container images. It supports various container formats and integrates seamlessly with CI/CD pipelines and orchestration tools like Kubernetes.

### 2. What are the benefits of using Utho Cloud Container Registry?
Benefits include:
- **Scalability:** Automatically scales to meet your storage needs.
- **Security:** Provides image scanning, signing, and encryption.
- **Reliability:** Ensures high availability and durability of your images.
- **Integration:** Works seamlessly with CI/CD tools and Kubernetes.
- **Access Control:** Offers fine-grained access control and role-based access management (RBAC).

### 3. How is Utho Cloud Container Registry priced?
Utho Cloud Container Registry uses a pay-as-you-go pricing model. You pay for the storage used by your container images and any additional features like image scanning. Refer to the pricing page on the Utho Cloud website for detailed information.

## Technical Questions

### 1. How do I push an image to Utho Cloud Container Registry?
To push an image:
1. **Tag the Image:**
    ```sh
    docker tag <local-image> <registry-url>/<repository>:<tag>
    ```
2. **Log in to the Registry:**
    ```sh
    docker login <registry-url>
    ```
3. **Push the Image:**
    ```sh
    docker push <registry-url>/<repository>:<tag>
    ```

### 2. How do I pull an image from Utho Cloud Container Registry?
To pull an image:
1. **Log in to the Registry:**
    ```sh
    docker login <registry-url>
    ```
2. **Pull the Image:**
    ```sh
    docker pull <registry-url>/<repository>:<tag>
    ```

### 3. How do I secure my container images?
Utho Cloud Container Registry provides several security features:
- **Vulnerability Scanning:** Automatically scan images for vulnerabilities.
- **Image Signing:** Sign images to ensure their integrity.
- **Encryption:** All images are encrypted at rest and in transit.
- **Access Control:** Use RBAC to manage who can push, pull, and manage images.

### 4. Can I integrate Utho Cloud Container Registry with my CI/CD pipeline?
Yes, Utho Cloud Container Registry integrates seamlessly with popular CI/CD tools. You can configure your CI/CD pipeline to automatically push images to the registry and pull them during deployment.

### 5. How do I manage access to my container images?
You can manage access using fine-grained access control and role-based access management (RBAC). Set up permissions to define who can push, pull, and manage images in your repositories.

## Troubleshooting

### 1. Why can't I push an image to the registry?
Possible reasons include:
- **Permissions:** Ensure you have the correct permissions to push images.
- **Authentication:** Make sure you are logged in to the registry.
- **Tagging:** Verify that the image is correctly tagged.
- **Network Issues:** Check your network connection.

### 2. Why can't I pull an image from the registry?
Possible reasons include:
- **Permissions:** Ensure you have the correct permissions to pull images.
- **Authentication:** Make sure you are logged in to the registry.
- **Image Tag:** Verify that the image tag is correct.
- **Network Issues:** Check your network connection.

### 3. What should I do if an image fails vulnerability scanning?
If an image fails vulnerability scanning:
- **Review Scan Results:** Check the detailed scan report to identify vulnerabilities.
- **Update Dependencies:** Update your base image and dependencies to address vulnerabilities.
- **Rescan Image:** Rescan the image after making necessary updates.

## Support

For further assistance or additional questions, please contact Utho Cloud Support through:
- **Utho Cloud Management Console**
- **Email:** support@utho.com

<!-- ## Feedback

We value your feedback! Please share your suggestions and comments regarding the Utho Cloud Container Registry at feedback@uthocloud.com. Your input helps us improve our services and products. -->
