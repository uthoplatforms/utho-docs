---
title: "How To Configure SFTP Server In Debian"
date: "2022-10-09"
---

![How To Configure SFTP Server In Debian](images/How-To-Configure-SFTP-Server-In-DEBIAN_utho.jpg)

## Introduction

In this article you will know about To Configure SFTP Server In Debian, A secure shell ([SSH](https://en.wikipedia.org/wiki/Secure_Shell)) session is required for the use of the Secure File Transfer Protocol (SFTP), which is a secure method for exchanging data between a local and distant computer. It is an upgraded version of the standard file transfer protocol (FTP), which adds an additional layer of protection during the process of transferring files and establishing a connection.

In this tutorial, you will learn how to set up SFTP User accounts on [Debian](https://utho.com/docs/tutorial/how-to-install-php-7-4-on-debian-10/) and enable the user to only access files that are located within the user's home directory.

## Configure SFTP Server In DEBIAN

Executing the below command confirms that OpenSSH Server was successfully installed.

```
# apt list openssh-server -a
```

![Configure SFTP Server In Debian](images/image-323.png)

Change the following in **/etc/ssh/sshd\_config**

In order to stop the sftp server, remove the comment from /usr/lib/openssh/sftp-server.

The configuration term "Add Subsystem sftp internal-sftp" allows sshd to use its own SFTP server code rather than starting a separate process (what would typically be the sftp-server).

Users in the sftp users group are allowed to using SFTP and not SSH.

Specify the SFTP root directory with the command ChrootDirectory /SFTP.

```
# vi /etc/ssh/sshd_config
```

The line below should be uncommented in `/etc/ssh/sshd_config`:

```
#Subsystem      sftp    /usr/lib/openssh/sftp-server
Subsystem       sftp    internal-sftp
```

Add the following lines to the end of the file.

```
Match group sftp_users
        X11Forwarding no
        AllowTcpForwarding no
        ChrootDirectory /SFTP
        ForceCommand internal-sftp
```

Save and close the file by escape :wq

## Restart SSH Service

```
# systemctl restart sshd
```

## Create Users & Group For SFTP

Create a new group called sftp\_cloud and new user called microhost

```
# groupadd sftp_cloud
```

```
# adduser microhost
```

Add user in **sftp\_**cloud group

```
# usermod -G sftp_cloud microhost
```

Create **/SFTP** folder and a sub folder **/SFTP/**microhost for user to upload via SFTP

```
# mkdir /SFTP
```

```
# mkdir /SFTP/microhost
```

```
# chown microhost:sftp_cloud /SFTP/microhost
```

## Verify SSH & SFTP Access

Login to SFTP

Open a new terminal window and log in with `sftp` using a valid user account and password.

```
# sftp microhost@Server_IP
```

List files within the directory. Your output should be similar to the one below:

microhost@Server\_IP's password:

Connected to server\_IP

## Conclusion

In this guide, you successfully set up SFTP on a Debian server, then tested connectivity through a terminal session and FileZilla. You can create multiple users with different directories to securely upload and download files on your server.

Thank You 🙂
