---
title: "How to Install OpenSSH on Windows Server"
date: "2022-09-26"
Title_meta: GUIDE TO Install OpenSSH on Windows Server

Description: This guide provides step-by-step instructions on how to install OpenSSH on Windows Server. Learn how to enable the OpenSSH feature, configure SSH settings, manage SSH keys, and secure remote access to your Windows Server environment using OpenSSH.

Keywords: ['OpenSSH', 'Windows Server', 'install OpenSSH', 'SSH configuration', 'remote access', 'server security']

Tags: ["OpenSSH", "Windows Server", "Install OpenSSH", "SSH Configuration", "Remote Access", "Server Security"]

icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-openssh-on-windows-server']
tab: true
---

![](images/How-to-Install-OpenSSH-on-Windows-Server_utho.jpg)

## Introduction

 This article will show you how to Install OpenSSH on [Windows Server](https://utho.com/docs/tutorial/category/windows-tutorial/), [OpenSSH](https://en.wikipedia.org/wiki/OpenSSH) is an encrypted remote access tool that protects data sent between a client and server. Therefore, it prevents common forms of network assaults like as connection hijacking, sniffing, and eavesdropping. Follow along with this tutorial to set up OpenSSH on a Windows server.

## Install OpenSSH

Through Windows Settings

Step 1. To access the Start Menu in Windows, click the Start button.

![cmd](images/1-11.png)

Step 2. Find the Settings menu and click it to open it. In the newly opened window, go to the Apps menu, and then select Optional Features from the Apps & Features drop-down menu.

Step 3. After clicking the "Add Feature" button, type "OpenSSH" into the search box to locate the feature. After that, from the list of results, choose OpenSSH Server, and then click the Install button.

![output](images/2-8.png)

Following the completion of the installation, the OpenSSH Client and Server will be added to the list of features that are installed on the system.

## Configure Firewall

Step 4. Open the Windows start menu, search for "Server Manager," and then click on it. In the window for the Server Manager, go to the Tools menu and then choose the Windows Defender Firewall with Advanced Security option from the drop-down list.

![output](images/3-8-1024x529.png)

Step 5. Now, within the Firewall window that has been opened, right click at the Inbound Rules button. After that, pick New Rule from the menu located in the right pane.

![output](images/4-7-1024x541.png)

Step 6.Choose Port from the list of available options within the New Inbound Rule Wizard.

![Install OpenSSH on Windows Server](images/5-10.png)

Step 7. Then click the Next button. Choose 'TCP' from the drop-down menu, and then type 22 into the box under 'Specific local ports:'

![output , cmd](images/6-6-1024x679.png)

Step 8. The next step is to enable the connection, assign the rule to server profiles, and set a custom name for the rule so that it can be easily identified among the list of Firewall rules.

![output](images/7-6-1024x682.png)

To save the new firewall rule, click the Finish button.

## Conclusion

I hope now you understand how to install OpenSSH on Windows Server  

Thank You 🙂
