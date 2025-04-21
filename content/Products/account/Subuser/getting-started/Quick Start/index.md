---
weight: 30
title: "Quick Start"
title_meta: "Quick Start"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/account/Subuser/getting-started/Quick Start"]
icon: guides
tab: true
---
## **Login or Sign Up**

1. Go to the **Utho Cloud Platform** [login](https://console.utho.com/login) page.
2. Enter your credentials and click  **Login** .
3. If you don't have an account, sign up [here](https://console.utho.com/signup).

---

## **Accessing Subuser**

1. Open the **Utho Cloud Platform** dashboard.
2. Click on **"Subuser"** in the sidebar.
3. You will be redirected to the **Subuser** listing page.
4. Click on **[Create Subuser](https://console.utho.com/accountManagement ".")** to open the deployment page.
5. **Fill in Subuser Details:**

   - Enter the following details for the subuser:

     - **Name:** Provide the full name of the subuser.
     - **Mobile Number:** Enter the contact number of the subuser.
     - **Email:** Input the email address of the subuser.
     - **Search:** It is used for searching the products whom user want to provide the permission.
     - **Choose Product for Permissions:** Select the product(s) for which you want to grant permissions to the subuser.
6. **Add Subuser:**

   - Click on the **Add Subuser** button to save and add the subuser to your account.

## **Managing Subuser Permissions**

When creating a **subuser**, you can assign specific **permissions** based on the level of access required. There are **three permission levels**:

### **Permission Levels**

1. **Read**

   - The user can **view** products and their flows.
   - No permission to **edit** or **delete** anything.
2. **Write**

   - The user can **read** and **write** (modify or deploy servers).
   - No permission to **delete** servers.
3. **Delete**

   - The user has **full access**: **read, write, and delete**

### **Permission Selection Rules**

- Selecting **Delete** ✅ → **Read** and **Write** are automatically checked.
- Selecting **Write** ✅ → **Read** is automatically checked.
- If **Delete** is checked, the user **cannot uncheck** **Read** or **Write**.
- If **Write** is checked, the user **cannot uncheck** **Read**.

This ensures a **hierarchical permission structure**, preventing accidental misconfigurations.

6. **Verify Subuser Addition:**

   - Once added, the new subuser should appear in the list of subusers with their assigned permissions.

---

## Subuser Configuration Info

At the top of the Manage section, users can view the configuration information of the selected Subuser. This includes:

* **Subuser Name:** The unique name assigned to the Subuser.
* **Subuser Email:** The email assigned to the Subuser.
* **Subuser ID:** The user id of Subuser.
* **Status:** The current status of the Subuser (e.g., active, inactive, pending).
* **Delete:** Use the delete button to remove a Subuser.

## Manage Subuser

In the Manage Subuser section, admin have the ability to update product permission for subuser. This section provides the following functionalities:

* **View Permission:** Click the **View Permission** button to open a form where you can update the permissions for the subuser.

## **Managing Subuser Permissions**

When creating a **subuser**, you can assign specific **permissions** based on the level of access required. There are **three permission levels**:

### **Permission Levels**

1. **Read**

   - The user can **view** products and their flows.
   - No permission to **edit** or **delete** anything.
2. **Write**

   - The user can **read** and **write** (modify or deploy servers).
   - No permission to **delete** servers.
3. **Delete**

   - The user has **full access**: **read, write, and delete**.

## **Permission Selection Rules**

- Selecting **Delete** ✅ → **Read** and **Write** are automatically checked.
- Selecting **Write** ✅ → **Read** is automatically checked.
- If **Delete** is checked, the user **cannot uncheck** **Read** or **Write**.
- If **Write** is checked, the user **cannot uncheck** **Read**.

**Note:-** For deleting the subuser click on the delete button and allow the permission the subuser will be delete refer snippet.

This ensures a **hierarchical permission structure**, preventing accidental misconfigurations.
