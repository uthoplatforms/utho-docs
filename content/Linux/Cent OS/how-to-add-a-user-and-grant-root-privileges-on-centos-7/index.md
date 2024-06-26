---
title: "How to Add a User and Grant Root Privileges on CentOS 7"
date: "2022-10-04"
title_meta: "Guide for Apache Virtual Hosts in Centos 7"
description: "Discover how to add a new user and assign root privileges on CentOS 7. This tutorial provides step-by-step instructions to create a user account, grant sudo privileges, and manage user permissions effectively on CentOS 7, enhancing server security and administrative control.
"
keywords: ["add user CentOS 7 root privileges", "grant root access CentOS 7 user", "create user CentOS 7 sudo", "CentOS 7 add admin user", "add user with sudo access CentOS 7", "CentOS 7 create user root permissions", "CentOS 7 add user to sudoers", "CentOS 7 user management"]

tags: ["Root Privileges", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-add-a-user-and-grant-root-privileges-on-centos-7/']
tab: true
---

![How to Add a User and Grant Root Privileges on CentOS 7](images/How-to-Add-a-User-and-Grant-Root-Privileges-on-CentOS-7_utho.jpg)

## Introduction

On CentOS 7, you will learn how to Add a [User](https://en.wikipedia.org/wiki/User) and Grant Root Privileges by reading the following article. One of the responsibilities of a system administrator is to be responsible for adding users and providing those users with administrative privileges. After a user has been added to CentOS and granted root access, that user will be able to log in to CentOS and carry out activities that are system-level in nature. After that, they will be able to execute tasks with elevated privileges by prefixing them with the sudo command. You will learn how to create a new user and give them administrative access by following the instructions in this concise guide.

A user group known as the "wheel" group is present in CentOS 7 when the operating system is first installed. The powers of sudo are automatically granted to anyone who is a member of the wheel group. Providing a user with sudo capabilities can be accomplished in a brisk and uncomplicated manner by just adding that user to this group.

## Step 1: I will add a user that is microhost.

```
# adduser microhost
```

## Step 2: Now set the password for the new user:

```
# passwd microhost
```

![Add a User and Grant Root Privileges on CentOS 7](images/image-238.png)

## Step 3: Grant Root Privileges to the User

```
# visudo
```

The above command takes us to the /etc/sudoers.tmp file, where we can see the code below:

![Grant Root Privileges](images/image-239.png)

You will add the line for your new user in the same format immediately following the line for the root user. Because of this, we will be able to provide that user administrative privileges.

![Grant Root Privileges](images/image-240.png)

To save your changes and quit the current file, press escape :wq.

If you have followed the procedures in the previous section, you should now have a user named microhost who has the ability to use sudo to run commands with root privileges.

Also read: [How To Install MariaDB 10.7 on CentOS 7](https://utho.com/docs/tutorial/how-to-install-mariadb-10-7-on-centos-7/)

Thank You 🙂
