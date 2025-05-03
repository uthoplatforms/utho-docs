---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "FAQs on SSH Keys in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "SSH keys", "secure access", "key management", "cloud authentication"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/account/SSH Keys/FAQs"]
icon: "globe"
tab: true
---


# SSH Keys : Frequently Asked Questions (FAQ)

### 1. What is an SSH key and why do I need it? 🤔
An **SSH key** is a secure authentication method used to access cloud resources like virtual machines without the need for passwords. It ensures encrypted and secure communication between your device and Utho Cloud services. SSH keys are essential for managing cloud resources securely and automating tasks.

### 2. How can I create an SSH key in Utho Cloud? 🛠️
To create an SSH key in Utho Cloud:
1. Log in to your account on the Utho Cloud Platform.
2. Navigate to the [**SSH Key Listing Page**](https://console.utho.com/ssh).
3. Click the **"Create SSH Key"** button.
4. Enter a unique name for your key and paste your public SSH key content.
5. Click **"Create SSH Key"** to finalize the process.

### 3. Where can I view my SSH keys? 👀
You can view all your SSH keys by:
1. [**Login**](https://console.utho.com/login) into the Utho Cloud Platform.
2. Go to the [**SSH Key Listing Page**](https://console.utho.com/ssh).
3. Here, you will find all your SSH keys listed, along with details like key name, content, and creation date.

### 4. How can I delete an SSH key in Utho Cloud? 🗑️
To delete an SSH key:
1. Go to the [**SSH Key Listing Page**](https://console.utho.com/ssh).
2. Find the SSH key you want to delete.
3. Click the **Delete** button next to it.
4. Confirm the deletion when prompted.

### 5. How do I generate SSH key content? 🔑
To generate SSH key content:
1. Open your terminal or PowerShell depending on your OS.
2. Run the following command: `ssh-keygen -t ed25519 -C "your_email@example.com"`.
3. After generating the key, view the public key content by running `cat ~/.ssh/id_ed25519.pub` (for Linux/macOS) or `Get-Content "$env:USERPROFILE\.ssh\id_ed25519.pub"` (for Windows).

### 6. Can I have multiple SSH keys in Utho Cloud? 🔐
Yes, you can create and manage multiple SSH keys within Utho Cloud. Each key can be used for different cloud resources or projects to maintain better security and organization.

### 7. How can I identify my SSH keys? 🏷️
Each SSH key in Utho Cloud is identified by its **name** and **creation date**. You can also view the SSH key content by copying it from the listing page. It's helpful to give each key a unique and descriptive name to easily identify them later.

### 8. How do I use my SSH key to access a cloud resource? 🌐
To use your SSH key to access a cloud resource, simply add the **public SSH key** to the cloud resource (like a virtual machine). When you attempt to connect, your private key will be used to authenticate the connection.