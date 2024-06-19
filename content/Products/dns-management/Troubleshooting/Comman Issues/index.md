---
weight: 10
title: "Comman Issues"
date: "2022-08-25"
---

# Databases (Maria DB)
--- 
Databases in cloud servers refer to the storage and management of data within cloud computing environments. Traditionally, databases were hosted on physical servers maintained by organizations themselves. However, with the advent of cloud computing, databases can now be hosted on virtual servers provided by cloud service providers.

### Databases offer several benefits:
- Scalability
- Cost-effectiveness
- Accessibility
- Reliability and redundancy
- Security

### Steps for approaching the Databases:
---
#### Visit on the link given below:
>
[Console url](https://console.utho.com/)
1. This link will redirect you to the Dashboard after Login of the cloud platform. 
![Dashboard](../Screenshots/Dashboard.png)
<br />

2.  Here we will get 2 options to reach the Databases tab.
- Deploy new (Dropdown)
- L.H.S tab
![DB_Process_1](../Screenshots/DB_process_1.png)
<br />

3. After clicking it will redirect  you to the database homepage.
![DB_Process_2](../Screenshots/DB_Process_2.png)
<br />

4. Now for creating a new database cluster click on create  cluster or create database cluster.
![DB_Process_3](../Screenshots/DB_Process_3.png)
<br />

5. It will  redirect you to the requirements and create cluster page.
    Where the user have to choose the data accordings to there requirements such as:
![DB_Process_4](../Screenshots/DB_Process_4.png)
<br />

6. Now from  Select DC location , select one required location.
![DB_Process_5](../Screenshots/DB_Process_5.png)
<br />

7. Now moving forword we have to Select Database Cluster with there version which is required.
![DB_Process_6](../Screenshots/DB_Process_6.png)
<br />

8. In this user have to select which mode of billing cycle he/she prefer from the given options:
- Hourly
- Monthly  
- Yearly

![DB_Process_7](../Screenshots/DB_Process_7.png)
<br />

9. Now from the Select Plan Type , choose the required plan from the given options:
- Basic Plan
- CPU Optimized
- Memory Optimized

![DB_Process_8](../Screenshots/DB_Process_8.png)
<br />

10. If the user needs Set Number of Replica of his cluster , so that it can keep his website up even when his main wesite is facing issue. From here he/she can select maximum upto 3 Replica.
![DB_Process_9](../Screenshots/DB_Process_9.png)
<br />

11. VPC Network allows users to create and manage isolated networks within the cloud infrastructure. VPC networks enable users to deploy resources such as virtual machines (VMs), databases, and other cloud services securely and privately.
![DB_Process_10](../Screenshots/DB_Process_10.png)
<br />

12. In Add security group , user can create and select the firewalls for the server.
![DB_Process_11](../Screenshots/DB_Process_11.png)
<br />

13. User can name his cluster as per his perception in Name Your Cluster tab.
![DB_Process_12](../Screenshots/DB_Process_12.png)
<br />

14. After filling all the details then on clicking on the create cluster button.
![DB_Process_13](../Screenshots/DB_Process_13.png)
<br />

15. A new db cluster will be created and you will be automatically redirected to the homepage of database.
![DB_Process_14](../Screenshots/DB_Process_14.png)
<br />

After the cluster is created , its internal functionalities will be seen after clicking on the Manage Button as shown in the below snippet.

![Mariadb_Process](../Screenshots/Mariadb_Manage.png)

After clicking on the manage a new screen will occur.
![Mariadb_Process_01](../Screenshots/MYSQL_Process_01.png)
<br />

##### Which contains various functionalities of Database stated below:

After clicking on manage button from dashboard it will redirect to this page where user will get all the points mentioned below for in depth understanding of the product. 
![Mariadb_Process_01](../Screenshots/Mariadb_Process_01.png)

1. **Connection Details**
   In cloud computing, "Connection Details" typically refer to the information required to establish connections between different components or services within a cloud environment.
   These details can include various parameters such as:    

    Endpoint Addresses
    Port Numbers
    Protocol
    Authentication Credentials
    Security Settings
    Connection Timeout Settings
![Connectial_Details](../Screenshots/Connection_Details.png)  

2. **Nodes**
    With the help of Nodes we can add additional replica of our cluster in (postgre) anf for others here we can see and copy our IP's either public or private. For both read only and edit too.
![Nodes_Process](../Screenshots/Nodes_Process.png)
3. **Databases**
    In databases user can create database and manage the permissions he want to provide to the reated database.
    Also user have the access to delete his created databases.
On clciking on add database user can create a new database for him.
![Database_Process](../Screenshots/Database_Process.png)
 After clicking on add database a new window will occur as shown in the snippet.
![Database_Process_01](../Screenshots/Database_Process_01.png)
And once the name if the database is filled & user clicked on add database a new database will be created as shown in the snippet.
![Database_Process_02](../Screenshots/Database_Process_02.png)

On clicking on Manage permissionn a side bar will occur where have to choose the database user and types of permission to give.
After clicking on add permission the permission will be allocated to the database user.
Also by cliciking on update you can update the access for database user as shown in the given snippet.
    ![Database Process_03](../Screenshots/Database_Process_03.png)
4. **Users**
    In this section user can create or add the user who can access that database for the work.
    Also owner have the access to delete his created user form databases by clicking on the delete button.
On clicking on Add database user a side screen will occur where the you can create the users for your database as shown in the snippet below (2). 
After entering the name you have to click on add database user, then user will be added.
![Users_Process](../Screenshots/Users_Process.png)
![Users_Process](../Screenshots/Users_Process_01.png)
    
6. **Backups**
    Backups are an essential aspect of maintaining data integrity and availability in cloud server environments. Cloud providers offer various backup solutions and features to help users protect their data from loss or corruption. User can add as many backups he want.
    With the help of backups user can save his cluster from any mishappening.
On clicking on add new backup a backup  will be created for the db.
![Backup's_Process](../Screenshots/Backup's_Process.png)
7. **Firewall**
    Firewalls in the cloud serve the same fundamental purpose as traditional network firewalls: to monitor and control incoming and outgoing network traffic based on predetermined security rules. However, in a cloud environment, firewalls are implemented and managed differently due to the distributed and dynamic nature of cloud computing.
    Here user can also add the firewall he needed.
Here user can also add the firewall he needed.
On clicking on add firewall a new sidebar will open where we have to choose the firewall created.
Also on clicking on detach we can remove the firewall if not needed.
![Firewall_Process](../Screenshots/Firewall_Process.png)
8. **Trusted Hosts**
    In cloud computing, "trusted hosts" typically refer to entities, such as servers, virtual machines, or services, that are deemed trustworthy within a cloud environment. These trusted hosts are often granted certain privileges or permissions based on their established trustworthiness.
    In this user can add the trusted IP address from where the server can get accessed.
On clicking on Add IP user can add the new trusted ip address.
Onclicking on detach user can remove that IP address.
![Trustedhost_Process](../Screenshots/Trustedhost_Process.png)
9. **Destroy**
     In destroy the user can destroy/delete his cluster permanently , which cannot be accessed again.
Here , if the user didn't need any of the cluster then user can destroy that cluster by clicking on destroy cluster button .
After clicking on destroy button user will get a pop for the confirmation & warning.
**NOTE** = Once the cluster is destroyed then in any case it will be not reterived.
![Destroy_Process](../Screenshots/Destroy_Process.png)
---
**THE END**
