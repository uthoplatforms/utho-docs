---
weight: 30
title: Manage Container Registry
title_meta: "Deploy Container Registry on the Utho Platform"
description: "Learn how Utho makes Container Registry deployment simple and easy so you easily anticipate your cloud infrastructure costs"
keywords: ["container registry", "security"]
tags: ["utho platform","container registry"]
icon: "guides"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/storage/container-registry/manage-cr']
tab: true
---
## Container Registry Configuration Info

At the top of the Manage section, users can view the configuration information of the selected Container Registry. This includes:

![Utho-Manage-container-registry-config](image/Utho-Manage-container-registry-config.png)

* **Container Registry Name:** The unique name assigned to the Container Registry.
* **Make Private/Public:** Click the **Make Private/Public** button to change the visibility of your container registry.
* **View Credentials:** Click the **View Credentials** button to open a modal where you can copy the username and password.
* **View Push Commands:** Click the **View Push Commands** button to open a drawer where user can see and copy the commands.

<!-- * **VPC Network:** The number of VPN users associated with the VPN. -->

## Manage Repository

In the Manage Repository section, users can view the Repositories and remove the repository. This section provides the following functionalities:

![Utho-Manage-container-registry-repo](image/Utho-Manage-container-registry-repo.png)

* **Delete:** Click the **Delete** button to remove the repository from the container registory.

<!-- * **Remove User:** Select a user from the list and click the **Remove** button to remove the user from the VPN.
* **Download User:** Select a user from the list, click the **Download** button, which will download your vpn user into your brows. -->

## Destroy

In the Destroy section, users can terminate the Container Registry instance. This action is irreversible and will permanently delete the Container Registry and all associated data. To destroy a Container Registry

![Utho-Manage-container-registry-destory](image/Utho-Manage-container-registry-destory.png)

Click the **Destroy Container Registry** button.

##### **Confirmation:**

![Utho-Manage-container-registry-destory-popup](image/Utho-Manage-container-registry-destory-popup.png)

A confirmation dialog will appear. Copy the name of the Container Registry and paste it into the input box. Confirm the action to proceed with destroying the Container Registry.

When you provide the confirmation then your Container Registry will destroy.
