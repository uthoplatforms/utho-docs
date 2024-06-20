---
weight: 40
title: "Troubleshoot"
title_meta: "Understanding How Container Registry Works on the Utho Platform"
description: "Learn how Utho makes Container Registry deployment simple and easy so you easily anticipate your cloud infrastructure security"
keywords: ["container registry", "security"]
tags: ["utho platform","container registry"]
icon: "Troubleshoot"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/Container Registry/Troubleshooting/']
---

<!-- # Troubleshooting Guide for Utho Cloud Container Registry -->

# Common Issues

### 1. Unable to Push Image to the Registry
**Problem:** You cannot push an image to the registry.

**Solution:**
- **Permissions:** Ensure you have the necessary permissions to push images to the repository. Check your user roles and access policies.
- **Authentication:** Verify that you are logged in to the registry. Use the following command to log in:
    ```sh
    docker login <registry-url>
    ```
- **Image Tagging:** Ensure that the image is correctly tagged with the registry URL, repository, and tag. Example:
    ```sh
    docker tag <local-image> <registry-url>/<repository>:<tag>
    ```
- **Network Issues:** Check your network connection and ensure there are no firewall rules blocking access to the registry.

### 2. Unable to Pull Image from the Registry
**Problem:** You cannot pull an image from the registry.

**Solution:**
- **Permissions:** Ensure you have the necessary permissions to pull images from the repository. Check your user roles and access policies.
- **Authentication:** Verify that you are logged in to the registry. Use the following command to log in:
    ```sh
    docker login <registry-url>
    ```
- **Image Tag:** Verify that the image tag is correct and exists in the repository. Use the following command to pull the image:
    ```sh
    docker pull <registry-url>/<repository>:<tag>
    ```
- **Network Issues:** Check your network connection and ensure there are no firewall rules blocking access to the registry.

### 3. Slow Image Upload or Download Speeds
**Problem:** Image upload or download speeds are slow.

**Solution:**
- **Network Connection:** Ensure your internet connection is stable and has sufficient bandwidth.
- **Proximity to Region:** Ensure your registry is located in a region close to your location to minimize latency.
- **Multipart Upload:** Use multipart upload for large images to improve upload speeds.
- **Registry Performance Settings:** Check and optimize performance settings in the Utho Cloud Management Console.

### 4. Image Fails Vulnerability Scanning
**Problem:** An image fails vulnerability scanning.

**Solution:**
- **Review Scan Report:** Check the detailed scan report to identify the vulnerabilities detected.
- **Update Base Image:** Update your base image to the latest version that addresses the vulnerabilities.
- **Update Dependencies:** Update any dependencies that have known vulnerabilities.
- **Rescan Image:** After making updates, rescan the image to ensure vulnerabilities have been addressed.

### 5. "Access Denied" Error
**Error:** `Access Denied`

**Solution:**
- **Permissions:** Ensure your account has the necessary permissions to access the repository. Check your user roles and access policies.
- **Bucket Policies:** Review and adjust bucket policies to grant appropriate access.
- **IAM Roles:** Verify that your IAM roles and policies are correctly configured.

### 6. "Repository Not Found" Error
**Error:** `Repository Not Found`

**Solution:**
- **Repository Name:** Confirm that the repository name is correct and exists in your account.
- **Region:** Ensure you are accessing the repository in the correct region.
- **Deletion:** Check if the repository was accidentally deleted.

### 7. "Request Timeout" Error
**Error:** `Request Timeout`

**Solution:**
- **Network Connection:** Check your internet connection for stability.
- **Retry Mechanism:** Implement a retry mechanism in your upload and download processes to handle transient network issues.
- **Endpoint Configuration:** Ensure the endpoint configuration is correct and reachable.

## Support

For further assistance or additional questions, please refer to the following resources:

<!-- ### Utho Cloud Documentation
- **Container Registry Documentation:** [Utho Cloud Container Registry Documentation](#) *(Replace with actual link)* -->

### Utho Cloud Support
- **Contact Utho Cloud Support:** Access support through the Utho Cloud Management Console or email support@utho.com.

### Community Resources
- **Utho Cloud Forums:** Participate in discussions and seek advice from the Utho Cloud community in the Utho Cloud Forums.

For immediate assistance, you can also contact Utho Cloud Support via your Utho Cloud Management Console.

By following these troubleshooting steps and solutions, you should be able to resolve common issues encountered with Utho Cloud Container Registry and ensure effective performance and reliability of your container images.
