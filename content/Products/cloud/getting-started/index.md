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
---
## Initial Start

* **Log in** to your account on our platform.
* **Navigate** to the top toolbar and locate the **Deploy** dropdown menu.
* **Select** the **Clouf** option from the dropdown.

## Quick Start

#### Open the Cloud Deployment Page:

Click on the **Deploy** dropdown in the top toolbar and select  **Cloud Instances** .

![1718808467739](image/index/1718808467739.png)After click on the above Cloud Instances button a cloud-deploy page will open

#### Configure Cloud Settings:

here you can configure your cloud deployment details .

1. ##### Choose Datacenter Location:

   ![1718808483679](image/index/1718808483679.png)Select a datacenter location from the available options to optimize performance and comply with regional data regulations.
2. ##### Select Image:

   ![1718808506952](image/index/1718808506952.png)Choose a image from the following image types to define the operating environment for your server:*


   * **Operating System** : ![1718808522570](image/index/1718808522570.png)Select an operating system such as Ubuntu, CentOS, Fedora, Rocky Linux, AlmaLinux, Debian, or Windows.
   * **Marketplace** : ![1718808539908](image/index/1718808539908.png)Choose from over 20+ marketplace applications to deploy ready-to-use software solutions.
   * **Stack** : Select from two types of stacks:

     **![1718808553838](image/index/1718808553838.png)Own Stack** : Private stacks that are accessible only by the user who created them.

     **Community Stack** : Public stacks that are accessible by any user.
   * **ISO** : ![1718808773203](image/index/1718808773203.png)Choose an ISO image to deploy a custom operating system or software environment.
   * **Snapshot** : ![1718808784765](image/index/1718808784765.png)Select a snapshot image to restore a previous state of a server.
   * **Backup** : ![1718808797330](image/index/1718808797330.png)Choose a backup image to recover data from a previous backup.
3. **Configure Billing Cycle:**

   ![1718808808882](image/index/1718808808882.png)Select the billing cycle that best fits your budget and usage patterns (e.g., hourly, monthly).
4. ****Select Plan** :**

   ![1718808818656](image/index/1718808818656.png)Choose a plan that matches your performance requirements, including CPU, RAM, and storage options.
5. **Setup Auth Configuration:**

   Configure authentication for accessing your server:![1718808832692](image/index/1718808832692.png)

   * **Password Authentication** : Provide a secure password for the server.
   * **SSH Key Authentication** : Upload an SSH key for more secure and convenient access.
6. ****Select CPU Model**:**

   ![1718808855087](image/index/1718808855087.png)Choose the type of CPU (AMD or Intel) that meets your performance and compatibility needs.
7. ****Additional Configurations**:**

   * **VPC Network** : ![1718808865118](image/index/1718808865118.png)Select a Virtual Private Cloud (VPC) network available at your chosen location to isolate and secure your cloud environment.
   * **Firewall** : ![1718808875158](image/index/1718808875158.png)Enable or disable firewall protection to control incoming and outgoing traffic.
   * **Backup** : ![1718808883547](image/index/1718808883547.png)Enable or disable the backup option (note that enabling backups will incur an additional cost of 20% of the cloud cost).
   * **Label** : ![1718808894118](image/index/1718808894118.png)Provide a descriptive name for your cloud server to easily identify it in your dashboard.
   * **Number of Servers** : ![1718808912339](image/index/1718808912339.png)Specify the number of cloud servers to deploy simultaneously with the same configuration.
8. **Cost Summary:**

   ![1718808924088](image/index/1718808924088.png)Review the cost summary on the right side of the interface to see a detailed breakdown of the costs associated with your selected configuration.
9. **Apply Coupon:**

   ![1718808931230](image/index/1718808931230.png)If you have a coupon code, apply it to receive a discount on your deployment.
10. ****Deploy Cloud**:**

    ![1718808942435](image/index/1718808942435.png)Click the 'Deploy Cloud' button to initiate the deployment process. The system will start provisioning your cloud server(s) based on the specified configuration.

#### Verify Deployment:

Your Cloud should now be active and visible in the list of deployed Clouds.

![1718809288460](image/index/1718809288460.png)here you can see your deployed cloud with configuration details your provided during the deployment process and you can manage your cloud by clicking on mange button, for detailed info check for the manage cloud section in the Utho docs.
