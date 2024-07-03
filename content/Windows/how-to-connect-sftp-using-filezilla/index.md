---
title: "How to connect SFTP using FileZilla"
date: "2020-05-17"
Title_meta: GUIDE TO Connect SFTP Using FileZilla

Description: This guide provides step-by-step instructions on how to connect to an SFTP server using FileZilla. Learn how to configure FileZilla, enter connection details such as host, port, username, and password (or key file), and establish a secure file transfer connection to manage files effectively.

Keywords: ['SFTP', 'FileZilla', 'connect SFTP', 'secure file transfer', 'FTP client', 'server connection']

Tags: ["SFTP", "FileZilla", "Secure File Transfer", "FTP Client", "Server Connection"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-configure-ip-manually-on-windows-server/']
---

In this tutorial, we will learn how to connect a Linux server with SFTP using FileZilla client.

You need to enable SFTP or SSH access in your Microhost cloud server before logging in to [SFTP](https://utho.com/docs/tutorial/how-to-configure-sftp-server-in-debian/).

1\. Open **[FileZilla](https://filezilla-project.org/download.php)**

2\. Click on `file` tab on top in FileZilla.

![connect SFTP using Filezilla](images/Screenshot_1_again.png)

3\. After that click file open `site manager` and click on `new site`.

![connect SFTP using Filezilla](images/Screenshot_2-1.png)

4\. Open new site and set `site name`(For Eg:- site1) and change `protocol` from `FTP` to `SFTP`.

![](images/Screenshot_3-1.png)

5\. Enter your `server ip or hostname` in host field and `username and password`(root user or normal user which one you have assigned to your website folder) and click on `connect.`

![connect SFTP using Filezilla](images/Screenshot_6-1.png)

Thankyou..
