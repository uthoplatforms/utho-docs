---
title: "How To Add User to Sudoers or Sudo Group in CentOS 7"
date: "2022-06-22"
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
