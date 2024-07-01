---
title: "How to change RDP port in Windows Server"
date: "2022-06-22"
Title_meta: GUIDE TO Change RDP Port in Windows Server

Description: This guide provides instructions on how to change the Remote Desktop Protocol (RDP) port in Windows Server. Follow step-by-step procedures to modify the RDP port number, enhancing security by configuring a custom port for remote desktop access on your Windows Server.

Keywords: ['Windows Server', 'change RDP port', 'Remote Desktop Protocol', 'RDP security', 'server configuration']

Tags: ["Windows Server", "Change RDP Port", "Remote Desktop Protocol", "RDP Security", "Server Configuration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-change-rdp-port-in-windows-support-internal/']
---

![](images/How-to-change-RDP-port-in-Windows-Support-Internal-1024x576.png)

**Step1 :: Need to connect windows server with Administrator privilege, then type below command in search box “regedit”**

![](images/pasted-image-0.png)

Step2 :: You need to follow the below path to change the RDP Port in the Windows system.

HKEY\_LOCAL\_MACHINE\\System\\CurrentControlSet\\Control\\Terminal Server\\WinStations\\RDP-Tcp

![](images/pasted-image-0-1.png)

**Step3 ::After following above path steps, need to right click on on port number then provide the wanted port number for connection.**

![](images/pasted-image-0-2.png)

**Step4 :: Need to follow below steps to change the port number in registry.**

![](images/pasted-image-0-3.png)

![](images/pasted-image-0-4.png)

Now give your desired port number to connect to the windows server…

![](images/pasted-image-0-5.png)

**Step5 :: Now I need to exit the registry then reboot the server to see the changes on it.**

**Step6 :: Now we have to check the server connection with the new RDP port.**

![](images/pasted-image-0-6.png)

Thanks :)
