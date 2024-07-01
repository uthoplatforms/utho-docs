---
title: "How to install WDS (Windows Deployment Services) via PowerShell"
date: "2023-06-13"
Title_meta: GUIDE TO Install WDS (Windows Deployment Services) via PowerShell

Description: This guide provides step-by-step instructions on how to install Windows Deployment Services (WDS) on Windows Server using PowerShell. Learn how to automate the deployment of WDS, configure deployment settings, manage boot images, and streamline operating system installations across your network environment.

Keywords: ['WDS', 'Windows Deployment Services', 'PowerShell', 'install WDS', 'deployment automation', 'network administration']

Tags: ["WDS", "Windows Deployment Services", "PowerShell", "Install WDS", "Deployment Automation", "Network Administration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-wds-windows-deployment-services-via-powershell']
tab: true
---

![](images/How-to-install-WDS-Windows-Deployment-Services-via-PowerShell-1024x576.jpg)

### INTRODUCTION

**Windows Deployment Services** (**WDS**) is a deprecated component of the [Windows](https://en.wikipedia.org/wiki/Windows_Server) operating system that enables centralized, network-based deploy of operating systems to bare-metal computers. It is the successor to Remote Installation Services (RIS). WDS officially supports remote deployment of Windows Vista and later, as well as Windows Server 2008 and later. However, because WDS uses disk imaging, in particular the Windows Imaging Format (WIM), it could deploy virtually any operating system. This is in contrast with its predecessor, RIS, which was a method of automating the installation process. We can also install WDS via Server Manager. But in this tutorial, we will learn how to install WDS (Windows Deployment Services) on Windows Server 2012, 2016 and 2019.

#### Prerequisites Windows Deployment Services on Windows

- Windows Server

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![Windows Deployment Services on Windows](images/Screenshot_11-13.png)

Step 3. Run the following command to install WDS

![](images/Screenshot_12-15-1024x526.png)

Step 4. Installation begins

![Windows Deployment Services on Windows](images/Screenshot_13-11-1024x524.png)

Step 5. Installation completed. As the command suggests a system restart s not needed.

![Windows Deployment Services on Windows](images/Screenshot_14-10.png)

Thank You!
