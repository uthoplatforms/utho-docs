---
title: "How to add or remove a User from a Linux Group"
date: "2022-10-07"
title_meta: "How to add or remove a User from a Linux Group"
description: "Linux is a multi-user system by default, which means that many people can connect to it at the same time and work. One of the most important jobs of a system administrator is to manage users. On a Linux system, user management includes creating, updating, and deleting user accounts and user groups."
keywords:  ['user management', 'Linux']
tags: ["Linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/miscellaneous/how-to-add-or-remove-a-user-from-a-linux-group']
---

![](images/How-to-add-or-remove-a-User-from-a-Linux-Group_utho.jpg)

**Description**

Linux is a multi-user system by default, which means that many people can connect to it at the same time and work. One of the most important jobs of a system administrator is to manage users. On a Linux system, user management includes creating, updating, and deleting user accounts and user groups.

In this short article, you will learn how to add or remove a user from a group in a Linux system.

**Need to check User group**

Just type in the following groups command to see whether you're a member of a certain user group:

```
#groups microhost.com
```

![](images/image-217.png)

\*Simply execute the groups command without providing any arguments in order to examine your own groups.

```
#groups
```

![](images/image-218.png)

**Add a User to a Group**

Make sure that the user in question already exists on the system before attempting to add them to a group. Use the usermod command with the -a flag, which informs usermod to add a user to the supplemental group(s), and the -G option, which specifies the actual groups in the following format, in order to add a user to a specific group. For further information, see the usermod man page.

```
#usermod -aG postgres microhost.com
```

```
#groups microhost.com
```

![](images/image-219.png)

**Remove a User from a Group**

```
#gpasswd -d microhost.com postgres
```

```
#groups microhost.com
```

![](images/image-220.png)

The deluser command lets you take a user out of a certain group.

```
#sudo deluser microhost.com postgres
```

**Thank You**
