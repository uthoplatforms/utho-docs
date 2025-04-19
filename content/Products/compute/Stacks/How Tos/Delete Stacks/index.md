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
## Deleting Stacks

### Steps for Stack Deletion

1. **Navigate to the Stack Listing Page** :

   ![1744102166387](image/index/1744102166387.png)

* The user accesses a page where all the stacks are listed. This could be a table or a grid displaying stack names and related information.

1. **Select the Stack to Delete** :

   ![1744102212978](image/index/1744102212978.png)

* The user identifies the stack they want to delete.
* This could be done by clicking on a **delete icon** (such as a trash bin or a cross symbol) beside the stack they wish to delete.

1. **Show Confirmation Popup** :

   ![1744102243808](image/index/1744102243808.png)

* Once the delete icon is clicked, a confirmation dialog or modal window should appear, asking the user to confirm the deletion.

1. **Allow Permission for Deletion** :

* After the user clicks  **OK** , they are asked to confirm their action or grant any necessary permissions for deletion, if required (e.g., confirming the user is authorized to delete).

1. **Delete the Stack** :

* Once the user confirms (clicks  **OK** ), the stack is removed from the listing. The deletion process should be triggered in the backend (typically via an API call).
* After a successful deletion, the stack should be removed from the front-end view, so the listing gets updated in real-time.
