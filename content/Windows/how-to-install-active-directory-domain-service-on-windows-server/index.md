---
title: "How to install Active Directory Domain Service on Windows Server"
date: "2023-05-08"
Title_meta: GUIDE TO Install Active Directory Domain Services on Windows Server

Description: This guide provides detailed instructions on how to install Active Directory Domain Services (AD DS) on Windows Server. Learn how to set up a domain controller, promote the server to a domain controller role, and configure AD DS to manage and authenticate users, computers, and resources within your network environment.

Keywords: ['Active Directory Domain Services', 'Windows Server', 'install AD DS', 'domain controller', 'server administration', 'network management']

Tags: ["Active Directory Domain Services", "Windows Server", "Install AD DS", "Domain Controller", "Server Administration", "Network Management"]

icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-active-directory-domain-service-on-windows-server/']
tab: true
---

![](images/How-to-install-Active-Directory-Domain-Service-on-Windows-Server-1024x576.jpg)

#### INTRODUCTION

[Active Directory Domain Services](https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) (AD DS) is a server role in Active Directory that enables administrators to control and store data from applications as well as network resource information in a distributed database. Administrators can control network components, including people and computing devices, and reorder them into a certain hierarchical structure with the use of AD DS. Incorporating security further is AD DS by authenticating logons and restricting access to directory resources. In this tutorial, we will learn how to install Active Directory Domain Service on [Windows Servers](https://utho.com/docs/tutorial/how-to-connect-to-a-windows-server-using-remote-desktop-protocol-rdp/) 2012R2, 2016, 2019 and 2022.

To install Active Directory Domain Service via PowerShell, open PowerShell with Admin Privileges, and run the following command.

PS C:\\Users\\Administrator> Install-WindowsFeature -name AD-Domain-Services -IncludeManagementTools

Now, run the following command to restart the server to apply changes.

PS C:\\Users\\Administrator> Restart-Computer -Force

To install Active Directory Domain Service via Server Manager

**Step 1.** Open **Server Manager** and click **Add roles and features**.

![](images/1-27.png)

**Step 2.** Click **Next**

![install Active Directory Domain ](images/2-20.png)

**Step 3.** Select **Role-based or feature-based installation**.

![](images/3-19.png)

**Step 4.** Select a Host which you'd like to add services.

![install Active Directory Domain ](images/4-16.png)

**Step 5.** Check a box Active Directory Domain Services.

![](images/5-17.png)

**Step 6.** Addtional features are required to add AD DS. Click **Add Features** button.

![install Active Directory Domain ](images/6-14.png)

**Step 7.** Click **Next**.

![](images/7-12.png)

**Step 8.** Click **Next**.

![install Active Directory Domain ](images/8-10.png)

**Step 9.** Click **Install**.

![](images/9-10.png)

**Step 10.** After finishing Installation, click **Close** button.

![install Active Directory Domain ](images/10-11.png)

**Thank You!**

<table><tbody><tr><td></td></tr></tbody></table>
