---
title: "Disable reboot using Ctrl-Alt-Del Keys in RHEL / CentOS 7/8"
date: "2022-06-22"
title_meta: "Guide for Disable reboot using Ctrl-Alt-Del Keys in Centos"
description: "Discover how to disable the Ctrl-Alt-Del key combination for rebooting in RHEL and CentOS 7/8. This guide provides step-by-step instructions to configure system settings to prevent accidental reboots using the Ctrl-Alt-Del keys on RHEL and CentOS Linux distributions.
"
keywords: ["disable reboot Ctrl-Alt-Del RHEL CentOS", "prevent restart key combination CentOS 7 8", "disable Ctrl-Alt-Del reboot RHEL 7 8", "CentOS disable restart shortcut", "disable reboot hotkey RHEL 7 8", "CentOS Ctrl-Alt-Del reboot disable", "RHEL 7 8 disable restart key", "Linux disable Ctrl-Alt-Del reboot"]

tags: ["Disable reboot using Ctrl-Alt-Del Keys", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/disable-reboot-using-ctrl-alt-del-keys-in-rhel-centos-7-8-support-internal/']
tab: true
---

![](images/Disable-reboot-using-Ctrl-Alt-Del-Keys-in-RHEL-_-CentOS-7_8-Support-Internal-1024x576.png)

:: In this blog we will know the process to disable the Ctrl-Alt-Del Key feature in the RHEL/CentOS system. This trick will work with both VPS & Physical Machine as well.

:: Firstly, you need to login on your Microhost Cloud Server with your root login details.

![](images/pasted-image-0-1-6.png)

:: Then you need to execute the below command on system with root privilege to run.

```
 # systemctl mask ctrl-alt-del.target 
```

:: After successful execute this command, it’s shown below result

![](images/pasted-image-0-26.png)

:: Now you Need to login into Microhost Cloud Platform then click on Console screen to access the KVM to check whether the Ctrl-Alt-Del Key is working or not.

![](images/pasted-image-0-2-5.png)

:: I have Press the Ctrl-Alt-Del Key in the right hand corner but system is not rebooting, it means that this method is working to stop this action.

![](images/pasted-image-0-3-5-1024x267.png)

Thank You!
