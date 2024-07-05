---
title: "How to setup NTP Server for time synchronization using Powershell"
date: "2023-03-27"
Title_meta: GUIDE TO Setup NTP Server for Time Synchronization Using PowerShell

Description: This guide provides step-by-step instructions on how to set up an NTP (Network Time Protocol) server for time synchronization using PowerShell. Learn how to configure the Windows Time service (w32time), set up the server to synchronize with reliable time sources, and ensure accurate timekeeping across your network environment.

Keywords: ['NTP server', 'time synchronization', 'PowerShell', 'Windows Time service', 'network time protocol', 'server administration']

Tags: ["NTP Server", "Time Synchronization", "PowerShell", "Windows Time Service", "Network Time Protocol", "Server Administration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['content/Windows/how-to-setup-ntp-server-for-time-synchronization-using-powershell']
tab: true
---

![setup NTP Server for time synchronization](images/How-to-setup-NTP-Server-for-time-synchronization-using-Powershell_utho.jpg)

Step 1. Login to your [Windows Server](https://www.microsoft.com/en-in/windows-server)

Step 2. Open [Powershell](https://utho.com/docs/tutorial/how-to-change-rdp-port-via-powershell-in-windows-server/) as Administrator setup NTP Server for time synchronization

![](images/Screenshot_0-1024x572.png)

If the computer you want to configure is an Active Directory domain controller, the NTP server function is automatically enabled. Therefore, the following is an example of setting the NTP server function to Windows Server in a workgroup environment.

Step 3. Run the following command to check current settings

```
Get-ItemProperty -Path "HKLM:SYSTEMCurrentControlSetServicesw32timeTimeProvidersNtpServer"
```

![](images/Screenshot_1-24-1024x151.png)

![setup NTP Server for time synchronization](images/Screenshot_2-29-1024x339.png)

Step 4. Run the following command to Enable NTP server function

```
Set-ItemProperty -Path "HKLM:SYSTEMCurrentControlSetServicesw32timeTimeProvidersNtpServer" -Name "Enabled" -Value 1
``` setup NTP Server for time synchronization

![setup NTP Server for time synchronization](images/Screenshot_3-23-1024x366.png)

Step 5 . Change AnnounceFlags to 5

```
# Meaning of numbers

```

```
# 0x00 : Not a time server

```

```
# 0x01 : Always time server

```

```
# 0x02 : Automatic time server

```

```
# 0x04 : Always reliable time server

```

```
# 0x08 : Automatic reliable time server

```

```
Set-ItemProperty -Path "HKLM:SYSTEMCurrentControlSetservicesW32TimeConfig" -Name "AnnounceFlags" -Value 5
```

![setup NTP Server for time synchronization](images/Screenshot_4-26-1024x347.png)

Step 6. Allow NTP port if Windows Firewall is running

```
New-NetFirewallRule `
```

![](images/Screenshot_5-21.png)

```
-Name "NTP Server Port" `
-DisplayName "NTP Server Port" `
-Description 'Allow NTP Server Port' `
-Profile Any `
-Direction Inbound`
-Action Allow`
-Protocol UDP `
-Program Any`
-LocalAddress Any`
-LocalPort 123
```

Thank You!
