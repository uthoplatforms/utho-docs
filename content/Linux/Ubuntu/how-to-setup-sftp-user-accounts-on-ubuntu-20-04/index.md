---
title: "How to Setup SFTP User Account on Ubuntu 20.04"
date: "2022-10-09"
title_meta: "Setup SFTP User Account on Ubuntu 22.04"
description: "Learn how to set up an SFTP user account on Ubuntu 20.04 with this detailed guide. Follow step-by-step instructions to create and configure a secure SFTP account, allowing file transfers over a secure SSH connection on your Ubuntu system.
"
keywords: ["set up SFTP user Ubuntu 20.04", "create SFTP account Ubuntu 20.04", "configure SFTP user Ubuntu 20.04", "Ubuntu 20.04 SFTP setup", "SFTP user configuration Ubuntu 20.04", "install SFTP server Ubuntu 20.04", "Ubuntu 20.04 SFTP guide", "secure SFTP Ubuntu 20.04"]

tags: ["SFTP", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/how-to-setup-sftp-user-accounts-on-ubuntu-20-04/']
tab: true
---

![How to Setup SFTP User Account on Ubuntu 20.04](images/How-to-Setup-SFTP-User-Account-on-Ubuntu-20.04_utho.jpg)

## Introduction

In this article you will know about to Setup [SFTP](https://www.techopedia.com/definition/1879/secure-file-transfer-protocol-sftp) User Account on Ubuntu 20.04.

A secure shell (SSH) session is required for the use of the Secure File Transfer Protocol (SFTP), which is a secure method for exchanging data between a local and distant computer. It is an upgraded version of the standard file transfer protocol (FTP), which adds an additional layer of protection during the process of transferring files and establishing a connection.

In this tutorial, you will learn how to set up SFTP User accounts on Ubuntu 20.04 and enable the user to only access files that are located within the user's home directory.

## Setup SFTP

Make a new group for SFTP users and join it. You should provide the name of the group you want instead of sftpcorner.

```
# addgroup sftpcorner
```

Make a brand-new account for the user. Simply replace "exampleuser" with the user name you want to use.

```
# adduser exampleuser
```

To continue, kindly enter the full name of the user and their password.

After that, the user should be added to the SFTP group.

```
# usermod -G sftpcorner exampleuser
```

Put access restrictions on the user so they can't access files outside of their home directory.

```
# chown root:root /home/exampleuser
```

Now, within the user's home directory, create new subdirectories. These are used in the process of file transfer.

```
# mkdir /home/exampleuser/uploads
```

Give the user ownership of the subdirectories and files in the directory.

```
# chown -R exampleuser:exampleuser /home/exampleuser/uploads
```

The next step is to give the user permission to read and write all of the files contained within their home directory.

```
# chmod -R 755 /home/exampleuser/
```

## Configure SFTP

After creating the user accounts and the sftp group, you will need to enable SFTP in the primary SSH configuration file.

Using an editor of your choice, open the file `/etc/ssh/sshd_config`.

```
# vi /etc/ssh/sshd_config
```

Add the following lines to the end of the file. Replace sftpcorner with your actual sftp group.

```
Match Group sftpcorner
ChrootDirectory %h
PasswordAuthentication yes
AllowTcpForwarding no
X11Forwarding no
ForceCommand internal-sftp
```

Save and close the file by escape :wq

Below are the functions for each of the above configuration lines:

1\. **Match Group sftpcorner:** Match the user group `sftpcorner`.

2\. **ChrootDirectory %h:** Restrict access to directories within the user's home directory.

3\. **PasswordAuthentication yes:** Enable password authentication.

4\. **AllowTcpForwarding no:** Disable TCP forwarding.

5\. **X11Forwarding no:** Don't permit Graphical displays.

6\. **ForceCommand internal-sftp:** Enable SFTP only with no shell access.

Also, confirm if SFTP is enabled (it is by default). The line below should be uncommented in `/etc/ssh/sshd_config`:

```
override default of no subsystems
```Subsystem sftp  /usr/lib/openssh/sftp-server
```

Restart the SSH server for changes to take effect.

```
# systemctl restart sshd
```

## Login to SFTP

Open a new terminal window and log in with `sftp` using a valid user account and password.

```
# sftp exampleuser@Server_IP
```

List files within the directory. Your output should be similar to the one below:

exampleuser@Server\_IP's password:

Connected to server\_IP  
```
# ls
```

Also, try creating a new directory within the subdirectory to test user permissions.

```
# cd uploads
```

```
# mkdir files
```

Confirm creation of the new directory:

```
# ls
```

![command output](images/image-321.png)

FileZilla and Cyberduck are the most popular SFTP clients available for Windows, Mac, and [Linux](https://utho.com/docs/tutorial/category/linux-tutorial/) desktop to test connectivity using a desktop client.

## Conclusion

In this guide, you successfully setup SFTP user account on Ubuntu 20.04 server, then tested connectivity through a terminal session and FileZilla. You can create multiple users with different directories to securely upload and download files on your server.

Thank You 🙂
