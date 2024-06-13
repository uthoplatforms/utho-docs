---
title: "How to Change Computer Name in Windows Server via PowerShell"
date: "2023-06-13"
---

![](images/How-to-Change-Computer-Name-in-Windows-Server-via-PowerShell-1024x576.jpg)

## INTRODUCTION Change Computer Name

Changing name of the computer/system is just a basic step to set a custom tag for your system. In this tutorial, we will learn how to Change Computer Name in Windows Server 2016, 2019 and 2022 via PowerShell.

#### Prerequisites

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- [PowerShell](https://learn.microsoft.com/en-us/powershell/) with Administrator rights

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![Change Computer Name](images/Screenshot_11-20.png)

Step 3. Run the following command to \[rtestserver\]

```
Rename-Computer -NewName rtestserver -Force -PassThru
```

![](images/Screenshot_1-41.png)

![Change Computer Name](images/Screenshot_2-51.png)

**Step 4. Run the following command to restart server and apply changes** Change Computer Name

```
Restart-Computer -Force
```

![](images/Screenshot_3-40.png)

Server name changed.

![Change Computer Name](images/Screenshot_4-40.png)

Thank You!
