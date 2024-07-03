---
title: "How to create RDP user in Windows Server 2012"
date: "2020-06-02"
Description: Learn how to create an RDP (Remote Desktop Protocol) user in Windows Server 2012 with this step-by-step guide. Follow instructions to add a user account, configure RDP permissions, and enable remote desktop access, facilitating secure remote management and administration of your server.

Keywords: ['Windows Server 2012', 'RDP user', 'create user account', 'remote desktop access', 'server administration']

Tags: ["Windows Server 2012", "RDP User", "Create User Account", "Remote Desktop Access", "Server Administration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-create-rdp-user-in-windows-server-2012/']
---

Follow the steps below to create additional RDP users to connect to your Windows Microhost cloud server.

1\. Access your server using RDP with administrator.

![](images/5-1.png)

2\. Click on **Server Manager** and open.

![](images/Screenshot-3-1024x619.png)

3\. In server Manager click on **Tools** and expand tools and open **Computer Management**.

![](images/Screenshot1.png)

4\. Click On **users and groups**.

![](images/Screenshot2-1.png)

5\. Right Click on **Users and add new user**. Check option "**User must change password at next login**" if you want to change user password at first login.

![](images/Screenshot3-1.png)

6\. Enter user name ,Full name and password.

![](images/Screenshot4-1.png)

7\. Now you need to go in user **properties** for assigned RDP group for access RDP.

![](images/Screenshot5-1.png)

8\. Click on add button.

![](images/Screenshot6.png)

9\. Now click on **Advanced** tab for search group.

![](images/Screenshot7.png)

10\. Click on **find** button.

![](images/Screenshot8-1.png)

11\. Here you need to select group "**Remote Desktop Users**" and click ok.

![](images/Screenshot9-again.png)

12\. Now the search window will close and click on **ok.**

![](images/Screenshot10.png)

13\. Again you need to open **Server Manager** and click on **Services**.

![](images/Screenshot11-1-1024x559.png)

14\. Click on **remote desktop Services** and restart the services using restart option.

Now try to access Remote Desktop with created new RDP user.

Thank You..
