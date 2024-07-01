---
title: "How to Configure FTP Server on Windows Server 2019"
date: "2023-03-27"
Title_meta: GUIDE TO Configure FTP Server on Windows Server 2019

Description: This guide provides step-by-step instructions on how to configure an FTP server on Windows Server 2019. Learn how to set up and manage File Transfer Protocol (FTP) services, configure security settings, and enable efficient file sharing and management on your Windows Server 2019 system.

Keywords: ['Windows Server 2019', 'FTP server', 'configure FTP', 'file transfer protocol', 'file sharing', 'server management']

Tags: ["Windows Server 2019", "FTP Server", "File Transfer Protocol", "File Sharing", "Server Management"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-configure-ftp-server-on-windows-server-2019/']
---

![Configure FTP Server on Windows](images/How-to-Configure-FTP-Server-on-Windows-Server-2019_utho.jpg)

Step 1. Log into tyour [windows server](https://www.microsoft.com/en-in/windows-server).

Step 2. Open [Server Manager](https://utho.com/docs/tutorial/how-to-install-iis-via-powershell-in-windows-server/)

![](images/Screenshot_7-15-1024x518.png)

Step 3. Go to Add Roles and features Configure FTP Server on Windows

![Configure FTP Server on Windows](images/Screenshot_3-22.png)

Select IIS and FTP server under IIS

![](images/Screenshot_1-23.png)

Click Install. IIS and FTP server will be installed.

Step 4. Go to TOOLS and open IIS

![](images/Screenshot_6-19-1024x528.png)

Step 5. Right-click on **sites** and click on **Add FTP Site...**

![Configure FTP Server on Windows](images/Screenshot_4-25-1024x527.png)

Input Site name and it's physical path Configure FTP Server on Windows

Select the server's IP address in bindings, set authentication and read/write permissions and you are good to go.

FTP folder has been set in the server.

![Configure FTP Server on Windows](images/Screenshot_6-20-1024x508.png)

step 6. Go to File manager of your local desktop and hit **ftp://server\_ip/** to access the FTP location.

![](images/Screenshot_7-16.png)

![Configure FTP Server on Windows](images/Screenshot_8-15.png)

FTP folder accessed successfully.

Thank You!
