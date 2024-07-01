---
title: "Mssql database backup restore script"
date: "2022-06-22"
Title_meta: MSSQL Database Backup Restore Script

Description: This script provides instructions and commands for backing up and restoring MSSQL databases. Learn how to create backups, transfer them to another server or storage, and restore them using SQL Server Management Studio or Transact-SQL commands.

Keywords: ['MSSQL', 'database backup', 'restore script', 'SQL Server Management Studio', 'Transact-SQL', 'database administration']

Tags: ["MSSQL", "Database Backup", "Restore Script", "SQL Server Management Studio", "Transact-SQL", "Database Administration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/mssql-database-backup-restore-script']
tab: true
---

![](images/Mssql-database-backup-restore-script-1024x576.png)

USE \[master\]  
RESTORE DATABASE \[Algeria\]

  
FROM DISK = N'C:\\db\\db\\Algeria\_FULL\_05282021\_131711.BAK' WITH FILE = 1,  

MOVE N'Algeria' TO N'C:\\Program Files\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\DATA\\Algeria.mdf',  

MOVE N'Algeria\_log' TO N'C:\\Program Files\\Microsoft SQL Server\\MSSQL12.MSSQLSERVER\\MSSQL\\DATA\\Algeria\_log.ldf',  

NOUNLOAD,  
REPLACE,  
STATS = 5;

Thank you!
