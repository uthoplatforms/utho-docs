---
title: "How To open a port in Windows Server Firewall"
date: "2020-06-08"
Title_meta: GUIDE TO Open a Port in Windows Server Firewall

Description: This guide provides step-by-step instructions on how to open a port in the Windows Server Firewall. Learn how to navigate Windows Firewall settings, create inbound and outbound rules for specific ports, and ensure proper configuration for allowing network traffic through the firewall.

Keywords: ['open port', 'Windows Server', 'Firewall configuration', 'inbound rules', 'outbound rules', 'network security']

Tags: ["Open Port", "Windows Server", "Firewall Configuration", "Inbound Rules", "Outbound Rules", "Network Security"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-open-a-port-in-windows-server-firewall']
tab: true
---

#### Open Windows Firewall

Hit the Windows key and find "firewall with Advanced Security." Select the first option as described below. Once the window of the firewall opens, go to the next step.

![](images/fir1.png)

#### Configure Inbound rule.

In the top-left section , click on the "Inbound Rule" button and in the top right-hand section, select the "New Rule." For a better photo view, see below. A window will open "New Inbound Rule Wizard." Continue to the following step.

![](images/fir2-1024x317.png)

![](images/fir3-1024x174.png)

#### Go to Wizard

On the new window, follow the steps shown in the screenshots below

Choose **port** and hit **next**.

![](images/fir4.png)

Click on TCP and add the port, whatever port you want to open. Then click next.

![](images/fir5.png)

Click on Allow the Connection.

![](images/fir6.png)

Now, click on next and select the domain, Private & Public as per the screenshot:

![](images/fir7.png)

In the last section, Give the name and description.

![](images/fir8.png)

We have successfully completed the Port opening section.

Thank You :)
