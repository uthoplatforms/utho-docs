---
title: "How to Install Windows RDP CAL license in windows servers"
date: "2022-06-07"
Title_meta: GUIDE TO Install Windows RDP CAL License in Windows Servers

Description: This guide provides comprehensive instructions on how to install Remote Desktop Services (RDS) Client Access Licenses (CALs) on Windows Servers. Learn the steps required to purchase, activate, and manage RDP CALs to enable remote desktop connections and manage user access efficiently.

Keywords: ['Windows Server', 'RDP CAL license', 'Remote Desktop Services', 'install RDP CAL', 'server administration', 'remote desktop connection']

Tags: ["Windows Server", "RDP CAL License", "Remote Desktop Services", "Install RDP CAL", "Server Administration", "Remote Desktop Connection"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-windows-rdp-cal-license-in-windows-servers']
tab: true
---

![](images/How-to-Install-Windows-RDP-CAL-license-in-windows-servers_utho.jpg)

**An RDS CAL license is also known as a Remote Deskto Service Client Access License. It allows multiple users to access the server concurrently. A user can perform their own task in their RDP session.**

_We will have a brief explanation of the license installation process._

**Step 1:** We need to install the role for Remote Desktop Services. We can install the role from the server manager option. Please have a look at the below screenshot.

![](images/ca1-1024x521.png)

**STEP 2:** As per the above screenshot, we have to select the Remote Desktop Services option and then click on "NEXT." It will take you to the next option, where you have to select the additional features present in the Remote Desktop Services.

![](images/ca2-1024x539.png)

![](images/ca3-1024x453.png)

![](images/ca4-1024x520.png)

**STEP 3:** As per the above screenshot, we have added two features to this. Now we will click on "NEXT". Afterward, a new window will appear for the installation of this role. Please have a look at the below screenshot for reference.

![](images/ca5-1024x446.png)

**Step 4:** Once you click on "install," the installation process will start. It will take 5 to 10 minutes to install the role. Once the installation is completed, we have to close the window. Afterward, "reboot" the server to reflect the applied changes.

![](images/ca6-1024x516.png)

**Step 5:** Once the server has rebooted successfully, we can see the remote desktop services option in the toolbar of the server manager. Please have a look at the screenshot.

![](images/ca7-1024x464.png)

**Step 6:** Now we have to click on Remote Desktop Services and then select the “Remote Desktop Licensing Manager”. While clicking it, you can see the below prompt as per the screenshot.

![](images/ca8-1024x432.png)

**Step 7:** We can see that the licensing server is not activated yet. Hence, we have to activate the server first.

![](images/ca9-1024x418.png)

![](images/ca10.png)

**Step 8:** Further, we have filled in the required details to activate the server.

![](images/ca13.png)

![](images/ca11.png)

![](images/ca12-1024x783.png)

![](images/ca14.png)

**Step** **9:** As per the above screenshot, we have activated the licensing server successfully. Now we will initiate the license installation process by clicking on 'Next'.

![](images/ca15.png)

**Step 10:** While clicking on next, as per the above screenshot, the next thing is to select the license program. Please have a look at the below screenshot for reference.

![](images/ca16-1024x769.png)

**Step 11:** Now we have to enter the agreement number.

![](images/ca17-1024x760.png)

**Step 12:** Now we have to select the operating system and then the license type, as well as the license quantity. Afterward, click on "Next" and then click on "FINISH".

![](images/ca18.png)

We have successfully installed the CAL license now.

Thank you 😊
