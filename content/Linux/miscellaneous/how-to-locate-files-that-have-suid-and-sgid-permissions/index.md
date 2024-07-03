---
title: "How to Locate Files That Have SUID and SGID Permissions"
date: "2022-10-08"
title_meta: "How to Locate Files That Have SUID and SGID Permissions"
description: "How to Locate Files That Have SUID and SGID Permissions"
keywords:  ['SUID', 'SGID','linux']
tags: ["linux", "SUID"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/miscellaneous/how-to-locate-files-that-have-suid-and-sgid-permissions']
---

![](images/How-to-Locate-Files-That-Have-SUID-and-SGID-Permissions_utho.jpg)

###### **Description**

In this section, we will walk you through the process of locating files that have [SUID](https://www.linux.com/training-tutorials/what-suid-and-how-set-suid-linuxunix/) (Setuid) and SGID (Setgid) configured, in addition to explaining auxiliary file permissions, which are referred to as "special permissions" in [Linux](https://utho.com/docs/tutorial/category/linux-tutorial/). To Find SUID and SGID Permissions, first know What is SUID and SGID ?

## What is SUID and SGID?

SUID is a special file permission that can only be applied to executable files. It gives other users the ability to run the file with the permissions that would normally be granted to the file's owner. You will observe a s (which stands for SUID), which denotes a particular authorization for the user to execute, rather than the usual x, which represents execute permissions.

Other users are granted the ability to inherit the effective GID of the file group owner through the use of a unique file permission known as SGID. This permission can be applied to executable files as well. Also, instead of the typical x, which denotes execute permissions, you will find an s, which stands for the SGID notation for special permission for group user.

Using the search command, let's investigate how to locate files that have the SUID and SGID parameters defined.

The syntax consists of the following:

```
# find directory -perm /permissions
```

**NOTE:** If you are administering your system as a regular user, you will need to use the sudo command in order to get root rights so that you may access and list certain directories and files. These directories and files include /etc, /bin, and /sbin, amongst others.

## How to Find Files with SUID Set

Using the -perm (print files only with permissions set to 4000) option, the following command is an example that will locate all files in the current directory that have the SUID setting.

```
# find . -perm /4000 
```

When you use the ls command with the -l option (which stands for long listing), you will be able to see the permissions that are set on the files that are listed.

## How to Find SGID Set Files

Enter the following command to search for files that have the SGID parameter set.

```
# find . -perm /2000 
```

Run the following command to search for files that contain a setting for both SUID and SGID.

```
# find . -perm /6000 
```

You were shown how to locate files in Linux that have the SUID (Setuid) and SGID (Setgid) settings in place. If you have any questions

###### **Thank You**
