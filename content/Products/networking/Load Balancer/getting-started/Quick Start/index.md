---
Load Balancerweight: 30
title: "Quick Start"
title_meta: "Quick Start"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/networking/Load Balancer/getting-started/Quick Start"]
icon: guides
tab: true
---
## **Login or Sign Up**

1. Go to the **Utho Cloud Platform** [login](https://console.utho.com/login) page.
2. Enter your credentials and click  **Login** .
3. If you don't have an account, sign up [here](https://console.utho.com/signup).

---

## **Accessing Load Balancer**

1. Open the **Utho Cloud Platform** dashboard.
2. Click on **"Load Balancer"** in the sidebar.
3. You will be redirected to the **Load Balancer** listing page.
4. Click on **[Create LoadBalancer](https://console.utho.com/ebs/deployhttps://console.utho.com/ebs/deploy)** to open the deployment page.

**Load Balancer Deployment Process**

Deploying a load balancer in Utho Cloud involves several steps that ensure proper configuration, security, and scalability of the load balancer. Below is a comprehensive guide to deploying a load balancer:

#### **Steps for Deploying a Load Balancer** :

1. **Select Data Center Location** :

* In the **Data Center Location** dropdown, the user will select the appropriate geographical location for the load balancer. This selection determines where the load balancer will be deployed.

2. **Select Load Balancer Type** :

* In the **Load Balancer Type** dropdown, the user will choose between two types of load balancers:
  * **Application Load Balancer** : Suitable for HTTP and HTTPS traffic, handling requests at the application layer (Layer 7 of the OSI model).
  * **Network Load Balancer** : Operates at the network layer (Layer 4), handling traffic more efficiently for high-performance applications that require low latency.

3. **Choose VPC Network** :

* The user will then select the **VPC Network** for the load balancer. The VPC (Virtual Private Cloud) is the isolated network in which the load balancer will operate.
  * **If a VPC exists for the selected location** , the user can select it from the available list.
  * **If there is no existing VPC** , the user should click on the **"Add New VPC"** button to create a new VPC for the load balancer. The process for creating a VPC will involve specifying IP range and network configurations.

4. **Choose Subnet** :

* If the selected VPC has existing subnets, the user is required to select a **Subnet** within that VPC for the load balancer. This is a mandatory step.
  * If no subnet is available, the user may need to create one under the selected VPC to ensure the load balancer is correctly provisioned.

5. **Configure Public IP Status** :

* The user will choose whether the **Public IP** for the load balancer should be **enabled** or  **disabled** .
  * **Enabled** : The load balancer will be assigned a public IP, making it accessible from the internet.
  * **Disabled** : The load balancer will not have a public IP and will only be accessible from within the VPC or through private endpoints.

6. **Choose Security Group** :

* The user will select an existing **Security Group** for the load balancer. The security group controls the inbound and outbound traffic rules to and from the load balancer.
  * **If a security group is not available** , the user can click the **"Add Security Group"** button to create a new security group. The new security group can be customized with specific rules to control access to the load balancer.

7. **Enter Load Balancer Name** :

* The user will enter a **name** for the load balancer. This name is used to identify the load balancer within the Utho Cloud platform.

8. **Create the Load Balancer** :

* After completing all the required fields, the user will click the **"Create Load Balancer"** button to initiate the deployment of the load balancer.

9. **Load Balancer Creation and Redirection** :

* Once the load balancer is successfully created, the system will automatically take the user to the  **Manage Section** .

# Load Balancer Management Process

## Step 1: Manage Load Balancer

- After clicking the **Manage** button, the user is redirected to the **Manage Section**.
- The **Manage Section** contains two subsections:
  1. **Configuration**
  2. **Destroy**

## Step 2: Configuration Section

Under the **Configuration** section, the user has the option to **Create Frontend**.

### 2.1: Create Frontend

Creating a **frontend** in a load balancer is used to define how **incoming traffic** is received and processed. It specifies the  **protocol** , **port** , and  **traffic routing rules** , allowing the load balancer to manage client connections and distribute them to appropriate backend servers.

- To create a new frontend, the user needs to click on the **Create Frontend** button.
- After clicking **Create Frontend**, the user will be redirected to a new page where they must provide the following details:

  1. **Name**: Enter a name for the frontend.
  2. **Port**: Specify the port number.
  3. **Protocol**: Select the protocol (HTTP, HTTPS, TCP).
  4. **Algorithm**: Choose the load balancing algorithm (Round Robin, Least Connections, etc.).
- Additionally, the user has the option to choose the following settings:

  - **Redirect to HTTPS**: Enable this option to redirect HTTP traffic to HTTPS.
  - **Sticky Sessions**: Enable sticky sessions to ensure a user's requests are sent to the same backend server.
- After entering all the details and selecting the desired settings, the user clicks **Add Frontend**.
- Once the frontend is successfully created, it will appear in the list of configured frontends.
- If the user selects **HTTPS** as the protocol, they will be asked to provide **additional data**:

  1. **SSH Key**: The user will be prompted to add an SSH key for secure communication.

  - **If no SSH key is available**:
    - The system will display an option to **Add SSH Key**.
    - Upon clicking **Add SSH Key**, the user will be redirected to the **SSH Key Page**, where they can create or upload an SSH key.
- After entering all the required details (including SSH key if applicable), the user can click **Add Frontend**.
- Once the frontend is successfully created, it will appear in the list of configured frontends.

---

## Explanation:

### Port:

A **port** is a communication endpoint used by a load balancer to receive traffic from clients. When creating a frontend in a load balancer, the port defines where the load balancer listens for incoming requests (e.g., port 80 for HTTP, port 443 for HTTPS). It helps route traffic to the correct backend server based on the protocol and service being used. Ports ensure the load balancer knows which service to forward traffic to. Custom ports can also be used for non-standard applications or services.

### **Frontend Protocol Dropdowns (HTTP, HTTPS, TCP)**

1. **HTTP (Hypertext Transfer Protocol)** :

* **What happens** : The load balancer will handle **unencrypted web traffic** on port 80. It forwards incoming HTTP requests from clients (such as browsers) to backend servers.
* **Use case** : Suitable for websites or applications where  **encryption is not required** , or the traffic will be encrypted at a later stage (e.g., SSL termination at the backend).

1. **HTTPS (Hypertext Transfer Protocol Secure)** :

* **What happens** : The load balancer will handle **encrypted web traffic** on port 443. It establishes a secure **SSL/TLS connection** with clients, ensuring data security.
* **Use case** : Used when secure communication is needed, such as for  **financial applications, login pages** , or any service that requires data privacy and encryption.

1. **TCP (Transmission Control Protocol)** :

* **What happens** : The load balancer handles **raw TCP traffic** and forwards it to backend servers without any application-level protocol (like HTTP/HTTPS). This allows the load balancer to work with any application protocol that uses TCP.
* **Use case** : Typically used for applications like  **databases** ,  **mail servers** , or any service that operates over **non-HTTP protocols** (e.g., MySQL, FTP, custom TCP-based protocols).

### **Use of Creating Frontend with Different Protocols** :

* **HTTP/HTTPS** : Ideal for web applications, where the load balancer handles HTTP requests, including SSL termination (for HTTPS) to reduce backend load.
* **TCP** : Useful for non-HTTP applications that require low-level transport control, allowing traffic for any service using TCP-based communication protocols.

## Algorithm

**Round Robin** and **Least Connections** are **load balancing algorithms** that determine how incoming requests are distributed across backend servers. Here's a more specific explanation:

### **1. Round Robin Algorithm in Load Balancer** :

* **How it works** : The load balancer distributes incoming requests to backend servers in a  **sequential, circular manner** . It sends the first request to Server 1, the second to Server 2, the third to Server 3, and once it reaches the last server, it loops back to Server 1.
* **Use case** : Best for **stateless applications** where each request is independent, and each server has  **similar performance** . It works well when there’s no need to consider server load or capacity.

### **2. Least Connections Algorithm in Load Balancer** :

* **How it works** : The load balancer routes incoming traffic to the backend server that has the  **fewest active connections** . This method ensures that servers that are less busy or handling fewer requests are given new traffic, helping to balance the load more efficiently.
* **Use case** : Ideal for **stateful applications** where backend servers might have different capacities or varying workloads. It’s especially effective when servers have **uneven load** or the requests require more processing time.

---

## Step 3: Deploying the Load Balancer

- When deploying the load balancer, the user selects a **Network** from the available options.

### 3.1: Add Backend Server

A **backend server** in a load balancer processes traffic forwarded by the load balancer. It helps distribute the load, ensuring **scalability** and **high availability** by handling more traffic across multiple servers. By creating backend servers, users can improve  **performance** , prevent overload, and ensure **fault tolerance** in case of server failure.

Once the frontend is deployed, the **Frontend** section will include the option to **Add Backend Server**.

- When the user clicks **Add Backend Server**, they will be prompted to provide the following details:

  1. **Server Type**: Choose the type of backend server (e.g., Web Server, Database Server, etc.). Which you want to attach with the load balancer.
  2. **Port**: Specify the port number for the backend server.
  3. **Backend Server**: Select the backend server from the available options.
- After entering the required details, the user clicks **Add Server**.
- The newly added backend server will appear in the list of **Backend Servers**.

## Explanation:-

### **Use of Server Type, Port, Backend Server, and Custom IP in Load Balancer**

1. **Server Type** :

* **What it is** : The **server type** defines the type of backend server you're adding to the load balancer. This could be an  **application server** ,  **database server** , or any other type of resource that handles specific traffic.
* **Use** : Choosing the correct **server type** ensures the load balancer routes traffic to the appropriate resource based on its function (e.g., web server, database server).

2. **Port** :

* **What it is** : The **port** specifies the network port on the backend server where the load balancer should send traffic. For example, **port 80** for HTTP, **port 443** for HTTPS, or any custom port for specific services.
* **Use** : Configuring the **port** ensures that the load balancer directs traffic to the correct service on the backend server (e.g., web traffic to port 80, database queries to port 3306).

3. **Backend Server** :

* **What it is** : The **backend server** is the resource that will process the requests sent by the load balancer. This could be a virtual machine, container, or any service running in your network.
* **Use** : Adding a **backend server** to the load balancer allows the load balancer to distribute incoming traffic across multiple backend servers, ensuring **scalability** and  **high availability** .

4. **Custom IP** :

* **What it is** : A **custom IP** allows you to specify a **specific IP address** for a backend server, rather than using the default IP assigned by your cloud provider.
* **Use** : This is useful when you need to add a backend server that has a **static IP address** or when routing traffic to a **non-cloud resource** or a server outside your cloud environment. It ensures traffic is directed correctly, even if the server's IP address is fixed or specific.

---

## Step 4: Adding ACL Rules (For Application Server Type)

If the user selects **Application** as the server type during deployment then after clicking **Add Frontend**, the user will be prompted to **Add ACL Rules**.

### 4.1: Add ACL Rules & Advance Routing

**ACL rules** are used to control **traffic flow** between the load balancer and backend servers by allowing or denying access based on specific conditions (e.g., IP address, protocol, or request type).

**Advanced routing** involves complex routing configurations that allow the load balancer to direct traffic based on specific criteria such as  **URL paths** ,**host headers** ,  **query strings** , or  **cookies** .

- The user will be asked to define **Access Control List (ACL) Rules** for the selected application.
  Here user have to select name and condition for the acl rule creation.
- The rules will determine which requests are allowed or denied based on certain conditions.
- Now once the ACL rule is added then click on **Add new routing** then a side bar will open where you have to enter the data like, acl rule, condition and target group. Now after entering all the data click on **Add Routing** then routing will be created.

Once the Advance routing are added, they will be applied to the frontend configuration.

## **Explanation:-**

* **Name** :

 The **name** is a **unique identifier** for the ACL rule, used to label and organize it within the configuration. It helps easily identify and manage rules.      For example, a name could be "Allow_Admin_Access" to specify a rule allowing access to admin resources.

* **Condition** :

  The **condition** defines the **criteria** that must be met for the rule to be triggered. Common conditions include attributes like the  **source IP address** ,  **HTTP headers** ,  **URL path** , or **protocol** (e.g., HTTP, HTTPS). Conditions allow you to create rules based on traffic characteristics.
* **Condition Value** :

  The **condition value** is the **specific parameter** that the condition is checked against. For example, if the condition is based on the  **source IP** , the condition value might be `192.168.1.0/24` (allowing traffic from this IP range). Similarly, if the condition is a  **URL path** , the condition value could be `/admin/*` to target all paths under `/admin/`.

**Advanced routing** allows you to direct traffic based on specific criteria like  **URL paths** ,  **HTTP headers** ,  **query strings** , or  **cookies** . This ensures more  **efficient traffic distribution** , enables **personalization** (e.g., A/B testing), and optimizes resource usage. It provides greater **flexibility** in routing requests to the appropriate backend servers, improving **scalability** and  **performance** .

* **ACL Rule** : Filters traffic before it reaches the target group, ensuring security.
* **Condition** : Defines the rules for routing traffic to the correct target group based on request attributes (e.g., path, headers).
* **Target Group** : A **target group** is a set of backend resources (e.g., EC2 instances, containers, IP addresses) that the load balancer routes traffic to. When adding a target group, you specify the **protocol** and **port** for communication. It ensures that the load balancer directs traffic to the appropriate backend service for processing.

---

## Destroy

In the Destroy section, users can terminate the Load Balancer. This action is irreversible and will permanently delete the Load Balancer and all associated data. To destroy a Load Balancer

Click the **Destroy Load Balancer** button.

##### **Confirmation:**

A confirmation dialog will appear. Confirm the action to proceed with destroying the Load Balancer.

When you provide the confirmation then your Load Balancer Instance will destroy.
