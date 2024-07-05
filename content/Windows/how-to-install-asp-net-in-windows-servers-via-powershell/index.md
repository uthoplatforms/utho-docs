---
title: "How to install ASP.NET in Windows Servers via PowerShell"
date: "2023-06-13"
Title_meta: GUIDE TO Install ASP.NET on Windows Servers via PowerShell

Description: This guide offers detailed steps on installing ASP.NET on Windows Servers using PowerShell. Learn to leverage PowerShell commands for seamless installation, configuring ASP.NET for web application development, and optimizing server performance for .NET frameworks.

Keywords: ['ASP.NET', 'Windows Server', 'PowerShell', '.NET framework', 'web development', 'server configuration']

Tags: ["ASP.NET", "Windows Server", "PowerShell", ".NET Framework", "Web Development", "Server Configuration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-asp-net-in-windows-servers-via-powershell']
tab: true
---

![](images/How-to-install-ASP.NET-in-Windows-Servers-via-PowerShell-1024x576.jpg)

## INTRODUCTION install ASP.NET in Windows Servers

**[ASP.NET](https://dotnet.microsoft.com/en-us/apps/aspnet)** is an open-source, server-side web-application framework designed for web development to produce dynamic web pages. It was developed by Microsoft to allow programmers to build dynamic web sites, applications and services. The name stands for Active Server Pages Network Enabled Technologies. In this tutorial, we will learn how to install ASP.NET in Windows Servers via PowerShell.

#### Prerequisites

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![install ASP.NET in Windows Servers](images/Screenshot_11-23.png)

**Step 3. Run the following command to install ASP.NET 4.7**

```
Install-WindowsFeature Web-Asp-Net45
```

**Step 4. Run the following command to restart the server**

```
Restart-Computer -Force
```

![install ASP.NET in Windows Servers](images/Screenshot_3-42.png)

ASP.NET installed.

Thank You!
