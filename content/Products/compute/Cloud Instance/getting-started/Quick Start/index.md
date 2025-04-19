---
Cloud Instances:weight: 20
title: Getting Started
title_meta: "Deploy Cloud on the Utho Platform"
description: "Learn how Utho makes Cloud deployment simple and easy so you easily anticipate your cloud infrastructure costs"
keywords: ["cloud", "instances",  "ec2", "server", "deploy", "deploy utho server"]
tags: ["utho platform","cloud","deploy", "how to deploy"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/cloud/getting-started']
icon: "get-started"
homecard: true
tab: true
---
## **Login or Sign Up**

1. Go to the **Utho Cloud Platform** [login](https://console.utho.com/login) page.
2. Enter your credentials and click  **Login** .
3. If you don't have an account, sign up [here](https://console.utho.com/signup).

---

## **Accessing Elastic Block Storage**

1. Open the **Utho Cloud Platform** dashboard.
2. Click on **"Cloud Instances"** in the sidebar.
3. You will be redirected to the **Cloud Instances** listing page.
4. Click on **[Deploy New ](https://console.utho.com/cloud/deploy ".")** to open the deployment page.

#### Configure Cloud Settings:

Here you can configure your cloud deployment details.

1. **Choose Datacenter Location: DC Location** refers to the **physical data center** or **region** where cloud infrastructure is hosted. It is useful for reducing  **latency** , ensuring **compliance** with local data laws, and providing **high availability** and **disaster recovery** by distributing resources across multiple locations.
2. ##### Select Image:

   Choose a image from the following image types to define the operating environment for your server:*


   * **Operating System** :
   * Refers to a **pre-configured operating system** image (e.g.,  **Ubuntu** ,  **Windows Server** ,  **CentOS** ) that will be installed on the instance. It provides the basic platform for running applications. Select an operating system such as Ubuntu, CentOS, Fedora, Rocky Linux, AlmaLinux, Debian, or Windows.
   * **Marketplace** : Offers **pre-packaged software** or **solutions** (like a  **web server** ,  **database** , or **CRM** software) that are ready to deploy. These images often come with additional configurations and setups specific to certain use cases. Choose from over 20+ marketplace applications to deploy ready-to-use software solutions.

     Once any data is selected from the marketplace then it will ask for the configuration section where it will ask for for further details as shown in the snippet. Mostly it ask to select **OS**.
   * **Stack** : Select from two types of stacks:

     A  **stack**  typically refers to a collection of **software technologies** (e.g., **LAMP stack** for Linux, Apache, MySQL, PHP) that are bundled together. Selecting a stack allows you to deploy a predefined set of technologies on your instance.

     **Own Stack** : Private stacks that are accessible only by the user who created them.

     **Community Stack** : Public stacks that are accessible by any user.
   * **ISO** : **ISO images** are disk image files (like  **bootable ISO files** ) that contain a full operating system or custom software setup. You can upload and use your custom **ISO image** for full control over the OS installation process. Choose an ISO image to deploy a custom operating system or software environment.
   * **Backup** : A **backup image** is similar to a snapshot but often includes  **longer-term backups** . It's used to restore the instance to a previous, stable state in case of failure or data loss. Choose a backup image to recover data from a previous backup.
3. **Select Storage Type:** There are two type of Storage type **General** and **Elastic Block Storage.**

   **General Storage  & EBS Storage Type:**

   * **General Storage** refers to standard storage options used for typical, non-performance-intensive applications.
   * It is designed for **cost-effectiveness** and is used for storing data that doesn't require high-speed access or frequent modifications.
   * General storage is often used for  **archiving** ,  **static files** , and other data that is not accessed or modified regularly.
   * **Examples** include cloud services like **S3 Standard** for object storage or basic storage solutions in cloud environments.
   * It offers a **balance** between cost and performance, suitable for applications where speed is not the primary concern.
   * **EBS (Elastic Block Store)** is a high-performance, **persistent block storage** service in cloud environments like AWS, used for  **applications requiring fast data access** .
   * It is typically attached to  **cloud instances** , providing storage that acts like a hard disk or SSD, allowing data to persist even after instance termination.
   * EBS volumes are used for  **database storage** ,  **file systems** , and workloads that need **low-latency** and  **high-throughput** .
   * There are different types of EBS volumes like  **gp2** ,  **gp3** ,  **io1** , and  **io2** , each optimized for specific performance needs.
   * EBS provides  **durability and scalability** , making it suitable for enterprise applications where data integrity and performance are critical.
4. **Configure Billing Cycle:**

   **Billing Cycle:**

   A **billing cycle** in cloud refers to the **period of time** during which a cloud service provider charges you for the services and resources you've used. The billing cycle determines how often you'll receive invoices and how often you'll be billed for your cloud services. Common billing cycles in the cloud are:

   * **Hourly** : Charges are based on the **number of hours** you use the cloud service.
   * **Monthly** : Charges are billed  **monthly**, **3 months, 6 months** often at the end of the month.
   * **Annually** : Charges are billed  **once a year** , often with **20%** or more  **discounts** for long-term commitments.
5. **Select Plan** :

   The **Basic Plan** is a budget-friendly option designed for small projects or applications that don't require high resources, ideal for development or testing environments.

   The **CPU Optimized Plan** is aimed at workloads that demand higher processing power, such as data processing or compute-intensive applications, offering more CPU cores and faster performance.

   The **Memory Optimized Plan** is suited for applications that require a lot of memory, like databases or analytics workloads, providing increased RAM.

   **Custom Plans** , allowing businesses to configure specific resources like CPU, memory, and storage to meet unique application or workload requirements, providing greater flexibility and control over the cloud environment.

* Choose a plan that matches your performance requirements, including CPU, RAM, and storage options.

##### Now if the storage is General then "Setup Auth Configuration" will occur and if storage is EBS then "Configure storage" section will occur.

6. **Setup Auth Configuration:**

   Configure authentication for accessing your server:

* **Password Authentication** : Provide a secure password for the server.
* **SSH Key Authentication** : Upload an SSH key for more secure and convenient access.

7. **Configure Storage:**

   **Configuring storage in EBS** involves selecting the right **volume type** (e.g.,  **general purpose SSD** ,  **provisioned IOPS SSD** ,  **HDD** ), setting the **size** based on storage needs, and determining **performance** (e.g., IOPS for high-demand workloads). You can also enable **encryption** for data security and use **snapshots** for backups. Configuring EBS storage ensures that data is persistent, secure, and optimized for your cloud instance's needs.

   Also user can add more storage by clicking on the  **"Add New Volume Button".**
8. **Auth Configuration:**

   ### 1. **Password Authentication & SSH Keys**


   * **How it Works** : Users provide a password to access the server.
   * **Benefits** :
   * Simple to set up and use.
   * Widely supported across systems.
   * User-friendly for non-technical users.
   * **Drawbacks** :
   * Vulnerable to brute-force and dictionary attacks.
   * Risk of password theft via phishing or keylogging.
   * Less secure if the password is weak or reused.
   * **Use Case** : Best for simple, low-risk environments or quick ad-hoc access.
   * **SSH Keys**
   * **How it Works** : Users authenticate using a cryptographic key pair (public and private keys).
   * **Benefits** :
   * Stronger security (no passwords transmitted).
   * Resilient to brute-force attacks.
   * Supports automated logins for scripts or CI/CD pipelines.
   * No risk of phishing attacks.
   * **Drawbacks** :
   * Initial setup is more complex.
   * Requires managing and securing private keys.
   * **Use Case** : Ideal for secure, automated access in production environments, cloud systems, and large-scale server management.
9. **Public IP:** A **Public IP** in a cloud server is an IP address that allows external access to the server from the internet. Enabling a **public IP** makes the server reachable from anywhere, which is essential for hosting websites, APIs, or services. **Disabling** it removes internet access, making the server accessible only within the private cloud network, enhancing security. You can enable a public IP during server creation or assign it later, while disabling it typically involves releasing the IP or adjusting firewall settings. Public IPs are often dynamic by default but can be made static for consistency.
10. * **VPC Network** : A **Virtual Private Cloud (VPC)** is a private, isolated network within a cloud environment, allowing you to securely launch and manage resources like virtual machines and databases. It gives you full control over network configurations, IP ranges, and security settings. **Subnets** are divisions within a VPC that help organize resources; **public subnets** provide internet access, while **private subnets** are isolated for sensitive resources. VPCs enhance security, enable flexible network design, and allow you to configure traffic flow using firewalls and route tables. They also help improve scalability and high availability by distributing resources across multiple availability zones.Select a Virtual Private Cloud (VPC) network available at your chosen location to isolate and secure your cloud environment. Also you can create vpc is there is no vpc on the selected dc loaction.
    * **Firewall** : A **firewall** is a security system that monitors and controls incoming and outgoing network traffic based on predefined security rules. In cloud servers, firewalls help protect against unauthorized access by filtering traffic, ensuring only trusted sources can communicate with your server. They can be configured to allow or block specific IP addresses, ports, or protocols. Cloud providers offer **virtual firewalls** (like AWS Security Groups or Azure Network Security Groups) to protect instances and resources. Firewalls enhance security by controlling access to both public and private cloud resources, preventing potential attacks such as unauthorized access or DDoS (Distributed Denial of Service) attacks.Enable or disable firewall protection to control incoming and outgoing traffic.Also you can create firewall if there is no firewall.
    * **CPU Mpdel:**

      **Intel** and **AMD** are the two major CPU manufacturers. Intel is known for its strong **single-core performance** and features like **Hyper-Threading** and  **Turbo Boost** , commonly used in high-performance laptops and workstations. Popular Intel models include **Core i3, i5, i7, i9** and **Xeon** for servers.  **AMD** , on the other hand, offers better **multi-core performance** and value for money, especially in gaming and parallel processing tasks. Popular AMD models include **Ryzen 3, 5, 7, 9** and **EPYC** for servers. AMD CPUs are generally praised for their price-to-performance ratio, while Intel tends to dominate in single-threaded applications.
    * **Label** : Provide a descriptive name for your cloud server to easily identify it in your dashboard.
    * **Number of Servers** :  **Instance Quantity** refers to the number of virtual machine (VM) instances running in a cloud environment. It determines how many separate computing resources are allocated for your applications or services. You can increase the instance quantity to handle more traffic or scale down during low demand. This directly impacts  **cost** , as cloud providers charge based on the number of active instances. Managing instance quantity involves launching, stopping, or terminating instances based on workload needs. It helps with  **scalability** , ensuring resources are available when needed. Overall, it's a key factor in optimizing performance and controlling cloud costs. Specify the number of cloud servers to deploy simultaneously with the same configuration.
11. **Cost Summary:**

    Review the cost summary on the right side of the interface to see a detailed breakdown of the costs associated with your selected configuration.
12. **Apply Coupon:**

    If you have a coupon code, apply it to receive a discount on your deployment. By doing so user will get additional benefits.
13. ****Deploy Cloud**:**

    Click the 'Deploy Cloud' button to initiate the deployment process. The system will start provisioning your cloud server(s) based on the specified configuration.

#### Verify Deployment:

Your Cloud should now be active and visible in the list of deployed Clouds.

Here you can see your deployed cloud with configuration details your provided during the deployment process and you can manage your cloud by clicking on mange button, for detailed info check for the manage cloud section in the Utho docs.

Users can manage the power state and access settings of their cloud instance. The available options include:

## Overview

### 1.  **CPU Usage** :

* Measures the percentage of the CPU being used. High usage (above 80%) indicates potential overload, while low usage suggests underutilization.

### 2.  **Memory Usage** :

* Tracks the amount of RAM being used. High memory usage (over 85%) can lead to performance issues, while low usage may indicate underuse of resources.

### 3.  **Network Traffic** :

* Monitors the volume of data sent and received. Excessive traffic may suggest congestion or need for scaling, while low traffic may indicate idle resources.

### 4.  **Network Packets** :

* Counts the number of data packets being transmitted. A high packet count without high traffic might indicate inefficiencies, such as small data transactions or network issues.

### 5.  **Disk Read/Write** :

* Measures how much data is read from and written to disk. High disk I/O could signal bottlenecks, while low I/O indicates less data activity or idle storage.

These performance metrics provide insights into how the system is running and whether resources are being overutilized or underutilized, which can indicate potential issues such as bottlenecks, resource exhaustion, or inefficient operations.

### 2. **Bandwidth Overview:**

* **Inbound Bandwidth** : The rate at which data is being received from external sources (e.g., incoming data from the internet or other cloud services).
* **Outbound Bandwidth** : The rate at which data is being sent to external sources (e.g., outgoing data to users, clients, or other systems).

Monitoring inbound and outbound bandwidth is crucial for understanding network performance, potential congestion, and ensuring that there is adequate capacity for traffic spikes.

### Console Access :

Provides users with a console interface to directly access the cloud instance as if they were physically present at the server. This can be useful for troubleshooting and performing administrative tasks that require direct access.

### Reset Server Password :

Allows users to reset the root or administrator password for the cloud instance. This is essential if the current password is forgotten or needs to be changed for security reasons.

Resetting a server password enhances **security** by protecting against unauthorized access. It enables **access recovery** if the password is forgotten or compromised. Additionally, it helps meet **compliance** requirements and ensures safe, up-to-date credentials.

### Power Off :

Powers off the cloud instance. This is a graceful shutdown process that allows all running applications and processes to close properly, minimizing the risk of data corruption.

Powering off a server offers several benefits:

1. **Cost Savings** : Reduces cloud costs by stopping resource usage (e.g., compute power, storage).
2. **Security** : Prevents unauthorized access or attacks when the server is not in use.
3. **Maintenance** : Allows for system updates, hardware troubleshooting, or backup tasks without running processes.

### Restart :

Reboots the cloud instance. This involves shutting down and then starting the cloud instance again. It is useful for applying updates or changes that require a restart to take effect.

Restarting a server offers several benefits:

1. **Performance Improvement** : Clears temporary files, memory caches, and resets system processes, improving overall performance.
2. **Troubleshooting** : Helps resolve issues like system errors, application crashes, or slow performance by reloading the server.
3. **Updates and Patches** : Applies pending updates, security patches, or configuration changes that require a reboot to take effect.

### Hard Reboot :

 Forces an immediate reboot of the cloud instance. This is akin to pressing the reset button on a physical machine and should be used with caution as it does not allow running applications and processes to close properly, which may lead to data loss or corruption.

A **hard reboot** (or  **hard reset** ) involves forcibly restarting a server or system, typically by power cycling (turning it off and on). The benefits include:

1. **System Recovery** : It can help recover from severe system freezes or unresponsiveness when a regular reboot isn't possible.
2. **Clearing System Errors** : A hard reboot can clear hardware or software issues that may be preventing the system from functioning properly.
3. **Restoring Hardware Functionality** : It can reset malfunctioning hardware components (e.g., network interfaces or storage) and restore normal operation.

### Enable Rescue Server :

Boots the cloud instance into a rescue mode. This is useful for recovering from system failures or performing maintenance tasks. In rescue mode, users can access the file system and troubleshoot issues that prevent the normal boot process.

Enabling a **rescue server** offers several key benefits:

1. **System Recovery** : It allows you to troubleshoot and fix issues (e.g., corrupted OS or boot problems) without affecting the primary server.
2. **Data Access** : Provides a way to recover or back up critical data from a compromised or unbootable server.
3. **Repair & Maintenance** : Enables performing system repairs, software reinstallation, or reconfiguration without downtime on the main server.
4. **Isolation** : Ensures that the main server remains unaffected while the rescue server is used for diagnostics and fixes.

Utho's network configuration allows users to manage various network settings for their cloud instances, including public and private IP addresses and VPC (Virtual Private Cloud) assignments. This section provides detailed information on how to manage these network settings effectively.

#### IPv4 Public IP Address

In this section, users can view all public networks associated with their cloud instance. The information is displayed in a table format, including the following details for each public network:

* **IP Address** : The public IP address assigned to the cloud instance.
* **Gateway** : The gateway for the public network, which routes traffic between the cloud instance and other networks. A **Gateway** is a network device or router that acts as the entry or exit point between two networks (e.g., between a local network and the internet). In cloud networking, the **default gateway** connects your instances to external networks, like the internet. It is essential for directing traffic between private cloud resources and the public internet or other external systems.
* **Netmask** : The subnet mask for the public network, defining the network's IP range. A **Netmask** (or subnet mask) defines the range of IP addresses that belong to a specific subnet in a network. It determines which part of an IP address refers to the network and which part refers to the host. In cloud environments, the netmask is used to partition network resources and control IP address allocation within a Virtual Private Cloud (VPC).
* **RDNS** is the process of resolving an IP address back to its associated domain name. Unlike DNS, which maps domain names to IP addresses, DNS maps IP addresses to domain names. In the cloud, rDNS is used for identifying the source of traffic and improving security (e.g., anti-spam measures). It helps verify that the server sending requests is authorized, enhancing trust and reducing the likelihood of malicious activity.For each public network, users have the following options:
* **Update** : Allows users to update the RDNS (reverse DNS) settings. Reverse DNS translates an IP address back to a domain name, which can be important for email servers and network diagnostics.
* **Delete** : Allows users to delete the public network. Deleting a public network will remove the associated public IP address and its configurations from the cloud instance. Click this to remove the public network. A confirmation dialog will appear to prevent accidental deletions.
* **Enable/Disable** : Allows users to enable or disable the public network. Disabling a network will temporarily deactivate the associated IP addresses.
* **Enable/Disable Toggle**: Use this switch to turn the public network on or off.

Additionally, there is a button to add an additional public IP address to the cloud instance:

* **Add Additional Public IP** : Click this button to allocate a new public IP address to your cloud instance. This will open a form where you can specify the new IP address details.
* Assigning an **additional public IP** in the cloud offers benefits like **high availability** through load balancing and **scalability** for handling more traffic. It allows  **service separation** , enabling different services to have distinct IPs. Multiple IPs also improve **redundancy and failover** by providing alternative routes in case of failure. This setup enhances **security** by enabling specific firewall rules and access controls for each IP. Overall, it ensures better performance, reliability, and control over cloud resources.

#### IPv4 Private IP Address

In this section, users can view the private networks associated with their cloud instance. Private networks are used for internal communication within the data center and are not accessible from the public internet. The table displays:![1718865740673](image/index/1718865740673.png)

* **IP Address** : The private IP address assigned to the cloud instance.
* **Netmask** : The subnet mask for the private network.
* **Gateway** : The gateway for the private network.

For each private network, there is an option to delete it:

* **Delete** : Allows users to delete the private network. Deleting a private network will remove the associated private IP address and its configurations from the cloud instance.

Utho's storage allows users to view and manage storage devices attached to their cloud instance on the Utho platform. This section provides detailed information about current storage and options to update or add new storage.

### Current Storage

Users can see the current storage devices attached to their server in a table format with the following information for each storage device:

* **ID** : The unique identifier for the storage device.
* **Size** : The size of the storage device in GB.
* **Bus** : The bus type of the storage device (e.g., virtio, ide).
* **Type** : The role of the storage device (primary or secondary).
* **Created At** : The date and time when the storage device was created.
* **Update** : A button to update the storage device.
* **Delete** : A button to delete the storage device.

### Updating Utho's Storage

For each storage device, users can perform the following updates:

* **Change Bus** : Users can change the bus type of the storage device between virtio and ide.
* **Change Storage Type** : Users can change the storage type from secondary to primary and vice versa. Note that there can only be one primary storage device. When a secondary storage device is promoted to primary, the current primary storage device is automatically demoted to secondary.

To update a storage device, click the **Update Button** in the respective row. This will open a form where you can select the new bus type and storage type.

### Adding Additional Storage

Users have the option to add additional storage to their cloud instance. This new storage will be assigned as secondary storage by default. To add new storage:

1. **Input Field** : Enter the desired size for the new storage in GB.
2. **Add Storage Button** : Click this button to create and attach the new storage device to your cloud instance.

Upon successful creation, the new storage device will appear in the current storage table as a secondary storage device.

Utho's resize plans allow users to upgrade or downgrade their cloud server's resources by selecting from predefined plans.

Resizing a server in the cloud offers several benefits:

1. **Resource Optimization** : Resizing allows you to adjust CPU, memory, or storage based on current needs, ensuring you're not over-provisioning (which saves costs) or under-provisioning (which improves performance).
2. **Scalability** : You can scale up (increase resources) during high-demand periods or scale down (decrease resources) when demand decreases, helping to manage workloads efficiently.
3. **Cost Efficiency** : By resizing, you can align server resources with actual usage, preventing unnecessary costs associated with underutilized or over-provisioned resources.
4. **Improved Performance** : Resizing can enhance server performance, ensuring faster processing and better handling of applications, especially when traffic spikes.
5. **Flexibility** : Cloud platforms allow for easy resizing without downtime, providing flexibility to adapt to changing workloads or business requirements.

There are two types of resize plans available:

### 1. RAM, CPU Plans

Users can choose a plan that includes only RAM and CPU configurations. This type of plan is ideal for users who need to adjust their server's processing power and memory without altering the storage capacity. The available configurations typically include:

To resize using a RAM, CPU plan:

1. **Select a Plan** : Choose a suitable plan from the list.
2. **Resize Button** : Click the "Resize" button at the bottom of the section.

Once the plan is selected and the resize button is clicked, the server will begin the resizing process to match the selected RAM and CPU configuration.

### 2. Disk, RAM, CPU Plans

Users can choose a plan that includes Disk, RAM, and CPU configurations. This type of plan is suitable for users who need to adjust their server's processing power, memory, and storage capacity. The available configurations typically include:![1718868619147](image/index/1718868619147.png)

To resize using a Disk, RAM, CPU plan:

1. **Select a Plan** : Choose a suitable plan from the list.
2. **Resize Button** : Click the "Resize" button at the bottom of the section.

Once the plan is selected and the **Resize Cloud Server** button is clicked, the server will begin the resizing process to match the selected disk, RAM, and CPU configuration.

Utho's resize plans allow users to upgrade or downgrade their cloud server's resources by selecting from predefined plans.

### **Rebuild  Cloud**

**Rebuilding** a server in the cloud refers to the process of restoring or replacing a cloud instance (virtual machine) with a new configuration or image, usually to fix issues, apply a fresh OS installation, or modify the server's specifications. It can be done without fully deleting the server, as cloud providers allow the creation of a new instance from the same template or snapshot, keeping data integrity intact when needed.

### **Benefits of Rebuilding in Cloud** :

1. **Quick Recovery** : If a server has issues (e.g., OS corruption, misconfiguration), rebuilding it can quickly restore a functional environment without waiting for long repair processes.
2. **Fresh Configuration** : Rebuilding allows the server to be reset with a fresh configuration, clearing any previous issues and providing an opportunity to apply new system updates or configurations.
3. **Minimal Downtime** : Cloud providers typically allow for rebuilding without taking the entire infrastructure offline, reducing the impact on service availability.
4. **Cost-Effective** : Instead of manually fixing complex issues, rebuilding a server from an image or snapshot can save time and reduce troubleshooting costs.
5. **Scalability and Flexibility** : Rebuilding lets you easily scale server resources, such as CPU, RAM, or storage, by selecting a new instance type during the rebuild process.

**Utho's** Rebuild functionality allows users to completely rebuild their cloud server.

**Note** : This action will destroy all data on the server and reinstall a fresh operating system. There are two ways to rebuild the cloud server:

### 1. Using Operating System

Users can select an operating system image from the available list to reinstall on their cloud server. Refer above snippet.

### 2. Using Marketplace

Users can choose an image from the marketplace to rebuild their cloud server.Refer above snippet.

### Rebuild Process

1. **Select an Image** : Choose an image either from the operating system list or the marketplace.
2. **Input Confirmation** : Fill in the confirmation text in the provided form:
   `I am aware this action will delete data permanently and build a fresh server.`
3. **Rebuild Button** : Click the "**Rebuild Cloud Server**" button to initiate the rebuild process.

Upon confirmation and clicking the rebuild button, the server will start the rebuilding process with the selected image, resulting in a fresh installation of the operating system and complete data loss.

**Utho's firewall** feature enables users to configure and manage firewall settings for their cloud server, offering flexibility in network security. A **firewall** is a security system that monitors and controls incoming and outgoing network traffic based on predefined security rules. Its primary function is to block or allow data packets between your network and external sources (like the internet) to protect against unauthorized access, cyberattacks, and threats. Firewalls can be hardware-based, software-based, or a combination of both. They are commonly used to secure networks by filtering traffic, preventing malicious activities, and ensuring that only authorized users can access certain services or resources.

### Attaching a Firewall

Users have the option to attach existing firewalls or create new ones for their cloud server:

1. **Attach Existing Firewall** : Users can select from a list of pre-existing firewalls and attach one to their cloud server.
2. **Create New Firewall** : If no suitable firewall exists, users can create a new one tailored to their specific security requirements. During the creation process, users define firewall rules and settings to enforce network traffic policies.

### Managing Attached Firewalls

Once a firewall is attached to the cloud server, users can:

* **View Attached Firewalls** : See a list of all firewalls currently attached to their cloud server.
* **Detach Firewalls** : For each attached firewall, there is an option to detach it from the cloud server. This action removes the firewall's rules and configuration from affecting the cloud server's network traffic.

### Workflow

1. **Create or Select Firewall** : Depending on the user's needs, either create a new firewall or select an existing one.
2. **Attach Firewall** : Once chosen, attach the firewall to the cloud server to implement specified security measures.
3. **Manage Firewalls** : Regularly review and manage attached firewalls to ensure optimal security and network performance.

Utho's firewall management ensures that cloud servers remain secure against unauthorized access and maintain efficient network traffic management according to user-defined policies.

Utho provides users with the capability to boot their cloud server from ISO images, enabling manual OS installation directly from the selected ISO. **ISO** refers to standards set by the **International Organization for Standardization** to ensure security, privacy, and quality. Key standards include **ISO/IEC 27001** (information security management), **ISO/IEC 27017** (cloud security), and **ISO/IEC 27018** (privacy protection for personal data). These standards help cloud providers demonstrate compliance with best practices and industry regulations. They ensure that cloud services are secure, reliable, and protect customer data, which builds trust and assures compliance with legal and regulatory requirements.

### Mounting an ISO

To mount an ISO image to your cloud server:

1. **Select ISO** : Choose an ISO image from the dropdown list of available ISOs.
2. **Mount and Boot ISO** : Click the "Mount and Boot ISO" button to initiate the mounting process.

Once mounted, the ISO image will be associated with your cloud server and will appear in the list of mounted ISOs.

### Managing Mounted ISOs

Users can manage mounted ISO images as follows:

* **View Mounted ISOs** : The list displays all currently mounted ISO images associated with the cloud server.
* **Unmount ISO** : For each mounted ISO, there is an option to unmount it from the server. This action disconnects the ISO image from the server's boot process.

**Benefits of Mounting ISO:**

Mounting an **ISO file** to a server provides several benefits, especially for tasks like software installation, system recovery, or deployment in cloud and virtualized environments. Here are the main advantages:

1. **Software Installation** : Mounting an ISO allows you to install operating systems, applications, or other software directly from the ISO image, without needing physical media like CDs or DVDs.
2. **System Recovery** : You can mount recovery or bootable ISO images to troubleshoot, repair, or restore a system without needing to reboot from physical media.
3. **Efficiency and Convenience** : It eliminates the need for physical disks or external devices. The ISO image is mounted directly on the server, saving time and hardware resources.
4. **Virtual Environments** : In virtualized environments, mounting an ISO is a quick way to deploy operating systems or software without using physical storage devices.
5. **Cost-Effective** : Reduces hardware dependencies and media costs by relying on virtual disk images for software distribution or system management.

Overall, mounting an ISO to a server enhances flexibility, automation, and convenience in managing server deployments and system recovery.

### Workflow

1. **Select ISO** : Choose the ISO image containing the desired operating system or software for installation.
2. **Mount ISO** : Initiate the mounting process to start booting the server from the selected ISO.
3. **Install OS** : Follow the prompts to install the operating system manually from the mounted ISO image.

Utho's ISO management feature allows users to customize their server configurations and perform manual OS installations conveniently and securely.

Utho provides transparent billing details and options to manage your cloud server's billing cycle and costs effectively.

### Billing Cycle

A **billing cycle** is the period of time between two consecutive billings for a service or subscription. In the context of cloud services, it refers to the recurring time frame (e.g., monthly, quarterly, or annually) for which usage is measured and billed.

The billing section displays current billing cycle details:

* **Current Billing Cycle**
* **Discount**

### Costs Breakdown

The breakdown of costs associated with your cloud server includes:

* **Total Cost:**   Total Cost refers to the overall expense incurred for using a service or product over a specific period. In the context of cloud services, it includes all charges for resources like compute, storage, bandwidth, and additional services. It’s the sum of all individual costs associated with running your infrastructure in the cloud.
* **OS Cost:** OS Cost refers to the charges associated with the operating system (OS) running on a virtual machine (VM) in the cloud. Some cloud providers charge extra for licensing specific operating systems (like Windows or certain enterprise Linux distributions). For example, Windows Server typically comes with additional licensing costs compared to Linux distributions, which are often free
* **Cloud Cost:** Cloud Cost refers to the overall expenditure related to using cloud services, including compute power, storage, networking, and other resources provided by cloud platforms (e.g., AWS, Azure, Google Cloud). Cloud cost can include:
* **Compute charges** (based on virtual machines or instances)
* **Storage costs** (for databases, file storage, backups)
* **Data transfer fees** (for inbound and outbound traffic)
* **Additional services** (such as load balancers, security, and monitoring tools)
* **Backup Cost:** Backup costs refer to the expenses of storing and managing data backups in the cloud. These costs depend on factors like storage type (e.g., standard vs. archival),  data volume ,  backup frequency , and  retention period . Cloud providers charge for the amount of storage used and may offer different pricing for faster or slower access. Additional costs can come from managed backup services and features like encryption or monitoring. Optimizing backup strategies can help reduce unnecessary costs.

### Additional Charges

* **18% GST** : GST will be applied extra to the total cost.

### Available Billing Cycles

Users can view and select from available billing cycles for their cloud server:

* **Change Billing Cycle** : Clicking this option generates an invoice for the new billing cycle. Once paid, the cloud server's billing cycle will be updated accordingly.

### Workflow

1. **Review Current Cycle** : Check the current billing cycle and associated costs.
2. **Select New Cycle** : Choose a different billing cycle from the available options.
3. **Generate Invoice** : Click "Change Billing Cycle" to receive an invoice for the new cycle.
4. **Payment** : Once the invoice is received, proceed to make the payment to update the billing cycle.

Utho's billing features ensure clarity and flexibility in managing cloud server costs, accommodating various billing preferences efficiently.

Utho offers a snapshot feature to capture and manage point-in-time backups of your cloud server, providing options to take new snapshots, view current snapshots, and perform snapshot-related actions.

### Take New Snapshot

To create a new snapshot of your cloud server:

1. **Power Off Recommendation** : It is recommended to power off your cloud server before taking a snapshot to ensure data consistency.
2. **Snapshot Cost** : Snapshots are charged based on the disk size used, at a rate of Rs. 4 per GB per month.

### Taking a Live Snapshot

1. **Click on "Take Live Snapshot"** : Click the "Take Live Snapshot" option to initiate the live snapshot process.
2. **Enter Snapshot Name** : In the drawer that appears, provide a name for your snapshot in the input field labeled "Snapshot Name".
3. **Create Snapshot** : Click the "Create" button to create the snapshot.

### Snapshot Actions

Upon clicking "Create", the snapshot will be generated and added to the snapshot table, displaying the following details:

* **Snapshot Name** : The name provided for the snapshot.
* **Snapshot Size** : The size of the snapshot.
* **Created At** : The timestamp indicating when the snapshot was created.
* **Actions** : Options to restore or delete the snapshot as needed.
  * **Restore** : Restore the cloud server to a previous state using a selected snapshot.
  * **Delete** : Remove unnecessary snapshots to manage storage efficiently.

Workflow

1. **Take New Snapshot** : Click on "Take New Snapshot" to capture a point-in-time backup of your cloud server's current state.
2. **Manage Snapshots** : Once snapshots are created, manage them including restoring the server from a snapshot or deleting outdated snapshots.

Utho's snapshot management feature ensures data protection and recovery options are readily available to maintain the integrity and availability of your cloud server.

The "Destroy" feature allows users to permanently delete their cloud server from Utho's platform. This action cannot be undone and will result in the complete removal of all data associated with the server.![1718873866650](image/index/1718873866650.png)

### Warning

* **Data Deletion** : This action will delete all data from the server permanently.

### Confirmation Process

1. **Click on "Destroy"** : To initiate the destruction process, click on the "Destroy" option.
2. **Confirmation Popup** : A confirmation popup will appear, prompting you to confirm the cloud server's name.
3. **Enter Server Name** : Enter the name of the cloud server as a confirmation step.
4. **Confirm Destruction** : Click "Confirm" to proceed with destroying the server.

### Note

* **Irreversible Action** : Once confirmed, the destruction process cannot be undone.

### Workflow

1. **Initiate Destruction** : Click on "Destroy" to begin the process.
2. **Confirm Server Name** : Enter the server name in the confirmation popup.
3. **Execute Destruction** : Click "Confirm" to execute the destruction of the cloud server.

The "Destroy" feature ensures that users can securely remove cloud servers from Utho's platform when no longer needed, emphasizing data security and management flexibility.

---
