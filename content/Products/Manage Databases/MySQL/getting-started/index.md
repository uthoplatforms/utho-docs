---
weight: 20
title: Getting Started
title_meta: "Deploy Cloud on the Utho Platform"
description: "Learn how Utho makes Cloud deployment simple and easy so you easily anticipate your cloud infrastructure costs"
keywords: ["cloud", "instances",  "ec2", "server"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/cloud/getting-started']
icon: "get-started"
tab: true
homecard: true

---
## Initial Start

* **Log in** to your account on our platform.
* **Navigate** to the top toolbar and locate the **Deploy** dropdown menu.
* **Select** the **DataBase** option from the dropdown.

## Quick Start

#### Open the DataBase Page:

Click on the **Deploy** dropdown in the top toolbar and select  **DataBase** .

![Utho-db](image/2.jpg)After click on the above DataBase button a database-deploy page will open.

#### Configure DataBase Settings:

here you can configure your database deployment details .

 1. ##### Choose Create Database Cluster:

   ![Utho-Manage-database-cluster-select-dc](image/1.jpg)Select a Create Database cluster or Deploy cluster then configure page is open.

2. ##### Choose Datacenter Location:

   ![Utho-Manage-database-cluster-select-dc](image/Utho-Manage-database-cluster-select-dc.png)Select a datacenter location from the available options to optimize performance and comply with regional data regulations.
3. ##### Select DataBase Version:

   ![Utho-Manage-database-cluster-database-version](image/Utho-Manage-database-cluster-database-version.png)Choose database cluster and its version such as MariaDb,MySQL,PostgreSQL,Redis,Kafka*

4. **Configure Billing Cycle:**

   ![Utho-Manage-database-cluster-db-billing-cycle](image/Utho-Manage-database-cluster-db-billing-cycle.png)Select the billing cycle that best fits your budget and usage patterns (e.g., hourly, monthly).

5. ****Select Plan** :**

   ![Utho-Manage-database-cluster-db-plan-type](image/Utho-Manage-database-cluster-db-plan-type.png)Choose a plan that matches your performance requirements, including CPU, RAM, and storage options.

5. ****Adjust Number of Replica**:**

   ![Utho-Manage-database-cluster-replica](image/Utho-Manage-database-cluster-replica.png)Adjust the number of replica according to you.

6. ****Additional Configurations**:**

   * **VPC Network** : ![Utho-Manage-database-cluster-db-vpc](image/utho-vpc.png)Select a Virtual Private Cloud (VPC) network available at your chosen location to isolate and secure your cloud environment.And also add subnet with public and private as per your preferences.
   * **Firewall** : ![Utho-Manage-database-cluster-db-security](image/Utho-Manage-database-cluster-db-security.png)Enable or disable firewall protection to control incoming and outgoing traffic.
   <!-- * **Backup** : ![1718808883547](image/index/1718808883547.png)Enable or disable the backup option (note that enabling backups will incur an additional cost of 20% of the cloud cost). -->
   * **Cluster Name** : ![Utho-Manage-database-cluster-db-name](image/new-cluster.png)Provide a descriptive name for your database to easily identify it in your dashboard.
   <!-- * **Number of Servers** : ![1718808912339](image/index/1718808912339.png)Specify the number of cloud servers to deploy simultaneously with the same configuration. -->
<!-- 8. **Cost Summary:**

   ![1718808924088](image/index/1718808924088.png)Review the cost summary on the right side of the interface to see a detailed breakdown of the costs associated with your selected configuration.
9. **Apply Coupon:**

   ![1718808931230](image/index/1718808931230.png)If you have a coupon code, apply it to receive a discount on your deployment. -->
8. ****Deploy DataBase**:**

    ![Utho-Manage-database-cluster-db-button](image/Utho-Manage-database-cluster-db-button.png)Click the 'Create Cluster' button to initiate the deployment process. The system will start provisioning your database based on the specified configuration.

#### Verify Deployment:

Your Database should now be active and visible in the list of deployed Databases.

![Utho-database-cluster-mysql](image/Utho-database-cluster-mysql.png)here you can see your deployed Database with configuration details your provided during the deployment process and you can manage your database by clicking on mange button, for detailed info check for the manage database section in the Utho docs.
