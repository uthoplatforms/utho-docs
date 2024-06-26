---
title: "How to share a folder over network using Server Manager in Windows Servers"
date: "2023-05-08"
---

#### INTRODUCTION

In this tutorial, we will learn how to share a folder over network using Server Manager in Windows Servers. There are many ways to setup shared folder in **[Windows Server](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022)**. You can use folder properties share option to share a folder. In this tutorial, we will learn the steps to share a folder in Windows Server 2016 using Server Manager folder share option.

##### Prerequisites

1. [Windows Server](https://utho.com/docs/tutorial/how-to-reset-a-lost-administrator-password-in-windows-server/)
2. Internet connectivity

Step 1. Connect to your Windows server via **RDP**.

![Share folder Server Manager](images/Screenshot_2-47.png)

Step 2. Go to Server Manager.

![Share folder Server Manager](images/Screenshot_1-37-1024x506.png)

Step 3. Go to **File and Storage Services**.

Step 4. Click on TASKS and Click on "**New Share**"

![](images/Screenshot_2-45-1024x513.png)

Step 5. Select **SMB Share - Quick**

![Share folder Server Manager](images/Screenshot_3-35.png)

Step 6. Select the folder via it's path that you wish to share

![](images/Screenshot_4-37.png)

Step 7. Give the folder appropriate permissions as per your requirements.

Step 8. Click close once the share is completed.

![Share folder Server Manager](images/Screenshot_5-30.png)

Step 9. Connect to the folder using network path using login credentials of RDP user

![](images/Screenshot_6-30.png)

![Share folder Server Manager](images/Screenshot_7-27-1024x430.png)

Folder aceessed.

Thank You!
