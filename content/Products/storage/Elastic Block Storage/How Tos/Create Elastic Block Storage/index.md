---
weight: 30
title: "Create Elastic Block Storage"
title_meta: "Create Elastic Block Storage"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/storage/Elastic Block Storage/How Tos/Create Elastic Block Storage"]
icon: guides
tab: true
---
# **How to create Elastic Block Storage**

## **Login or Sign Up**

1. Visit the **Utho Cloud Platform** [login ](https://console.utho.com/login)page.
2. Enter your credentials and click  **Login**.
3. If you’re not registered, sign up [here](https://console.utho.com/signup).

---

## **Step 1: Access the Elastic Block Storage**

1. Open the **Utho Cloud Platform** dashboard.
2. Click **"Elastic Block Storage"** in the sidebar.
3. You’ll be redirected to the **Elastic Block Storage** **listing page.**
4. Navigate to the **listing** of Elastic Block Storage and click on **[Create Storage](https://console.utho.com/ebs/deployhttps://console.utho.com/ebs/deploy)**.

### Steps to Create Elastic Block Storage

**Deploy Page of EBS:**

1. - After clicking **Create Storage**, the **Deploy Page** of EBS will open. Here, you need to configure the following options:
   - **Select DC Location**: Choose the data center (DC) location where you want your storage to reside.

     **DC Location** refers to the physical or geographical location of a **Data Center** where cloud resources are hosted. It affects factors like **latency**, **performance**, **data sovereignty**, and **compliance** with legal regulations.

     ![1743596780105](image/index/1743596780105.png)
   - **Storage Size**: Specify the size of the storage you require for your ebs.

     ![1743596815256](image/index/1743596815256.png)
   - **IOPS**: Select the IOPS (Input/Output Operations Per Second) based on your performance requirements.

     ![1743596867677](image/index/1743596867677.png)

     **IOPS (Input/Output Operations Per Second)** measures the number of read/write operations a storage volume can perform per second. In **Elastic Block Storage (EBS)**, IOPS indicates how fast data can be accessed or modified.

     - **Performance**: Higher IOPS means faster data access, important for applications like **databases** or **enterprise workloads**.
     - **Provisioned IOPS**: Cloud services like AWS allow users to provision specific IOPS levels for EBS volumes to meet performance needs (e.g., **io1**, **io2** volumes in AWS).
     - **Use Cases**: Critical for **high-performance databases**, **transaction-heavy apps**, and any workloads with frequent, random data read/writes.
   - **Throughput (MiB/s)**: Choose the desired throughput speed for the storage.

     **Throughput** in **Elastic Block Storage (EBS)** refers to the rate of data transfer, measured in **MB/s (Megabytes per second)**. It determines how quickly data can be read or written to the storage, impacting performance for large data operations like **big data processing** or **backups**.

     - **Higher Throughput** is important for workloads that need fast, continuous data transfer.
     - **Provisioned Throughput** allows users to allocate specific throughput levels for optimal performance.

     ![1743596867677](image/index/1743596867677.png)
   - **Elastic Block Storage Name**: Provide a name for your Elastic Block Storage volume.

     ![1743596923494](image/index/1743596923494.png)
2. **Create the Storage**:

   - Once you have configured all the settings, Verify pricing & click on the **Create Storage** button.

     ![1743596946677](image/index/1743596946677.png)
3. **Verify Deployment**:

   - After clicking **Create Storage**, your Elastic Block Storage will be created and will redirect user to the manage section of ebs where active  status verify the creation of EBS.![1743596971542](image/index/1743596971542.png)

Now, your newly created Elastic Block Storage is ready for use.

#### **Support**

For additional help with **Elastic Block Storage** or if you encounter any issues, contact **Utho Support** through:

- The **Support Ticket System**
- Email: 📩 **[support@utho.com](support@utho.com)**

---
