---
title: "Windows Server Backup feature (2012R2, 2016, 2019)"
date: "2022-11-11"
Title_meta: Windows Server Backup Feature (2012R2, 2016, 2019)

Description: This guide explores the Windows Server Backup feature across versions 2012R2, 2016, and 2019. Learn how to use built-in tools to backup and restore server data, configure backup schedules, manage recovery options, and ensure data protection for your Windows Server environment.

Keywords: ['Windows Server Backup', 'backup and restore', 'data protection', 'server administration', 'Windows Server 2012R2', 'Windows Server 2016', 'Windows Server 2019']

Tags: ["Windows Server Backup", "Backup and Restore", "Data Protection", "Server Administration", "Windows Server 2012R2", "Windows Server 2016", "Windows Server 2019"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/windows-server-backup-feature-2012r2-2016-2019']
tab: true
---

![](images/Windows-Server-Backup-feature-2012R2-2016-2019-1024x576.png)

## Introduction

[Windows Server Backup](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj614621(v=ws.11)) (WSB) is a feature that provides backup and recovery options for Windows server environments. Windows Server Backup is a feature that provides a set of wizards and other tools for you to perform basic backup and recovery tasks for the server it is installed on. Administrators can use Windows Server Backup to back up a full server, the system state, selected storage volumes or specific files or folders, as long as the data volume is less than 2 terabytes. Windows Server Backup replaced the Ntbackup feature in earlier Windows Server operating systems. 

In this article we will learn to take backup of one Windows Server into another Windows Server. For this we obviously need, two windows servers. For better understing, we will call them Server1 and Server2.

## Prerequisites

1. 2 Windows Servers with **IIS** and **Windows Server Backup** feature installed.
2. Internet connectivity on both the servers.
3. Backup user wih same credentials on both the servers.

## Stage ONE in SERVER-1 Windows Server Backup feature

Step 1. Login to server **1**

![Windows Server Backup feature](images/Screenshot_2-32.png)

Step 2. Open [Server Manager](https://learn.microsoft.com/en-us/windows-server/administration/server-manager/server-manager)

![Windows Server Backup feature](images/Screenshot-18-1024x549.png)

Step 3. Click on **Add roles and feartures**

Install **Windows Server Backup**

![Windows Server Backup feature](images/Windows-Server-Backup-1.png)

![Windows Server Backup feature](images/Windows-Server-Backup-2.png)

![Windows Server Backup feature](images/Windows-Server-Backup-3.png)

## Stage TWO in SERVER-2

Step 1. We will create a folder in server-2 and share it over network, in order to be able store the backup from server-1

Create a folder

![Windows Server Backup feature](images/Screenshot-1-3-1024x540.png)

Go to it's Properties

![Windows Server Backup feature](images/Screenshot-2-1-1024x540.png)

Go to Sharing tab, and click on "**Share...**"

![Windows Server Backup feature](images/Screenshot-3-2-1024x539.png)

Click on "**Share...**"

![Windows Server Backup feature](images/Screenshot-4-1-1024x539.png)

![Windows Server Backup feature](images/Screenshot-5-2-1024x538.png)

Copy the path of the shared folder \\\\server\_ip\\foldername

## Stage THREE in SERVER-1 Windows Server Backup feature

Step 1. Click on Tools and open Windows Server Backup

![](images/Screenshot-3-1-1024x549.png)

Step 2. We will a create one backup for this posts' purpose. Click on 'Backup once...'

![Windows Server Backup feature](images/Screenshot-4-2-1024x549.png)

![Windows Server Backup feature](images/Screenshot-5-3-1024x548.png)

We will choose the "Custom" option to backup specific files/folder

![](images/Screenshot-6-1-1024x548.png)

Click on "Add Items" to select folders for backup

![Windows Server Backup feature](images/Screenshot-7-1-1024x548.png)

We will select the folder "ftpbackup"

![](images/Screenshot-8-1024x548.png)

![Windows Server Backup feature](images/Screenshot-9-1024x548.png)

Click on "Remote shared folder"

![](images/Screenshot-10-1024x548.png)

Input the folder path in location and click on next

![Windows Server Backup feature](images/Screenshot-12-1024x547.png)

Input the server credentials of SERVER-2

![](images/Screenshot-13-1024x548.png)

![Windows Server Backup feature](images/Screenshot-14-1024x548.png)

Click on "**Backup**" to initiate backup

![Windows Server Backup feature](images/Screenshot-15-1024x549.png)

![](images/Screenshot-16-1024x548.png)

![](images/Screenshot-17-1-1024x549.png)

Backup created in the shared folder.

![](images/Screenshot-6-2-1024x539.png)

Thank You!
