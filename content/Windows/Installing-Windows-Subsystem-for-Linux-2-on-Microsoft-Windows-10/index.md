---
title: "Installing-Windows-Subsystem-for-Linux-2-on-Microsoft-Windows-10"
date: "2025-02-09"
Title_meta: GUIDE TO Install WSL 2 on Windows 10
Description: This guide provides step-by-step instructions to install Windows Subsystem for Linux 2 (WSL 2) on Microsoft Windows 10. Learn how to enable WSL, install a Linux distribution, configure settings, and start using Linux on your Windows system.
Keywords: ['WSL 2', 'Windows Subsystem for Linux', 'Windows 10', 'Install WSL', 'Linux on Windows', 'WSL Installation']
Tags: ["WSL 2", "Windows 10", "Linux on Windows", "Install WSL", "Subsystem for Linux", "Microsoft Windows"]
icon: "windows"
date: "2025-02-09T17:25:05+01:00"
lastmod: "2025-02-09T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/Installing-Windows-Subsystem-for-Linux-2-on-Microsoft-Windows-10']
tab: true
---

# **Installing Windows Subsystem for Linux 2 on Microsoft Windows 10**

![](./images/image2.png)

## **What is the Windows Subsystem for Linux?**

The Windows Subsystem for Linux (WSL) lets developers run a Linux
environment directly on Windows. It's a full Linux OS running inside
Windows so you can use the same apps and files seamlessly. In 2021
Microsoft introduced Windows Subsystem for Linux (short: WSL). With that
you can run a Linux environment directly on your Windows machine without
the need for tools like VirtualBox or dual-boot setups. You can just
start a Linux session of your choice directly.

We will guide you through the process of installing and enabling wsl on
your local machine and installing ubuntu 22.04.

## **Prerequisites**

To follow this guide, make sure that your PC has Windows 10 Version 1903
(or later) with Build 18362 (or later).

### **Enabling WSL on Your Machine:**

Open the Start menu by pressing the Windows-Key and search for
"PowerShell". Now right click on Windows PowerShell and click on "Run as
administrator".

![](./images/image3.png)

execute the following commands in a Windows Powershell terminal run as
an Administrator:
```bash
dism.exe /online /enable-feature
/featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```

In order to run the latest version of WSL, which is WSL 2, you need to
enable "Windows Virtual Machine Platform"
```bash
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform
/all /norestart
```

For all the new changes and enabled features to take effect you need to
restart your PC.

To install the default Ubuntu 20.04 distro, enter:
```bash
wsl --install
```
To install a specific distro by name, such as Debian, enter:
```bash
wsl --install -d Debian
```
![](./images/image1.png)

Alternatively, you can install Linux distros from the Microsoft Store
accessed in the Start menu. Enter "Linux" in the search box (be wary
there could be software other than WSL distros).

![](./images/image4.png)

After choosing a username you will be prompted to create a password for
this account.

Congrats! Now your Ubuntu instance is set up and fully configured.
