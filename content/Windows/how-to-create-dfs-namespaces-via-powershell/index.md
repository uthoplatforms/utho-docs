---
title: "How to create DFS NameSpaces via PowerShell"
date: "2023-06-26"
Title_meta: GUIDE TO Create DFS NameSpaces via PowerShell

Description: This guide provides step-by-step instructions on how to create DFS (Distributed File System) NameSpaces using PowerShell. Learn how to configure and manage DFS namespaces to organize and unify access to shared folders across your Windows Server environment efficiently.

Keywords: ['DFS', 'DFS NameSpaces', 'PowerShell', 'distributed file system', 'server management', 'file sharing']

Tags: ["DFS", "DFS NameSpaces", "PowerShell", "Distributed File System", "Server Management", "File Sharing"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-create-dfs-namespaces-via-powershell/']
---

INTRODUCTION

[DFS](https://en.wikipedia.org/wiki/Distributed_File_System_(Microsoft)) is a set of client and server services that allow an organization using Microsoft Windows servers to organize many scattered SMB file shares into a distributed file system. DFS has two components to its service: Location transparency and Redundancy. Basically a distributed file system (DFS) is **a file system that spans across multiple file servers or multiple locations**, such as file servers that are situated in different physical places. Files are accessible just as if they were stored locally, from any device and from anywhere on the network. In this tutorial, we will learn how to create DFS NameSpaces via PowerShell.

Prerequisites

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![create DFS NameSpaces](images/Screenshot_11-28.png)

**Step 3. Run the following command to create a folder for DFS Namespace share**

```
mkdir C:\NEWFOLDER\Share
```

![create DFS NameSpaces](images/Screenshot_4-43.png)

Step 4. Run the following command to set smbshare for DFS Namespace

```
New-SmbShare –Name 'Share' –Path 'C:\NEWFOLDER\Share' -FullAccess 'Everyone'
```

![](images/Screenshot_5-33.png)

**Step 5. Run the following command to create DFS namespace**

\-Path (path of share)

\-Type (standalone , Domain1 , Domain2)

\-TargetPath (target share path : the one created above)

```
New-DfsnRoot -Path '\\NEWFOLDER\Share' -Type DomainV2 -TargetPath '\\NEWFOLDER2\Share'
```

![create DFS NameSpaces](images/Screenshot_6-34-1024x227.png)

**Step 6. Run the following command to confirm path**

```
Get-DfsnRoot -Path '\\NEWFOLDER\Share' | Format-List
```

![create DFS NameSpaces](images/Screenshot_7-30.png)

Thank You!
