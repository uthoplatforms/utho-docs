---
VPNweight: 30
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

## **Accessing VPN**

1. Open the **Utho Cloud Platform** dashboard.
2. Click on **"VPN"** in the sidebar.
3. You will be redirected to the **VPN** listing page.
4. Click on **[Deploy New VPN](https://console.utho.com/vpn/deploy)** to open the deployment page.

On the  **Deploy Page** , configure the following:

Then a vpn-deploy page will open.

1. **Datacenter Location:** Choose the desired datacenter location from the DC Location list.
2. **VPC Network :** Choose Vpc and its subnet (if available) for your VPN.
3. **Security Group:** Choose firewall/Security group for your VPN.
4. **VPN Name:** Enter a unique name for your VPN.
5. **Number of VPN Users:** Specify the number of VPN users to add.
6. **Billing Cycle:** Select the preferred billing cycle.
7. **Review Total Amount:** The total cost will be displayed in the bottom left corner of the page.
8. **Deploy VPN:** Click the **Deploy VPN** button on the right side to create your VPN.

### Uses of DC Location, VPC Network, and Security Group in VPN

1. **DC Location (Data Center Location)**:

   - **Definition**: Refers to the physical location of the data center where cloud resources are hosted.
   - **Use in VPN**: The DC location affects latency and performance of the VPN connection. Selecting a data center geographically closer to users or on-premise infrastructure can enhance speed and reduce delays.
2. **VPC Network (Virtual Private Cloud Network)**:

   - **Definition**: A logically isolated network within the cloud provider’s environment that allows you to launch resources in a virtual network.
   - **Use in VPN**: VPN connections often link users or on-premise networks to a VPC, enabling secure communication between the local network and cloud resources. This setup allows resources in the VPC to be accessed securely over the VPN.
3. **Security Group**:

   - **Definition**: A virtual firewall that controls inbound and outbound traffic for resources within a VPC.
   - **Use in VPN**: Security groups define rules that govern which traffic can flow through the VPN connection. They help ensure that only authorized traffic is allowed, enhancing security by restricting access based on IP addresses, protocols, and ports.

#### Verify Deployment:

Your VPN should now be active and visible in the list of deployed VPNs.
Here you can see your deployed vpn with configuration details your provided during the deployment process and you can manage you vpn by clicking on mange button, for detailed info check for the manage vpn section in the Utho docs.

## VPN Configuration Info

At the top of the Manage section, users can view the configuration information of the selected VPN. This includes:

* **VPN Name:** The unique name assigned to the VPN.
* **Datacenter Location:** The chosen datacenter location.
* **Number of Users:** The number of VPN users associated with the VPN.
* **VPN Gateway:** The IP address or hostname of the VPN gateway.
* **Status:** The current status of the VPN (e.g., active, inactive, pending).

## Manage Users

In the Manage Users section, users can add, delete, and download VPN users. This section provides the following functionalities:

* **Add User:** Click the **Add User** button to open a form where you can enter the user details such as username, email, and password.

### Use of VPN Users in Utho Cloud

**VPN users** connect securely to cloud resources via a VPN, providing:

1. **Secure Remote Access**: Access cloud resources securely from anywhere.
2. **Authentication**: Ensure only authorized users connect.
3. **Data Privacy**: Encrypt communication to protect sensitive data.
4. **Network Segmentation**: Limit user access to specific resources.
5. **Accessing Private Services**: Enable access to internal cloud resources.
6. **Business Continuity**: Maintain access during disruptions.

VPN users ensure secure, controlled access to cloud resources and services.

* **Remove User:** Select a user from the list and click the **Remove** button to remove the user from the VPN.
* **Download User:** Select a user from the list, click the **[Download]()** button, which will download your vpn user into your browser.

### How to Use a Downloaded VPN

1. **Install the VPN**:

   - Download and install the VPN application on your device (PC, macOS, or mobile).
2. **Open the VPN Client**:

   - Launch the app after installation.
3. **Login**:

   - Enter your **username** and **password** provided by your VPN service.
4. **Choose a Server**:

   - Select a server location (or let the app choose the best one automatically).
5. **Connect**:

   - Click **Connect** to establish a secure VPN connection.
6. **Verify the Connection**:

   - Check that you’re connected and your internet traffic is encrypted.
7. **Use the VPN**:

   - Enjoy secure browsing or accessing private cloud resources.
8. **Disconnect**:

   - Click **Disconnect** once you’re finished using the VPN.

## Destroy

In the Destroy section, users can terminate the VPN instance. This action is irreversible and will permanently delete the VPN and all associated data.

Click the **Destroy VPN** button.

##### **Confirmation:**

A confirmation dialog will appear. Confirm the action to proceed with destroying the VPN.

When you provide the confirmation then your VPN Instance will destroy.
