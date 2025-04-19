---
weight: 30
title: Manage Elastic Block Storage
title_meta: "Manage Elastic Block Storage on the Utho Platform"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ['/products/Elastic Block Storage/manage-loadbalancer']
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
2. Click on **"Elastic Block Storage"** in the sidebar.
3. You will be redirected to the **Elastic Block Storage** listing page.
4. Click on **[Create Storage](https://console.utho.com/ebs/deployhttps://console.utho.com/ebs/deploy)** to open the deployment page.

On the  **Deploy Page** , configure the following:

* **Choose DC Location** : Select the data center for your storage.
* **Select Storage Size** : Choose the required storage size (e.g., GB or TB).
* **IOPS** : Select the Input/Output Operations Per Second (IOPS) based on your needs.
* **Throughput** : Set the throughput required for your storage.
* **Name the Storage** : Provide a name for easy identification.
* **Verify Price** : Confirm the estimated cost of your storage.
* After entering all the details, click  **"Create Storage"** .

## **Manage Elastic Block Storage**

Once deployment is complete, you will be directed to the  **Manage Section** , where you can:

#### **Configuration Page** :

**Create a Filesystem**:
First, create a filesystem on your new storage with the command:
`mkfs.ext4 /dev/disk/by-id/virtio-uthostorage-49050`

**Create a Mountpoint** :
Next, create a mountpoint (a folder) to store your data using:

`mkdir /mnt/uthostorage-49050`

**Mount the Storage:** Once the filesystem is ready, mount the storage to the folder with:

`mount /dev/disk/by-id/virtio-uthostorage-49050 /mnt/uthostorage-49050`

**Auto-Mount on Boot:** To automatically mount the storage every time your server starts, add a line similar to the following to your /etc/fstab file.

`/dev/disk/by-id/virtio-uthostorage-49050 /mnt/uthostorage-49050 ext4 defaults,noatime,nofail 0 2 `

#### **Resize Storage** :

With the **Resize** feature, users can increase or decrease the storage size, IOPS, and throughput values of their Elastic Block Storage. To resize the storage:

1. **Adjust the Settings**:

   - Modify the **Storage Size**, **IOPS**, and **Throughput** values according to your requirements.
2. **Click on Resize Storage**:

   - Once you've made the necessary adjustments, click on the **Resize Storage** button.
3. **Completion**:

   - After clicking **Resize Storage**, the EBS volume will be resized as per the new settings.

#### **Attach/Detach EBS** :

1. **Navigate to the Attach/Detach Section**:

- Go to the **Attach/Detach** section from the EBS management page.

2. **Click on the Attach Button**:

   - Click on the **Attach** button to open the attachment drawer.
3. **Select the Required Server**:

   - From the dropdown menu, select the server you want to attach the EBS volume to.
4. **Click on Attach**:

   - After selecting the server, click on **Attach**.
5. **Confirmation**:

   - The server will now be attached to the EBS volume and will reflect in the list under the Attach/Detach section.

### 7. **Delete (Destroy) EBS**

* **Navigate to the Destroy Section**:
  - Go to the **Destroy** section from the EBS management page.
* **Click on the Destroy Button**:
  - Click on the **Destroy** button to initiate the deletion process.
* **Confirmation Pop-up**:
  - A pop-up will appear asking for confirmation. Click on **OK** to confirm the destruction.
* **EBS Deletion**:
  - The EBS volume will be destroyed.
* **Verify Deletion**:
  - To verify the deletion, navigate back to the homepage of EBS. If the EBS volume is no longer listed, the deletion was successful.
