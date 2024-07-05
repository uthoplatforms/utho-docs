---
title: "Install Docker on Windows Server"
date: "2023-06-05"
Title_meta: GUIDE TO Install Docker on Windows Server

Description: This guide offers detailed instructions on how to install Docker on Windows Server. Learn how to prepare your system, download and install Docker, configure Docker settings, and verify the installation to start leveraging containerization benefits on your Windows Server environment.

Keywords: ['Docker', 'Windows Server', 'containerization', 'install Docker', 'container management', 'server administration']

Tags: ["Docker", "Windows Server", "Containerization", "Install Docker", "Container Management", "Server Administration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/install-docker-on-windows-server']
tab: true
---

![](images/Install-Docker-on-Windows-Server-1024x576.jpg)

#### INTRODUCTION

[Docker](https://www.docker.com/) is a set of platform as a service products that use OS-level virtualization to deliver software in packages called containers. The service has both free and premium tiers. The software that hosts the containers is called Docker Engine. It was first started in 2013 and is developed by Docker, Inc. In this tutorial, we will learn how to install Docker in a Windows Server.

#### Prerequisites

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Run Server Manager and start Add roles and features, then select **Containers** feature on Select features section like follows to install. After installing, restart computer.

![](images/1-29.png)

![Install Docker on Windows Server](images/2-22.png)

![](images/3-21.png)

![Install Docker on Windows Server](images/4-18.png)

![](images/Screenshot_5-31.png)

![Install Docker on Windows Server](images/Screenshot_6-31.png)

![](images/Screenshot_7-28.png)

Install Docker on Windows Server

![Install Docker on Windows Server](images/Screenshot_8-26.png)

Step 2. After restarting, Run PowerShell with Admin Privilege and Install Docker.  
Answer Y (Yes) to all confirmations during the installation.

PS > Install-Module -Name DockerMsftProvider -Repository PSGallery -Force

![](images/Screenshot_9-22.png)

![Install Docker on Windows Server](images/Screenshot_10-14-1024x306.png)

PS > **Install-Package -Name docker -ProviderName DockerMsftProvider**

![](images/Screenshot_11-15-1024x236.png)

![Install Docker on Windows Server](images/Screenshot_12-16-1024x224.png)

![](images/Screenshot_13-12-1024x316.png)

![Install Docker on Windows Server](images/Screenshot_14-11-1024x325.png)

Thank You!
