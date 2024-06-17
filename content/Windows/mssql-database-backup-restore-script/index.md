---
title: "Mssql database backup restore script"
date: "2022-06-22"
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
