---
weight: 30
title: "Manage subuser"
title_meta: "Manage subuser"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/account/Subuser/How Tos/Manage subuser"]
icon: guides
---
## Manage Subuser

In the Manage Subuser section, admin have the ability to update product permission for subuser. This section provides the following functionalities:

![1743759896129](image/index/1743759896129.png)

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

![1743759962397](image/index/1743759962397.png)

## **Permission Selection Rules**

- Selecting **Delete** ✅ → **Read** and **Write** are automatically checked.
- Selecting **Write** ✅ → **Read** is automatically checked.
- If **Delete** is checked, the user **cannot uncheck** **Read** or **Write**.
- If **Write** is checked, the user **cannot uncheck** **Read**.
