---
title: "How to Install MultiPoint Services in Windows Server 2016"
date: "2022-06-22"
Title_meta: GUIDE TO Install MultiPoint Services in Windows Server 2016

Description: This guide provides detailed instructions on how to install MultiPoint Services in Windows Server 2016. Learn how to enable and configure MultiPoint Services to deliver multiple concurrent user sessions from a single server, facilitating efficient desktop virtualization and remote access management.

Keywords: ['MultiPoint Services', 'Windows Server 2016', 'install MultiPoint Services', 'desktop virtualization', 'remote access management', 'server configuration']

Tags: ["MultiPoint Services", "Windows Server 2016", "Install MultiPoint Services", "Desktop Virtualization", "Remote Access Management", "Server Configuration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-multipoint-services-in-windows-server-2016']
tab: true
---

![](images/How-to-Install-MultiPoint-Services-in-Windows-Server-2016_utho.jpg)

The MultiPoint Services (MPS) role replaces the well-known Windows MultiPoint Server 2012. This role enables multiple users to share simultaneously one single computer. In turn, each user has its own independent Windows experience..

Let get started.

**Step 1** – Open server manager dashboard, **“Add roles and features”** **Click Next,** Choose **“Role-based or feature-based installation”** radio button and **click Next, Scroll down and choose** select **MultiPoint Services.** You may notice there are some additional features are required for MPS such as **File And Storage Services, Print and Document Services and etc. Just click on “Add features”** and **click Next. (refer picture).**

![](images/3-1-1024x575.png)

![](images/4-1024x575.png)

![](images/5-2-1024x575.png)

![](images/6-1-1024x575.png)

![](images/73-1024x575.png)

![](images/83-1024x575.png)

![](images/93-1024x575.png)

![](images/103-1024x575.png)

**Step** **2** – Read a great explanation from Microsoft **“what is MPS?”**.

Remote Desktop Licensing **needs to be activated or use trial period** (120 days).

![](images/114-1024x575.png)

**Step** **3** – I leave **default settings** and click Next. Read and **click next.**

- **Print Server** is needed to manage multiple printers

- **Distributed Scan Server** – enables you to manage and share networks scanners that support Distributed Scan Management

- **Internet Printing** creates a web site where users can manage printer jobs on the server .  
    If you have installed Internet Printing client on stations you can connect and print to shared printers using Web Browser and Internet Printing Protocol

- **LPD service** – Line Printer Daemon Service enables UNIX-based computers using the Line Printer Remote service to print to shared printers on MPS

![](images/123-1-1024x575.png)

![](images/132-1024x575.png)

**Step** **4** – I leave **default settings** and click Next. Read and **click next.**

- **RD Gateway** – to publish RDS (not suitable for MPS)

- **RD Connection Broker** – to distribute connections  (not suitable for MPS)

- **RD Virtualization Host** – for VDI

- **RD Web Access** – web access to RD session/vdi/remoteapp collections (not suitable for MPS)

![](images/143-1024x575.png)

![](images/153-1024x575.png)

**Step** 5 – **Click at Restart** the destination server automatically if required checkbox to restart your server.

![](images/163-1024x575.png)

![](images/173-1024x575.png)

**Step** **6**: – When installation progress reaches the end click the **Close** button to close the Add Roles and Features Wizard. **The server restart is required.**

![](images/183-1024x575.png)

![](images/204-1024x463.png)

**Step** **7** – Press Start button and open **MultiPoint Manager.**

![](images/215-1024x575.png)

**Step** **8** – **Add MultiPoint Servers** or **personal computers** (optional)

![](images/224-1024x575.png)

**Step** **9** – Go to Users tab and click “**Add user account**”, click Next and select user type. **(refer picture).**

![](images/253-1024x575.png)

![](images/244-1024x575.png)

![](images/263-1024x575.png)

**Step** **10** – Testing, Connect to **MultiPoint** **Server** from the user connection using **RDP**.

When user firstly log on to **MultiPoint** **Services** he receives **privacy notification** “To assist you with your usage of this computer, your activities may be monitored by your system administrator”

Click on **“Accept and continue using this computer”** and **go back to MPS server. (refer picture).**

![](images/43.jpg)

![](images/284-1024x575.png)

![](images/302-1024x575.png)

![](images/274-1024x575.png)

![](images/313-1024x575.png)

**Step** **11** – On MPS server run **MultiPoint Dashboard**. All screens from user stations are being added and updated to dashboard.

You can see what happens on user’s station, block this desktop, set message for blocked users, take control, write IM to user, block USB storage or limit web access on selected desktops.

![](images/332-1024x575.png)

**Step** **12** – You can also **project your desktop to all or selected user desktops**.

It’s really needed when trainer or teacher does not have projector so he or she shares screen to all user’s station.

If you are familiar with **Lync / Skype** there is a similar feature called as **“desktop sharing” (refer picture).**

![](images/342-1-1024x575.png)

![](images/352-1024x575.png)

**Step** **13** – If you open MultiPoint Manager you can notice that list of stations has been updated with rlevchenko’s station.

![](images/362-1024x575.png)

![](images/372-1024x575.png)

Thank you :)
