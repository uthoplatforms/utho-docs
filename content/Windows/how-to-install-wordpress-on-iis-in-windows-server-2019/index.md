---
title: "How to install Wordpress on IIS in WIndows Server 2019"
date: "2021-12-19"
---

![](images/How-to-install-WordPress-on-IIS-in-WIndows-Server-2019_utho.jpg)

## Introduction

WordPress is a free and open-source content management system written in hypertext preprocessor language and paired with a MySQL or MariaDB database with supported HTTPS. In this article we will learn to install wordpress on IIS in a Windows Server 2019.

## **Prerequisites**

- Windows Server® 2019

- IIS 

- PHP (latest version)

- MySQL (latest version)

## **Step 1. Login to your win server via RDP.**

![](images/Screenshot_1-10.png)

## **Step 2. Install IIS (Internet Information Services) if you don’t have it on your server.**

IIS is very important to install Wordpress on IIS in WIndows Server 2019.

Please see the link below for the IIS installation.

[Install IIS through Powershell](https://utho.com/docs/tutorial/how-to-install-iis-via-powershell-in-windows-server/) ----- [Install IIS through GUI](https://utho.com/docs/tutorial/installation-and-configuration-of-iis-web-server-on-windows-server/)

## **Step 3. Open IIS**

![](images/Screenshot_11-5.png)

Click on your server.

## **Step 4. On the right side, click on Get New Web Platform Components**

![](images/Screenshot_12-4.png)

## **Step 5. Install Web Platform Installer**

![](images/Screenshot_13-3.png)

![](images/Screenshot_17-1.png)

## **step 6. Open Web Platform Isntaller**

![](images/Screenshot_15-2.png)

## **Step 7. Search for PHP, and install PHP 7.4.13 (x86).**

![](images/Screenshot_16-1-1024x549.png)

## **Step 8. Now open your browser and download PHP Manager for IIS.** 

Use the link below to download

LINK: [Download PHP Manager for IIS](https://www.iis.net/downloads/community/2018/05/php-manager-150-for-iis-10)

![](images/Screenshot_18-1.png)

![](images/Screenshot_19-4.png)

![](images/Screenshot_20-2.png)

![](images/Screenshot_21-1.png)

## **Step 9. Download MariaDB.**

Use one of the links below to download

LINK 1: [Download MariaDB 10.6.5](https://mariadb.org/download/?t=mariadb&p=mariadb&r=10.6.5&os=windows&cpu=x86_64&pkg=msi&m=inet)

LINK 2:  [MariaDB downloads page](https://mariadb.com/downloads/)

![](images/Screenshot_23-1.png)

![](images/Screenshot_24-1.png)

Enter the password for the root user.

![](images/Screenshot_25-1.png)

![](images/Screenshot_26-1.png)

![](images/Screenshot_27-1.png)

## **Step 10. After the installation, go to Start Menu and open HeidiSQL**

![](images/Screenshot_28-1.png)

## **Step 11. Add a new session and change its name from "Unamed" to "localhost"**

![](images/Screenshot_29-1.png)

![](images/Screenshot_30-1.png)

## **Step 12. Set a password for the root user.** Click save, open

![](images/Screenshot_31-1.png)

## **Step13. Create a new database under localhost**

![](images/Screenshot_32-1024x671.png)

Name it "**wordp1**".

![](images/Screenshot_33.png)

## **Step 14. Go to the User Manager**

![](images/Screenshot_34-1024x645.png)

## **Step 15. Click on "Add new user."**

![](images/Screenshot_35.png)

## **Step 16. Set the username as admindb. Set from the host as Access from everywhere**

![](images/Screenshot_36-1024x618.png)

## **Step 17. Set a password for the new user and retype the password**

![](images/Screenshot_37.png)

Allow Global Privileges

Click save, then close.

![](images/Screenshot_38.png)

## **Step 18. Download Wordpress**

Use the link to download: [Wordpress Downloads Page](https://wordpress.org/download/)

![](images/Screenshot_2-14.png)

## **Step 19. Copy the contents of the downloaded WordPress file.**

![](images/Screenshot_39-1024x497.png)

## **Step 20. Go to the following location: C:\\inetpub\\wwwroot**

Create a folder there, (we will name it "**test"**)

And paste the copied Wordpress contents into this folder.

![](images/Screenshot_1111.png)

## **Step 21**. **Open IIS**Go to sites and click on **Add Website.**

![](images/Screenshot_40.png)

## **Step 22. Set the site name as: APP1**

Set the physical path as: C:\\inetpub\\wwwroot\\test

![](images/Screenshot_41.png)

## **Step 22. Click on "Connect as..." > Specific user > set**

Set user name as Administrator

Set user password

Retype the password; click OK.  
  
Click on Test Settings..

![](images/Screenshot_42.png)

Proceed if the connection is valid.

![](images/Screenshot_43.png)

Set port to: 8041

![](images/Screenshot_44.png)

## **Step 23. Open IIS and open 'APP1'  
**Open **PHP Manager**

![](images/Screenshot_45-1024x430.png)

## **Step 24. Click on "View Recommendations,"**

![](images/Screenshot_46-1024x434.png)

change the PHP configuration for your machine. Select both options available and click OK.

![](images/Screenshot_47.png)

## **Step 25. Go to APP1  
**Under the **Actions** section, click on **Browse \*.8041 (http)**

![](images/Screenshot_48-1024x402.png)

**The Wordpress installation page will open.**

![](images/Screenshot_49-1024x511.png)

## **Step 26. Select the installation language and proceed.**

![](images/Screenshot_50-1024x496.png)

## **Step 27. Set the Database Name as: wordp1.**

Set Username as: **admindb**

Type your admindb **password**

Set Database Host as: **localhost**

Set the table prefix as: **wp\_**

![](images/Screenshot_51-1024x474.png)

Click on "submit."

## **Step 28. Set the Site Title as: APP1**

Set Username as: admin

Set Password

Enter your email Id

Check the search **engine visibility** option.

![](images/Screenshot_52-1024x550.png)

Click on **Install Wordpress.**

**  
Installation was successful.**

![](images/Screenshot_100.png)

## **Step 29. Login with your username and password.**

![](images/Screenshot_101.png)

![](images/Screenshot-7-1024x576.png)

## **Wordpress successfully installed.**

Thank You.
