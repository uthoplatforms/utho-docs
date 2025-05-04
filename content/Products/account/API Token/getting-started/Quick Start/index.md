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
aliases: ["/products/account/API Token/getting-started/Quick Start"]
icon: guides
tab: true
---
## **Login or Sign Up**

1. Go to the **Utho Cloud Platform** [login](https://console.utho.com/login) page.
2. Enter your credentials and click  **Login** .
3. If you don't have an account, sign up [here](https://console.utho.com/signup).

---

## **Accessing Api Token**

1. Open the **Utho Cloud Platform** dashboard.
2. Click on **"Api Token"** in the sidebar.
3. You will be redirected to the **Api Token** listing page.
4. Click on **[Api Token]()** to open the deployment page.
5. Clicking on the **"Create API Key"** button will trigger a sidebar to open, where the user will be prompted to provide the following details:

   * **API Name** : The user must enter a unique and descriptive name for the API token, which will help identify its purpose or usage later.
   * **Access Scope** : The user will select the appropriate access level for the API token. This could typically include options such as:
   * **Read-Only** : The token allows read access to the resources.
   * **Write** (optional): If needed, the user can select **"Write"** to grant the API token permission to modify or create resources, in addition to reading them.
6. After filling in the necessary details (API Name and Access Scope), the user will click the **"Add New API"** button to create the token.

   Upon creation, a unique **API Token** will be generated.
7. The generated API token will be shown in the sidebar.  **Important** : This token will only be displayed  **once** , so the user should immediately copy the token and securely store it. It will not be visible again, ensuring that sensitive information is not exposed after the creation process.
8. **Features of the API Token Listing Page:**
9. **Name** : This column reflects the name assigned to each API token during the creation process, allowing users to easily identify tokens by their designated names.
10. **Access Scope** : The **Access Scope** column displays the permissions granted to the API token. This can be either **Read-Only** or  **Write** , depending on what the user selects during the creation process.
11. **Created At** : The **Created At** column provides a timestamp of when the API token was generated, offering insight into the token's creation date for auditing purposes.
12. **Action** :

* The **Action** column features a **Delete** button. If the user determines that the API token is no longer needed or should be revoked, they can simply click the **Delete** button to remove the token from the system.
* Deleting an API token ensures that the token can no longer be used to authenticate requests to the platform, enhancing security.
