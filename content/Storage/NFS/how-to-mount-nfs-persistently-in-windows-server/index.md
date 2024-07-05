---
title: "How to mount NFS persistently in Windows Server"
date: "2022-11-11"
---

**NFS** allows a system to share directories and files with others over a network. By using NFS, users and programs can access files on remote systems almost as if they were local files.

  
In other words, **Network File System (NFS)** is a file sharing solution that allows you to transfer files using the NFS protocol between computers running [Windows Server](https://utho.com/docs/tutorial/category/windows-tutorial/) and [UNIX](https://en.wikipedia.org/wiki/Unix) operating systems.

**Steps to mount NFS Persistently in Windows Server**

**By Mapping Network Drive**

1. First open up “This PC” and select Computer from the menu at the top. From here click on Map network drive, as shown below.

![mount NFS persistently in Windows Server](images/nfs1.png)

2. The Map Network Drive window will open, select the drive letter that you want to assign to the NFS share, followed by the IP address or hostname of the NFS server as well as the path to the exported NFS directory. Click the Finish button when complete.

![mount NFS persistently in Windows Server](images/nfs3.png)

3. You may see a pop up window showing that the connection is being attempted. Once complete the shared NFS folder will open up.

When you view “This PC” you will see the mapped network drive under Network location.

![mount NFS persistently in Windows Server](images/nfs5-1-1.png)

Thankyou
