---
weight: 40
title: "Quick Start"
title_meta: "Quick Start"
description: "Quick Start guide on Object Storage in Utho Cloud Platform"
keywords: ["utho cloud", "object storage", "cloud storage"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/storage/Object Storage/Getting Started/Quick Start"]
icon: "globe"
tab: true
---


# **Quick Start Guide: Object Storage**

This guide covers the steps to deploy an object storage bucket and manage its functionalities.

---

## **1. How to Create an Object Storage Bucket**

### **Step 1: Log In or Sign Up**

1. Visit the **Utho Cloud Platform** [Login](https://console.utho.com/login) page.
2. Enter your credentials and click  **Login** .
3. If you’re not registered, sign up [here](https://console.utho.com/signup).

### **Step 2: Navigate to Object Storage**

1. Open the **Utho Cloud Platform** dashboard.
2. Click **"Object Storage"** in the sidebar.
3. You’ll be redirected to the  **Object Storage Listing Page** , which includes:
   * **Buckets** : Displays all deployed storage instances.
   * **Access Keys** : Lists generated API access keys.

### **Step 3: Create a Storage Bucket**

1. Click the **"Create Bucket"** button.
2. Configure the bucket:
   * **Datacenter Location** : Choose a region for storage. A closer region ensures better performance and compliance with local data laws.
   * **Storage Plan** : Select a plan based on your storage needs. Different plans offer varying capacities and pricing.
   * **Bucket Name** : Enter a unique, lowercase, space-free name.

### **Step 4: Deploy the Bucket**

1. Review your selected configurations.
2. Click **"Create Storage"** to start deployment.
3. The system will provision your bucket, which may take a few seconds.
4. Once created, you will be redirected to the  **Manage Bucket Page** , where you can verify your deployment.

---

## **2. Managing Object Storage**

Once inside the  **Manage Page** , you can access the following sections:

### **1. Overview**

* Displays key storage details using graphs:
  * **Total Size** : The allocated space.
  * **Occupied Storage** : Space used by files.
  * **Storage Left** : Remaining available space.
  * **Number of Objects** : Total count of stored files/folders.

### **2. Object Management**

#### **Uploading Files**

* Go to the **Object** section and click  **Upload Files** .
* Attach a file and click  **Upload** .
* A toast notification will confirm success.

#### **Downloading Files**

* Locate the file in the **Object** section.
* Click the **Download** icon to download it to your system.

#### **Creating & Managing Folders**

* Click  **New Folder** , enter a name, and confirm.
* To delete a folder, click the **Delete** icon next to it.

#### **Deleting Files**

* Click the **Delete** icon next to the file.
* Confirm deletion when prompted.

### **3. Permissions**

* View and manage access keys with assigned permissions.
* Click **Update Permission** to modify or assign new permissions (Read, Write, Read/Write).

### **4. Access Control**

* Control how users interact with stored objects:
  * **Private** : Only the owner can manage objects.
  * **Public** : Anyone can list objects, but only the owner can modify them.
  * **Upload** : Others can upload, but only the owner can manage files.

### **5. Destroying Object Storage**

* Click **Destroy** to delete the bucket permanently.
* This action  **cannot be undone** , and all stored data will be lost.

---

This guide provides everything needed to deploy and manage object storage effectively. 🚀
