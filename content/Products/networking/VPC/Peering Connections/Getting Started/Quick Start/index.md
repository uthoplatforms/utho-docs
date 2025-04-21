## **Quick Start Guide for Peering Connections in Utho Cloud**

### **Overview**

Peering Connections in Utho Cloud enable private network connectivity between two VPCs, allowing resources in different VPCs to communicate securely without traversing the public internet. This guide provides step-by-step instructions for viewing, creating, and deleting peering connections.

---

### **1. How to View Peering Connections**

* **[Login](https://console.utho.com/login)** to your Utho Cloud account.
* From the sidebar menu, go to **Networking** or **VPC** and click on **Peering Connections** to open the  **[Peering Connections Listing Page](https://console.utho.com/peeringconnection)** .
* The list displays all existing peering connections with details such as:
  * **Name** : Name of the peering connection.
  * **Requester VPC / Subnet** : The initiating VPC and its subnet (if applicable).
  * **Accepter VPC / Subnet** : The target VPC and its subnet (if applicable).
  * **Status** : Indicates if the peering connection is active or pending.
  * **Actions** : Buttons to manage or delete the connection.

---

### **2. How to Create a Peering Connection**

* On the  **Peering Connections Listing Page** , click the **"Create Peering Connection"** button at the top of the list.
* A drawer will open asking for the following configuration:
  * **Name** : Enter a name for the peering connection.
  * **Data Center Location** : Select a DC location from the dropdown.
  * **Requester VPC** : Select the initiating VPC from the dropdown.
  * If this VPC has subnets, a second dropdown will appear to choose a  **Requester Subnet** .
  * **Accepter VPC** : Select the target VPC from the dropdown.
  * If this VPC has subnets, a second dropdown will appear to choose an  **Accepter Subnet** .
* After filling in all fields, click the **"Create Peering Connection"** button.
* A success notification will confirm the creation, and the new connection will appear in the list.

---

### **3. How to Delete a Peering Connection**

* On the  **Peering Connections Listing Page** , locate the peering connection you want to delete.
* Click the **"Delete"** button at the end of its row.
* A confirmation popup will appear. Click **"OK"** to permanently delete the connection.
* The connection will be removed from the list, and you can verify the deletion by refreshing the listing.

---

### **Conclusion**

Peering Connections in Utho Cloud make inter-VPC communication secure and efficient. This quick start guide helps you perform essential actions such as viewing, creating, and deleting peering connections—ensuring your network topology is optimized and under control.
