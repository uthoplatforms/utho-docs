---
title: "Install Plesk on Windows Server 2012"
date: "2020-04-29"
---

![](images/Install-Plesk-on-Windows-Server-2012_utho.jpg)

To Install Plesk in windows using browser,You need to follow below steps.

1\. Login on your server using RDP.

2\. Download plesk installer [Plesk Installer](https://installer-win.plesk.com/plesk-installer.exe).

3\. Double click on downlaoded `plesk installer` file.

![](images/Screenshot_1-4.png)

4\. It will open command prompt and your server default browser itself.

![](images/Screenshot_2-3.png)

4\. Enter your server `administrator user password` for next.

5\. Click on "`Install or Upgrade your product`"

![](images/Screenshot_3-4.png)

6\. Choose the latest stable version of your product and press `"Continue"`

![](images/Screenshot_4-3.png)

7\. The installation type determines which Plesk components and features are to be installed. The following types of installation are available:

![](images/Screenshot_5-4.png)

- The `Recommended` installation type includes all components necessary for web hosting (including web server, mail server, database server, etc.) plus the most popular and widely used features. If you don't know what type of installation to choose, going with Recommended is a safe bet.
- The `Full Installation` Type includes all Plek components and features. Note that choosing this type of installation will require the most disk space.
- The `Custom installation` type allows you to choose and install items from the list of all available components and features. This type of installation is recommended for experienced Plesk administrators.

You can choose an installation type that is not suited to your needs-you will be able to `add or remove` Plesk components at any time after the installation has finished.

![](images/Screenshot_6-3.png)

The custom installation type looks like-mark the components you want to install with the green checkbox icon, select the red cross icon for those you don't need. When you're satisfied with the components you've selected, click Continue to move forward.

![](images/Screenshot_7-1.png)

8\. You will see following setting in screenshot which you can change according to your requirement.

![](images/Screenshot_1-5.png)

9\. If you want to install plesk with `default setting`. Enter the password of administrator user and click on continue for proceed installation.

10\. You will see the output of the console inside the web interface. Wait till the installation process is over.

![](images/Screenshot_2-4.png)

11\. When you will get the message”`All operations with products and components have been successfully completed`.” Click Ok” and it will exit the installation page.

![](images/Screenshot_7-2.png)

12\. Now reboot your server and after reboot configure your plesk panel using plesk url `http://your-server-ip:8880` or `https://your-server-ip:8443` by entering administrator user and password.

13\. After login in plesk enter the email address for admin notifications and password for admin user.

![](images/Screenshot_9-2.png)

14\. Enter the Licnese key if you have plesk panel license key or go with “`Proceed with a full-featured trial license`” and Check the button “`I confirm that I've read and accepted the End-User License Agreement` “

![](images/Screenshot_8-2.png)

15\. Click On `enter plesk`.
