---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "FAQs on Object Storage in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "object storage", "cloud storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/storage/Object Storage/FAQs"]
icon: "globe"
tab: true
---

# **Object Storage - Frequently Asked Questions (FAQ)**

## **1. What is Object Storage? 📦**
**Object Storage** is a cloud-based storage system that stores data as objects, consisting of the data itself, metadata, and a unique identifier. It is commonly used for storing large, unstructured data like images, videos, and backups.

## **2. How can I upload a file to my Object Storage bucket? 📤**
To upload a file to your bucket:
1. Navigate to the [**Object Storage**](https://console.utho.com/objectstorage/buckets) section in your Utho Cloud platform account.
2. Select the desired **bucket** for uploading the file.
3. Click **Manage**, and then go to the **Objects** tab.
4. Click **Upload Files** and choose the file from your system.
5. Click **Upload**, and your file will be added to the selected bucket.

## **3. How do I delete a file from my Object Storage bucket? ❌**
To delete a file from your bucket:
1. Go to the [**Object Storage**](https://console.utho.com/objectstorage/buckets) section and open the desired **bucket**.
2. Navigate to the **Objects** section.
3. Find the file you wish to delete.
4. Click the **Delete** button next to the file, and confirm the action to permanently remove it.

## **4. How can I share a file stored in my Object Storage? 🔗**
To share a file:
1. Open the [**Object Storage**](https://console.utho.com/objectstorage/buckets) section and navigate to the **bucket** where the file is located.
2. In the **Objects** section, find the file you want to share.
3. Click the **Share** icon next to the file.
4. Set an expiration time for the shared link and click **Generate URL**.
5. Copy the URL and share it with others.

## **5. How can I update the access control of my Object Storage bucket? 🔐**
To update the access control for your bucket:
1. Open the [**Object Storage**](https://console.utho.com/objectstorage/buckets) section and select your bucket.
2. Click on **Manage** and then go to the **Access Control** section.
3. Choose the access level (Private, Public, or Upload) for your bucket.
4. Click **Save Changes** to apply the new settings.

## **6. How do I manage permissions for my Object Storage bucket? 🛡️**
To manage permissions:
1. Go to the **Object Storage** section and select your bucket.
2. Open the **Permissions** section.
3. Click on **Update Permission** to modify or add permissions.
4. Select the access key and permission level (Read, Write, or Read/Write).
5. Click **Update Permission** to save the changes.

## **7. How can I view metrics for my Object Storage bucket? 📊**
To view storage metrics:
1. Open the [**Object Storage**](https://console.utho.com/objectstorage/buckets) section and select your bucket.
2. In the **Overview** section, you can see:
   - Total size of the bucket
   - Occupied storage
   - Remaining storage space
   - Number of objects stored in the bucket

## **8. Can I set an expiration for shared links in Object Storage? ⏳**
Yes, you can set an expiration time for shared links when generating them. The expiration options allow you to set a time frame from seconds to years, after which the link will no longer be accessible.

## **9. Can I update permissions for individual objects inside my bucket? ⚙️**
No, individual object-level permission updates are not supported. Permissions are managed at the bucket level, and all objects within inherit those access settings.


## **10. How do I handle issues or errors with Object Storage? ⚠️**
If you're facing issues with your Object Storage:
1. Ensure that your account has sufficient storage quota.
2. Check the access control and permissions to ensure proper configurations.
3. Verify that files are uploaded correctly and not being blocked by any firewall or security settings.
4. If the issue persists, reach out to customer support for further assistance.

## **11. Can I perform bulk uploads or downloads in Object Storage? 📤**
No, bulk uploads and downloads are not supported at the moment. You’ll need to upload and download files one at a time — select a file, complete the action, then repeat for each file individually.

## **12. What are the limits on file sizes and storage? 📏**
File size limits and storage quotas vary based on the plan you choose. You can check your storage limits by navigating to the **Overview** section of the bucket settings.
🔺 Note: You cannot upload any file larger than 100MB.

## **13. How can I increase my storage space in Object Storage? 🔼**
To increase your storage space:
1. Go to the [**Object Storage**](https://console.utho.com/objectstorage/buckets) section and select your bucket.
2. Click on **Manage** and navigate to the **Upgrade** option.
3. Choose a higher storage plan and confirm the upgrade.

## **14. Can I change the bucket's region after creation? 🌍**
Currently, the region for a bucket cannot be changed once it is created. To change the region, you will need to create a new bucket and move the data to it.

## **15. How do I monitor storage costs for my Object Storage? 💸**
To monitor your storage costs:
1. Navigate to the **Billing** section of your account.
2. Review the **Object Storage** usage report, which provides insights into your current storage usage and associated costs.

## **16. How do I ensure the security of my data in Object Storage? 🔐**
To ensure data security:
- Use strong access control settings (Read/Write/Read-Only permissions).
- Regularly review permissions to ensure that only authorized users have access.
