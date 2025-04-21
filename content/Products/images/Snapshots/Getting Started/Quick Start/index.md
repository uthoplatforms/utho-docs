---
weight: 40
title: "Quick Start"
title_meta: "Quick Start"
description: "Quick start guide on snapshots in utho cloud platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/images/Snapshots/Getting Started/Quick Start"]
icon: "globe"
tab: true
---



# **Quick Start Guide for Managing Snapshots in Utho Cloud**

Snapshots in Utho Cloud allow you to save and restore the state of your cloud servers at a particular point in time. This feature is useful for backup purposes or when you need to roll back to a previous server state. Below is a quick guide on how to view, restore, and delete snapshots within the Utho Cloud platform.

---

### **1. How to View Snapshots**

* **[Login](https://console.utho.com/login)** to your Utho Cloud Platform account.
* Navigate to the **Snapshot Listing Page** or click [here](https://console.utho.com/snapshot) to view all snapshots.
* On this page, you can view important snapshot details, including:
  * **ID** : Unique identifier for the snapshot.
  * **Name** : The name you assigned to the snapshot.
  * **Cloud ID** : The ID of the cloud server that the snapshot belongs to.
  * **Size** : The size of the snapshot.
  * **Snapshot Location** : The data center location where the snapshot is stored.
  * **Created At** : The date and time the snapshot was created.
  * **Restore** : An action button to restore the snapshot to your server.
  * **Action** : A **Delete** icon to remove the snapshot.

---

### **2. How to Restore a Snapshot**

* On the  **Snapshot Listing Page** , find the snapshot you want to restore.
* Click on the **Restore** button next to the snapshot.
* A confirmation popup will appear asking if you're sure you want to restore the snapshot.
* Click **OK** to confirm. The restoration process will begin, and the cloud server will be restored to the state it was in when the snapshot was taken.
  **Important:** Restoring a snapshot will overwrite the current state of the server with the snapshot's state. Any data or changes made after the snapshot was created will be lost.

---

### **3. How to Delete a Snapshot**

* Navigate to the  **Snapshot Listing Page** .
* Find the snapshot you want to delete.
* At the end of the snapshot row, click the **Delete** button.
* A confirmation popup will appear, asking you to confirm the deletion.
* Click **OK** to permanently delete the snapshot. Once deleted, the snapshot cannot be restored.

---

### **4. Conclusion**

Snapshots are a powerful tool for managing the state of your cloud servers in Utho Cloud. You can easily view, restore, or delete snapshots depending on your needs. Regularly using snapshots ensures you have restore points to return to in case of issues or changes in your cloud environment.

By following this guide, you can manage your snapshots efficiently to maintain a secure and organized cloud environment.

---

Let me know if you need further details or adjustments!
