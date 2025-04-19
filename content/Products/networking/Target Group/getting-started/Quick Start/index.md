---
ioTarget  GroupTarget  Groupweight: 30
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

## **Accessing Target  Group**

1. Open the **Utho Cloud Platform** dashboard.
2. Click on **"Target  Group"** in the sidebar.
3. You will be redirected to the **Target Group** listing page.
4. Click on **[Create Target Group](https://console.utho.com/targetgroups)** to open the deployment page.

After click on the above Target Group button a Create Target Group sidebar page will open

#### Configure Target Group Settings:

Here you can configure your Target Group deployment details :

1. **Target Group Name:** Enter a unique name for your Target Group.
2. **Protocal:** Select protocal option from the dropdown for your Target Group.
3. **Port:** Enter the port for your Target Group.
4. **Health Check Path:** Enter the health check path for your Target Group.
5. **Health Check Protocol:** Select health check protocol option from the dropdown for your Target Group.
6. **Health Check Interval:** Enter the health check interval for your Target Group.
7. **Health Check Timeout:** Enter the health check timeout for your Target Group.
8. **Healthy Threshold:** Enter the healthy threshold for your Target Group.
9. **Unhealthy Threshold:** Enter the unhealthy threshold for your Target Group.

#### Verify Deployment:

Your Target Group should now be active and visible in the list of deployed Target Groups.

Here you can see your deployed Target Group with configuration details your provided during the deployment process and you can manage you Target Group by clicking on mange button, for detailed info check for the manage Target Group section in the Utho docs.

## Target Group manage  Section

At the top of the Manage section, users can view the configuration information of the selected Database. This includes:

* **Configuration:** Defines the settings for the target group, including protocols, health checks, and load-balancing rules.
* **Targets:** Specifies the instances, containers, or IP addresses that receive traffic from the load balancer.
* **Destroy:** Removes the target group and disassociates all linked resources, stopping traffic distribution.

## Configuration

In the Configuration section, users can update the Target Group. This section provides the following functionalities:

* **Target Group Name:** Update a unique name for your Target Group.
* **Protocal:** Select protocal option from the dropdown for your Target Group.
* **Port:** Update the port for your Target Group.
* **Health Check Path:** Update the health check path for your Target Group.
* **Health Check Protocol:** Update health check protocol option from the dropdown for your Target Group.
* **Health Check Interval:** Update the health check interval for your Target Group.
* **Health Check Timeout:** Update the health check timeout for your Target Group.
* **Healthy Threshold:** Update the healthy threshold for your Target Group.
* **Unhealthy Threshold:** Update the nnhealthy threshold for your Target Group.

## Targets

In the Targets section, users can add the remove Target from Target Group. This section provides the following functionalities:

* **Add Target:** Click the **Add Target** button to open a form where you can enter the user details such as server type, protocal, port and backend server or user have a custom IP option where user enter custom IP, protocal and port.
* **Add Server:** Click the **Add Server** button to the target into Target Group.

## Destroy

In the Destroy section, users can terminate the Target Group instance. This action is irreversible and will permanently delete the Target Group and all associated data. To destroy a Target Group

Click the **Destroy Target Group** button.

##### **Confirmation:**

A confirmation dialog will appear. Confirm the action to proceed with destroying the Target Group.

When you provide the confirmation then your Target Group Instance will destroy.
