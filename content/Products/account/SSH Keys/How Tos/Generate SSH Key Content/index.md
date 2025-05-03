---
weight: 40
title: "Generate SSH Key Content"
title_meta: "Generate SSH Key Content"
description: "Guide on how to generate ssh key content in utho cloud platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "SSH keys", "secure access", "key management", "cloud authentication"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/account/SSH Keys/How Tos/Generate SSH Key"]
icon: "globe"
tab: true
---


## **How to Generate and View Your SSH Key Content**

An SSH key pair consists of two parts: a **private key** (which remains secure on your machine) and a **public key** (which can be shared). You’ll need to generate an SSH key pair to enable secure connections to services like your cloud platform or remote servers.

This guide provides detailed steps for  **Windows** ,  **macOS** , and **Linux** users on how to **generate** an SSH key and then **view** the content of your public key.

---

### **1. Windows Users - Using PowerShell**

#### **Step 1: Open PowerShell**

1. Press `Win + X` and choose **Windows PowerShell** from the menu.
2. Or search for **PowerShell** in the Start menu and open it.

#### **Step 2: Generate SSH Key Pair**

1. In PowerShell, run the following command to generate the SSH key pair:

   * `ssh-keygen -t ed25519 -C "your_email@example.com"`
   * Replace `"your_email@example.com"` with your actual email address (used to identify the key).
   * You can choose a different key type if needed (e.g., `rsa` instead of `ed25519`).
2. The terminal will prompt you to specify where to save the key. By default, it will save to:
   `C:\Users\yourusername\.ssh\id_ed25519`

   Press **Enter** to confirm the default location.
3. You will be asked for a **passphrase** (optional but recommended) to protect the private key. If you prefer no passphrase, just press  **Enter** .

#### **Step 3: View the SSH Key Content**

1. After generating the SSH key, run the following command to view the content of the  **public key** :

   `Get-Content "$env:USERPROFILE\.ssh\id_ed25519.pub" `

   This will display the content of your  **public SSH key** .

---

### **2. macOS Users - Using Terminal**

#### **Step 1: Open Terminal**

1. Open **Terminal** by either:
   * Pressing `Cmd + Space`, typing  **"Terminal"** , and pressing  **Enter** , or
   * Navigating to  **Applications > Utilities > Terminal** .

#### **Step 2: Generate SSH Key Pair**

1. In the Terminal, run the following command to generate your SSH key pair:

   `ssh-keygen -t ed25519 -C "your_email@example.com"`

   Replace `"your_email@example.com"` with your email address.
2. The terminal will ask where to save the generated key. By default, it will save to:

   `/Users/yourusername/.ssh/id_ed25519`

   Press **Enter** to confirm the default location.
4. You will be prompted to enter a **passphrase** (optional but recommended) to secure the private key. If you don't want a passphrase, just press  **Enter** .

#### **Step 3: View the SSH Key Content**

1. Once the key pair is generated, use the following command to view the public key:

   `cat ~/.ssh/id_ed25519.pub`

   This will display your  **public SSH key content** .

---

### **3. Linux Users - Using Terminal**

#### **Step 1: Open Terminal**

1. Open your preferred terminal (such as  **GNOME Terminal** ,  **Konsole** , or  **Xterm** ).
   * You can also press `Ctrl + Alt + T` on most distributions to open the terminal.

#### **Step 2: Generate SSH Key Pair**

1. In the terminal, run the following command to generate the SSH key pair:

   `ssh-keygen -t ed25519 -C "your_email@example.com"`

   * Replace `"your_email@example.com"` with your own email address.
2. The terminal will prompt you to specify where to save the key. By default, it will save to:

   `/home/yourusername/.ssh/id_ed25519 `

   Press **Enter** to confirm the default location.
3. You will be asked to enter a **passphrase** (optional but recommended). If you don’t want to use a passphrase, simply press  **Enter** .

#### **Step 3: View the SSH Key Content**

1. After generating the key, use the following command to view the **public key** content:

   `cat ~/.ssh/id_ed25519.pub`

   This will display your  **public SSH key content** .

---

### **Recap of Key Tools for Each Operating System**

* **Windows Users** : Use **PowerShell** to generate and view your SSH public key.
* **macOS Users** : Use the **Terminal** to generate and view your SSH public key.
* **Linux Users** : Use the **Terminal** to generate and view your SSH public key.

---



### **Example of a Dummy SSH Public Key**

A typical SSH public key looks like this (this is a dummy example for illustration purposes):

`ssh-ed25519 BBBBA4QscD9fYAI1NTE5SSSSIGVuIwB+dVF8/92qfEX/9K5Q47RTVkcp9dm6GHubWIWm john@utho.com `

* **Key Type (`ssh-ed25519`)** : This indicates the type of SSH key being used.
* **Key Content `BBBBA4QscD9fYAI1NTE5...`)** : This is the actual cryptographic key content that is used for the SSH connection.
* **Comment (`john@utho.com`)** : This is an optional comment that is usually your email or username to help identify the key.

### **Conclusion**

By following the steps above, you will generate an SSH key pair and be able to view the public key content. This SSH public key can then be added to various services, such as your cloud platform or remote server, to enable secure SSH access. Always ensure to protect your private key, and feel free to refer to this guide whenever you need to manage your SSH keys.
