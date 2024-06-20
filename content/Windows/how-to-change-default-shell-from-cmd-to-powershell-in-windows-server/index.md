---
title: "How to change default shell from cmd to PowerShell in Windows Server"
date: "2022-11-08"
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
