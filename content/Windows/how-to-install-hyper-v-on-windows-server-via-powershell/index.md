---
title: "How to install Hyper-V on Windows Server via PowerShell"
date: "2023-06-13"
Title_meta: GUIDE TO Install Hyper-V on Windows Server via PowerShell

Description: This guide provides step-by-step instructions on how to install Hyper-V on Windows Server using PowerShell. Learn how to enable the Hyper-V role, configure virtualization settings, create and manage virtual machines (VMs), and optimize server performance for virtualization tasks.

Keywords: ['Hyper-V', 'Windows Server', 'PowerShell', 'install Hyper-V', 'virtualization', 'server management']

Tags: ["Hyper-V", "Windows Server", "PowerShell", "Install Hyper-V", "Virtualization", "Server Management"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-hyper-v-on-windows-server-via-powershell']
tab: true
---

![](images/How-to-install-Hyper-V-on-Windows-Server-via-PowerShell-1024x576.jpg)

## INTRODUCTION install Hyper-V on Windows

Microsoft Hyper-V, codenamed Viridian, and briefly known before its release as Windows Server Virtualization, is a native hypervisor; it can create virtual machines on x86-64 systems running Windows. Starting with Windows 8, Hyper-V superseded Windows Virtual PC as the hardware virtualization component of the client editions of Windows NT. A server computer running Hyper-V can be configured to expose individual virtual machines to one or more networks. Hyper-V was first released with Windows Server 2008, and has been available without additional charge since Windows Server 2012 and Windows 8. A standalone Windows Hyper-V Server is free, but has a command-line interface only. The last version of free Hyper-V Server is Hyper-V Server 2019, which is based on Windows Server 2019. In this tutorial, we will earn, how to install Hyper-V on Windows Server.

#### Prerequisites

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![install Hyper-V on Windows](images/Screenshot_11-21.png)

**Step 3. Run the following command to install [Hyper-V](https://en.wikipedia.org/wiki/Hyper-V) with Windows admin tools**

```
Install-WindowsFeature Hyper-V -IncludeManagementTools
```

![install Hyper-V on Windows](images/Screenshot_1-42.png)

![](images/Screenshot_2-52.png)

**Step 4. Run the following command to restart the server after installing Containers**

```
Restart-Computer -Force
```

![install Hyper-V on Windows](images/Screenshot_3-41.png)

Thank You!
