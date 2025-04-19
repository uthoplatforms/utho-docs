# **Quick Start Guide: Auto Scaling**

This guide will help you quickly deploy, configure, and manage your Auto Scaling instances on Utho Cloud.

---

## **1. How to Deploy an Auto Scaling Instance**

### **Step 1: Log In or Sign Up**

1. Visit the **Utho Cloud Platform** [Login](https://console.utho.com/login) page.
2. Enter your credentials and click  **Login** .
3. If you’re not registered, sign up [here](https://console.utho.com/signup).

### **Step 2: Navigate to Auto Scaling**

1. Open the **Utho Cloud Platform** dashboard.
2. Click **"Auto Scaling"** in the sidebar.
3. You’ll be redirected to the  **Auto Scaling Listing Page** , where you can view and manage your scaling instances.

### **Step 3: Deploy a New Auto Scaling Instance**

1. Click the **"Create New"** button to open the Auto Scaling deployment page.
2. Configure your Auto Scaling instance:
   * **Data Center Location** : Select the data center closest to your users.
   * **Stack** : Choose a stack with your required OS and software configuration.
   * **Plan** : Select the appropriate plan based on RAM, vCPUs, and SSD disk size.
   * **VPC and Subnet** : Choose the VPC and subnet for the instance.
   * **CPU Model** : Select between **AMD** or  **Intel** .
3. Attach a **Firewall** (optional) to protect your instance.
4. Attach a **Load Balancer/Target Group** (optional) for better traffic management.
5. Set your instance's **Scaling Parameters** (Max Size, Min Size, Desired Size).
6. Configure **Scaling Policies** to set triggers based on CPU or RAM usage.
7. Define a **Scaling Schedule** (optional) to set time-based scaling actions.
8. Provide a **Name** for the instance.
9. Click **"Deploy Auto Scaling"** to deploy your instance.

### **Step 4: Verify Deployment**

1. After deployment, you will be redirected to the **Manage Page** of your newly deployed instance.
2. Verify the instance's **status** is "Active", which indicates it's running smoothly.

---

## **2. Managing Auto Scaling**

Once inside the  **Manage Page** , you can access the following sections:

### **1. Overview**

* Contains the scaling configuration, VPC list, and deployment settings of your instance.

### **2. Firewall**

* Attach or detach a firewall to your Auto Scaling instance for enhanced security.

### **3. Load Balancers**

* Attach or detach **Load Balancers** and **Target Groups** to your Auto Scaling instance to manage traffic distribution.

### **4. Scaling Policy**

* View the list of  **scaling policies** .
* Add, update, or delete scaling policies based on CPU or RAM usage triggers.

### **5. Scaling Schedules**

* View the list of  **scaling schedules** .
* Add, update, or delete scheduled scaling actions based on predefined times or recurring schedules.

### **6. History**

* View the  **Auto Scaling history** , tracking changes, deployments, and other scaling actions.

### **7. Destroy**

* Click **Destroy** to permanently delete the Auto Scaling instance.
* This action  **cannot be undone** .

---

This guide provides everything you need to deploy and manage Auto Scaling instances effectively on Utho Cloud. 🚀
