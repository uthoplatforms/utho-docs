---
weight: 10
title: Quickstart 
description: "Learn how Utho makes Kubernetes management simple and easy so you easily anticipate your kubernetes infrastructure costs"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/compute/kubernetes/getting-started/quick-start']
icon: 'kubernetes'
tab: true
---
Utho Kubernetes Cluster is a robust container orchestration platform designed to efficiently manage and scale containerized applications. Built for reliability and scalability, it provides seamless deployment, automated scaling, and self-healing capabilities for applications. Utho Kubernetes simplifies workload distribution across nodes, ensuring optimal resource utilization and high availability. It supports multi-environment setups for development, staging, and production, offering enhanced security and performance. With integrated monitoring and logging tools, users gain full visibility into cluster operations. Utho Kubernetes is ideal for businesses seeking to streamline DevOps workflows and deploy microservices with ease.

## Deploy a Kubernetes Cluster on Utho Cloud

Follow these steps to deploy a Kubernetes cluster on Utho Cloud using the Kubernetes deployment page:

**1. Initial Start**

* **Log in** to your account on our [platform](https://console.utho.com "https://console.utho.com").
* **Navigate** to the top toolbar and locate the **[Deploy](https://console.utho.com/kubernetes/deploy)** dropdown menu.
* **Select** the **[Kubernetes](https://console.utho.com/kubernetes/deploy)** option from the dropdown

**2. Select DC Location**

* Choose a **Data Center** location from the dropdown menu.
* Example: Select "Mumbai, India."

**3. Select Kubernetes Version**

* Enter a **Cluster Label** to identify your Kubernetes cluster.
* Choose a **Cluster Version** from the dropdown menu.

**4. Manage Node Worker Pools**

* Click on **Add Node Pool** to configure worker nodes for the cluster.
* Provide a **Pool Name** for the node pool.
* Select a **Node Size** from the dropdown menu to allocate resources for the nodes.
* Specify the **Desired Count** of nodes in the node pool.
* The **Total Cost** will be calculated automatically based on the configuration.

**5. Configure VPC Network**

* Select an existing **VPC Network** or click **Add New VPC** to create a new one.

**6. Configure Endpoint Access Info**

* Choose the endpoint access level for the Kubernetes API server:
  * **Public** : Accessible from outside the VPC.
  * **Public and Private** : Accessible from both outside and within the VPC.
  * **Private** : Accessible only within the VPC.

**7. Configure Security Group**

* Select a **Security Group** from the available options or click **Add New Security Group** to create a new one.

**8. Select CPU Model**

* Choose the desired **CPU Model** for the server:
  * **AMD**
  * **Intel**

**9. Deploy the Cluster**

* Review the total cost displayed at the bottom.
* Click **Deploy Cluster** to initiate the deployment process.

## Deploy an Application on Utho Kubernetes Cluster

**1. Download the configuration file**

* Once deployed, navigate to the [Manage Section]() of the cluster.
* Download the configuration file (`kubeconfig`) for the cluster.
* Use this file to connect to your Kubernetes cluster with tools like `kubectl`.

**2. Deploy Your Application**

* Prepare your application’s deployment and service YAML file.
* Use `kubectl` to apply the configuration and monitor the application deployment.

**3. Access and Monitor the Application**

* Access your application using the external IP of the LoadBalancer service.
* Use `kubectl` commands to monitor pods, logs, and the application’s health.

For a more detailed walkthrough, click here to deploy a application on kubernetes cluster.

## Manage the Kubernetes Cluster on Utho

---

##### 1. Access the Existing Kubernetes Cluster

* Open the [K8S Nodes](https://conosle.utho.com/kubernetes/:id "https://conosle.utho.com/kubernetes/:id") tab to view the node pools for your cluster.

##### 2. View Existing Node Pools

* The node pools are listed under Manage Node Worker Pools.
  * Each pool displays:

    * **Pool Name**: Identifier for the node pool.
    * **Node Size**: Specifications like vCPU, RAM, and SSD.
    * **Private IP**: The internal IP address for the node(s).
    * **Status**: Ensure the node pool shows as Active.

---

##### 3. Add a New Node Pool

* Click **Add Node Pool** to create a new node pool.
* In the Pool Worker configuration:
  * Provide a **Pool Name**.
  * Select the desired **Node Size** from the available plans (Basic, CPU Optimized, or Memory Optimized).
  * Set the **Desired Count** of nodes for this pool.
  * Review the Total **Cost** for the selected configuration.
* Click **Create Node Pool** to finalize the addition.

---

##### 4. Scale Existing Node Pools

* To adjust the number of nodes in a pool:
  * Click Update **Scale Pool** for the selected node pool.
  * Modify the **Desired Count** of nodes.
  * Review the updated Total **Cost**.
  * Click **Update Scale Pool** to apply changes.

---

##### 5. Edit Node Pool Configuration

* Select an existing **node pool** and click Update Scale Pool to:
  * Modify the **pool name** if needed.
  * Adjust resource configurations for the pool.
* **Save** changes once configurations are updated.

---

##### 6. Configure VPC Networking

* Navigate to the [VPC tab](https://conosle.utho.com/kubernetes/:id "https://conosle.utho.com/kubernetes/:id"):
  * View and manage the **VPC ID** and associated **Network CIDR**.
  * Use the Manage button to configure additional settings for the **VPC**.

---

##### 7. Assign Security Groups

* Navigate to the [Security Groups](https://conosle.utho.com/kubernetes/:id "https://conosle.utho.com/kubernetes/:id") tab:
  * **Assign** an existing firewall to the Kubernetes cluster or create a new one.
  * Use the **Create** or **Assign Firewall** button to set up firewall rules for managing **inbound** and **outbound** traffic.
  * Ensure **security rules** align with your application requirements.

---

##### 8. Destroy the Kubernetes Cluster

* Navigate to the [Destroy tab](https://conosle.utho.com/kubernetes/:id "https://conosle.utho.com/kubernetes/:id"):
  * Use this section if you need to permanently **delete** **the Kubernetes cluster.**
  * **Confirm** the deletion, noting that all worker nodes and resources will be removed, and the cluster’s IP address will be deprovisioned.

---

### Key Actions You Can Perform:

1. **Add Node Pools**: Expand your cluster by adding more nodes.
2. **Scale Nodes**: Adjust the number of nodes in an existing pool.
3. **Configure VPC**: Apply VPC to kubernetes cluster.
4. **Configure Security**: Apply firewalls to secure cluster communication.
5. **Destroy Cluster**: Remove the cluster and all its resources when no longer needed.
