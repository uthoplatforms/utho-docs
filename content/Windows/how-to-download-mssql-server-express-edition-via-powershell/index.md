---
title: "How to download MSSQL Server Express Edition via PowerShell"
date: "2023-06-24"
Title_meta: GUIDE TO Download MSSQL Server Express Edition via PowerShell

Description: This guide provides detailed instructions on how to download Microsoft SQL Server Express Edition using PowerShell. Learn how to utilize PowerShell commands to initiate and manage the download process for MSSQL Server Express, facilitating efficient installation and setup on your Windows Server environment.

Keywords: ['MSSQL Server Express', 'PowerShell', 'download MSSQL Server', 'SQL Server installation', 'server management']

Tags: ["MSSQL Server Express", "PowerShell", "Download MSSQL Server", "SQL Server Installation", "Server Management"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-download-mssql-server-express-edition-via-powershell/']
---

INTRODUCTION

[Microsoft SQL Server Express](https://www.microsoft.com/en-us/download/details.aspx?id=101064) is a version of Microsoft's SQL Server relational database management system that is free to download, distribute and use. It comprises a database specifically targeted for embedded and smaller-scale applications. In this tutorial, we will learn how to download MSSQL Server Express Edition via PowerShell.

Prerequisites download MSSQL Server Express

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![](images/Screenshot_11-24.png)

**Step 3. Write the following script to download SQL server Express Edition**

```
function Install-SQLServerExpress2019 
{
    Write-Host "Downloading SQL Server Express 2019..."
    $Path = $env:TEMP
    $Installer = "SQL2019-SSEI-Expr.exe"
    $URL = "https://go.microsoft.com/fwlink/?linkid=866658"
    Invoke-WebRequest $URL -OutFile $Path\$Installer

    Write-Host "Installing SQL Server Express..."
    Start-Process -FilePath $Path\$Installer -Args "/ACTION=INSTALL /IACCEPTSQLSERVERLICENSETERMS /QUIET" -Verb RunAs -Wait
    Remove-Item $Path\$Installer
}
```

![download MSSQL Server Express](images/Screenshot_4_001-1024x335.png)

**Step 4. After writing the script, type "Install-SQLServerExpress2019" and hit ENTER**

![download MSSQL Server Express](images/Screenshot_4_003-1-1024x374.png)

![download MSSQL Server Express](images/Screenshot_4_004-1024x364.png)

![](images/Screenshot_4_005-1024x627.png)

SQL Server Express Edition downloading and installation has started.

Thank You!
