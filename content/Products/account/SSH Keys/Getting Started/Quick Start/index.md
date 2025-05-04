---
weight: 40
title: "Quick Start"
title_meta: "Quick Start"
description: "Quick Start Guide on SSH Keys in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "SSH keys", "secure access", "key management", "cloud authentication"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/poducts/account/SSH Keys/Getting Started/Quick Start"]
icon: "globe"
tab: true
---


## **Quick Start Guide for Managing SSH Keys in Utho Cloud**

SSH keys are an essential part of securely accessing cloud resources, providing a safe and password-less method of authentication. In Utho Cloud, SSH keys are used to access virtual machines and other services securely. Follow this guide to generate, view, create, and delete SSH keys.

### **1. How to Generate SSH Key Content**

To create an SSH key, use the following commands based on your operating system:

#### **For Windows (using PowerShell)**

1. Open **PowerShell** and run:

   `ssh-keygen -t ed25519 -C "your_email@example.com`
2. Press **Enter** to accept the default location or specify a new path.
3. Optionally, set a passphrase for added security.

#### **For Mac/Linux**

1. Open **Terminal** and run:

   `ssh-keygen -t ed25519 -C "your_email@example.com"`
2. Press **Enter** to accept the default location or specify a new path.
3. Optionally, set a passphrase for added security.

After generation, the SSH key will be stored in the default directory (`~/.ssh/`). To view the public key, run:

`cat ~/.ssh/id_ed25519.pub`

---

### **2. How to View SSH Keys**

Once your SSH keys are created, you can view them within the Utho Cloud Platform.

* **Login** to your Utho Cloud Platform account.
* Navigate to the **SSH Key Listing Page** to see all SSH keys that have been created.
* In the listing, you will find the following information for each SSH key:
  * **Name** : The name of the SSH key.
  * **SSH Key** : A **Copy** button to copy the SSH key content.
  * **Created At** : The date and time the SSH key was created.
  * **Action** : A **Delete** button to remove a specific SSH key.

---

### **3. How to Create an SSH Key**

To create a new SSH key, follow these steps:

* **Login** to your Utho Cloud Platform account.
* Navigate to the **SSH Key Listing Page** and click the **"Create SSH Key"** button at the top of the page.
* Fill in the following details:
  * **Name** : Provide a unique name for your SSH key.
  * **SSH Key Content** : Paste the SSH public key content you generated on your local machine.
* Once you've entered the required information, click the **"Create SSH Key"** button.
* After successful creation, you will see a success notification, and you can verify the SSH key by checking the list of SSH keys.

---

### **4. How to Delete an SSH Key**

To delete an SSH key, follow these steps:

* Navigate to the  **SSH Key Listing Page** .
* In the list of SSH keys, locate the key you want to delete.
* Click on the **Delete** button next to the key.
* A confirmation popup will appear. Click **OK** to confirm the deletion of the SSH key.
* After confirmation, the SSH key will be deleted, and you can verify this by checking the list of SSH keys again.

---

By following these simple steps, you can generate, view, create, and delete SSH keys in Utho Cloud to securely access your cloud resources.
