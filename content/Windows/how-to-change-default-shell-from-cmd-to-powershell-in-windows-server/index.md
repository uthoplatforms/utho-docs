---
title: "How to change default shell from cmd to PowerShell in Windows Server"
date: "2022-11-08"
Title_meta: GUIDE TO Change Default Shell from CMD to PowerShell in Windows Server

Description: Learn how to change the default shell from CMD to PowerShell in Windows Server with this step-by-step guide. Follow the instructions to configure your server to launch PowerShell by default, enhancing your command-line experience with advanced features and capabilities.

Keywords: ['Windows Server', 'change default shell', 'CMD to PowerShell', 'command-line configuration', 'PowerShell']

Tags: ["Windows Server", "Change Default Shell", "CMD", "PowerShell", "Command-Line Configuration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-change-default-shell-from-cmd-to-powershell-in-windows-server/']
---

![](images/How-to-change-default-shell-from-cmd-to-PowerShell-in-Windows-Server_utho.jpg)

  
Follow these steps to change default shell from cmd to PowerShell in the following article..

Step 1. Login to your [Windows Server](https://utho.com/docs/tutorial/category/windows-tutorial/).

Step 2. Start PowerShell as Administrator on the [OpenSSH](https://en.wikipedia.org/wiki/OpenSSH) server and run the following command:

```
Get-Command powershell | Format-Table -AutoSize -Wrap
```

CommandType Name Version Source

\-------------- ------ ------- ------

Application powershell.exe 10.0.17763.1 C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe

Step 3. Set DefaultShell=PowerShell in the OpenSSH registry entry. PowerShell PATH specifies the PATH confirmed above

```
New-ItemProperty -Path "HKLM:SOFTWAREOpenSSH" -Name DefaultShell -Value "C:WindowsSystem32WindowsPowerShellv1.0powershell.exe" -PropertyType String -Force
```

DefaultShell : C:\\Windows\\System32\\WindowsPowerShell\\v1.0\\powershell.exe

PSPath : Microsoft.PowerShell.Core\\Registry::HKEY\_LOCAL\_MACHINE\\SOFTWARE\\OpenSSH

PSParentPath : Microsoft.PowerShell.Core\\Registry::HKEY\_LOCAL\_MACHINE\\SOFTWARE

PSChildName : OpenSSH

PS Drive : HKLM

PSProvider : Microsoft.PowerShell.Core\\Registry

So, this is how you can change default shell from cmd to PowerShell in Windows Server.

Thank You!
