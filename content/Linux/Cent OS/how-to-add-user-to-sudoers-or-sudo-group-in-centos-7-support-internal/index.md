---
title: "How To Add User to Sudoers or Sudo Group in CentOS 7"
date: "2022-06-22"
title_meta: "Guide for Add User to Sudoers or Sudo Group in Centos 7"
description: "Learn how to add a user to the sudoers or sudo group in CentOS 7 to grant administrative privileges. This guide provides detailed steps to modify the sudoers file, add a user to the sudo group, and configure sudo access on CentOS 7, enabling users to perform administrative tasks with elevated permissions.
"
keywords: ["add user to sudoers CentOS 7", "add user to sudo group CentOS 7", "CentOS 7 sudoers file add user", "grant sudo access CentOS 7 user", "CentOS 7 sudoers configuration", "CentOS 7 add user to sudo group", "CentOS 7 sudo privileges", "sudoers file CentOS 7"]

tags: ["Sudoers", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-add-user-to-sudoers-or-sudo-group-in-centos-7-support-internal/']
tab: true
---

![](images/How-To-Add-User-to-Sudoers-or-Sudo-Group-in-CentOS-7-Support-Internal-1024x576.png)

**How To Add User to Sudoers or Sudo Group in CentOS 7**

The sudo command stands for “Super User DO” and temporarily elevates the privileges of a regular user for administrative tasks. The sudo command in CentOS provides a workaround by allowing a user to elevate their privileges for a single task temporarily.

This guide will walk you through the steps to add a user to sudoers in CentOS.

**Prerequisites**

- A system running CentOS 7
- Access to a user account with root privileges

Step 1. Login to the server with root privileges.

```
root@103.150.136.136.163's password:
```

step 2. Add a user with the following command

```
root@cloudserver-rootdenied ~]# adduser narender
```

step 3. Set password of the user with the following command

```
root@cloudserver-rootdenied ~]# passwd narender
```

Step 4. Now run the following command

```
root@cloudserver-rootdenied ~]# visudo
```

Step 5. Now edit the visudo file and unselect the below line

```
## Allows people in group wheel to run all commands
```

```
%wheel        ALL=(ALL)       ALL
```

Adding User to the wheel Group  

The easiest way to grant sudo privileges to a user on CentOS is to add the user to the “wheel” group. Members of this group are able to run all commands via sudo and are prompted to authenticate themselves with their password when using sudo.  

Step 6. To add a user to the wheel group, use the command:

```
root@cloudserver-rootdenied ~]# useradd -aG wheel narender
```

Step 7. Switch to the new (or newly-elevated) user account with the su (substitute user) command:

```
root@cloudserver-rootdenied ~]# su - narender
```

```
# sudo ls -la /root
```

Now user is able to execute commands with sudo privilege   

Thank You!
