---
title: "Install SQL Server 2012 Express Edition in Windows Server 2012"
date: "2020-06-05"
---

![](images/Install-SQL-Server-2012-Express-Edition-in-Windows-Server-2012_utho.jpg)

Asp.net 3.5 version should be installed on server before install SQL server and you have access administrator RDP user login details.

Step:- 1. Install Asp.net 3.5 version using server manager.

(i) Open server manager click on server manager button.

![](images/Screenshot-6-1024x619.png)

(ii) Click on Add roles and features.

![](images/Screenshot_1-7-1024x453.png)

(iii) Select .NET framework 3.5 Features and install.

![](images/Screenshot_2-7.png)

(iv) Click on next.

![](images/Screenshot_3-5.png)

(v) When you will get below message in screenshot close and exit the server manager installation wizard.

![](images/Screenshot_4-6.png)

Step:- 2. For download SQL express edition 2012 setup click **[here](https://www.microsoft.com/en-in/download/confirmation.aspx?id=29062&6B49FDFB-8E5B-4B07-BC31-15695C5A2143=1)** and save it on your server.

Step: 3. When setup will download on your server. Double click on the setup file.

![](images/Screenshot_5-5.png)

Step:- 4. Click on "New Sql stand-alone installation or add features in existing installation".

![](images/Screenshot_6-4.png)

Step:- 5. Select terms and policy and click on next.

![](images/Screenshot_7-4.png)

Step:- 6. Select features which you want to install in SQL database and defined installation directory and click on next.

![](images/Screenshot_8-4.png)

Step:- 7 Enter instance name and id of SQL database server.

![](images/Screenshot_9-4.png)

Step:- 8 Select mixed mode enter sa user password of sql database server.

![](images/Screenshot_10-1.png)

Step:- 9. Select option "install and configure" and click on next and next again. Now the installation is started of sql and it will take 20-40 minutes for complete.

![](images/Screenshot_11-2.png)

Step:- 10. When you will get the message below means sql installation is completed.

![](images/Screenshot_12.png)

Now SQL database server is installed and operational.

Thankyou..
