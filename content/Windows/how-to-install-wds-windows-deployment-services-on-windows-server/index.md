---
title: "How to install WDS (Windows Deployment Services) on Windows Server"
date: "2023-06-13"
Title_meta: GUIDE TO Install WDS (Windows Deployment Services) on Windows Server

Description: This guide provides detailed instructions on how to install Windows Deployment Services (WDS) on Windows Server. Learn how to enable the WDS role, configure deployment settings, create and manage boot images, and streamline operating system installations across your network using WDS.

Keywords: ['WDS', 'Windows Deployment Services', 'Windows Server', 'install WDS', 'deployment tools', 'network administration']

Tags: ["WDS", "Windows Deployment Services", "Windows Server", "Install WDS", "Deployment Tools", "Network Administration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-wds-windows-deployment-services-on-windows-server']
tab: true
---

![](images/How-to-install-WDS-Windows-Deployment-Services-on-Windows-Server-1024x576.jpg)

### INTRODUCTION

**Windows Deployment Services** (**WDS**) is a deprecated component of the [Windows](https://en.wikipedia.org/wiki/Windows_Server) operating system that enables centralized, network-based deploy of operating systems to bare-metal computers. It is the successor to Remote Installation Services (RIS). WDS officially supports remote deployment of Windows Vista and later, as well as Windows Server 2008 and later. However, because WDS uses disk imaging, in particular the Windows Imaging Format (WIM), it could deploy virtually any operating system. This is in contrast with its predecessor, RIS, which was a method of automating the installation process.

#### Prerequisites Windows Deployment Services on Windows

- Windows Server

- Internet connectivity

Run [Server Manager](https://utho.com/docs/tutorial/how-to-share-a-folder-over-network-using-server-manager-in-windows-servers/) and Click **Add roles and features**.

![Windows Deployment Services on Windows](images/1-28.png)

Click **Next** button.

![](images/2-21.png)

Select **Role-based or feature-based installation**.

![Windows Deployment Services on Windows](images/3-20.png)

Select a Host which you'd like to add services.

![](images/4-17.png)

Check a box \[Windows Deployment Services\].

![Windows Deployment Services on Windows](images/5-18.png)

Additional features are required to add WDS. Click \[Add Features\] button and then Click \[Next\] button.

![](images/6-15.png)

Click **Next** button.

![Windows Deployment Services on Windows](images/7-13.png)

Click **Next** button.

![](images/8-11.png)

Check boxes you'd like to install role services.

![Windows Deployment Services on Windows](images/9-11.png)

Click **Install** button.

![](images/10-12.png)

After finishing Installation, click \[Close\] button.

![Windows Deployment Services on Windows](images/11-6.png)

Thank You!
