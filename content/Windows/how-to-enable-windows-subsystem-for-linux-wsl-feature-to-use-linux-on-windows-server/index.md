---
title: "How to enable Windows Subsystem for Linux (WSL) feature to use Linux on Windows Server"
date: "2023-06-13"
Title_meta: GUIDE TO Enable Windows Subsystem for Linux (WSL) Feature on Windows Server

Description: This guide provides step-by-step instructions on how to enable the Windows Subsystem for Linux (WSL) feature on Windows Server. Learn how to install and configure WSL to run Linux distributions alongside Windows, enabling developers and administrators to utilize Linux tools and workflows on their Windows Server environments.

Keywords: ['Windows Subsystem for Linux', 'WSL', 'Windows Server', 'Linux on Windows', 'developer tools', 'server administration']

Tags: ["Windows Subsystem for Linux", "WSL", "Windows Server", "Linux on Windows", "Developer Tools", "Server Administration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-enable-windows-subsystem-for-linux-wsl-feature-to-use-linux-on-windows-server/']
tab: true
---

![](images/How-to-enable-Windows-Subsystem-for-Linux-WSL-feature-to-use-Linux-on-Windows-Server-1024x576.jpg)

## INTRODUCTION

**Windows Subsystem for Linux** (**[WSL](https://learn.microsoft.com/en-us/windows/wsl/install)**) is a feature of Windows that allows developers to run a Linux environment without the need for a separate virtual machine or dual booting. There are two versions of WSL: WSL 1 and WSL 2. WSL 1 was first released on August 2, 2016, and acts as a compatibility layer for running Linux binary executables (in ELF format) by implementing Linux system calls on the Windows kernel. It is available on Windows Server 2016, 2019 and 2022. In this tutorial, we will learn how to enable Windows Subsystem for Linux (WSL) feature to use Linux on Windows Server 2016, 2019 and 2022.

### Prerequisites

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![](images/Screenshot_11-19.png)

Step 3. Run the following command to enable Windows Subsystem for Linux

```
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

![enable Windows Subsystem for Linux](images/Screenshot_1-40.png)

![](images/Screenshot_2-50-1024x253.png)

![enable Windows Subsystem for Linux](images/Screenshot_3-39.png)

Step 4. In this tutorial, we will be creating the subsystem for Ubuntu.

Run the following command to download Ubuntu 18.04

```
Invoke-WebRequest -Uri "https://aka.ms/wsl-ubuntu-1804" -OutFile "ubuntu-1804.zip"
Expand-Archive ubuntu-1804.zip
cd ubuntu-1804
.\ubuntu1804.exe
```

![](images/Screenshot_4-39-1024x227.png)

![enable Windows Subsystem for Linux](images/Screenshot_6-32-1024x202.png)

![](images/Screenshot_7-29.png)

![enable Windows Subsystem for Linux](images/Screenshot_8-27.png)

**Step 5. Enter new UNIX username: (to create a new user)**

Enter the password for the user.

![](images/Screenshot_9-24.png)

Installation successful.

**Step 6. Windows resources are mounted on \[/mnt/c\] which can be accessed by**

```
df -h
```

![enable Windows Subsystem for Linux](images/Screenshot_10-16.png)

```
ls -l /mnt/c
```

![](images/Screenshot_11-18.png)

Step 7. To access Linux resources with root priviledge, us **sudo** command

![enable Windows Subsystem for Linux](images/Screenshot_12-18.png)

Thank You!
