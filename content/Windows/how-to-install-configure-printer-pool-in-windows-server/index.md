---
title: "How to Install &amp; Configure Printer Tool in Windows Server"
date: "2022-04-20"
Title_meta: GUIDE TO Install & Configure Printer Tool in Windows Server

Description: This guide provides comprehensive instructions on how to install and configure a printer tool in Windows Server. Learn the process of setting up printers, configuring print queues, managing print jobs, and ensuring efficient printing services across your Windows Server environment.

Keywords: ['Windows Server', 'install printer tool', 'configure printers', 'print management', 'server administration', 'printing services']

Tags: ["Windows Server", "Install Printer Tool", "Configure Printers", "Print Management", "Server Administration", "Printing Services"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-install-configure-printer-pool-in-windows-server']
---

![](images/How-to-Install-Configure-Printer-Tool-in-Windows-Server-1024x576.png)

#### **Prerequisites:**

1. Windows Server
2. IIS
3. A printer

Step 1. Log into your Windows server via RDP.

Step 2. Open **Server Manager,** click **Add Roles & Features,** then click Next **3 times.** On the Select S**erver Roles interface**, click **Print and Document Services** and then click **Next.**

![](images/1-5.png)

Step 3. On the Select Features interface, click **Next.**

![](images/2-4.png)

Step 4. On the Print and Document Services interface, click **Next.**

![](images/3-4.png)

Step 5. On the Select Role Services interface, in the Role Services section, verify that the **Print Server** check box is selected and then click **Next.**

![](images/4-3.png)

Step 6. In the Confirm Installation Selections interface, click **Install.**

![](images/5-5.png)

Step 7. On the installation progress interface, click **"Close."**

![](images/6-4.png)

Step 8. On the DC-CLOUD.Windows.ae server, in the **Server** M**anager,** click **Tools**, and then click **Print Management.**

![](images/7-3-1024x569.png)

Step 9. Expand **Print Servers**, expand **DC-CLOUD (local)**, right-click **DC-CLOUD** and then click **Add Printer.**

![](images/8-3.png)

Step 10. On the Network Printer Installation Wizard interface, click **Add a TCP/IP or Web Services Printer by IP address or hostname**, and then click **Next.**

![](images/9-3.png)

Step 11. On the Printer Address interface, change the **Type of Device to TCP/IP Device.** Next, in Host name or IP address, type **172.16.1.254** (this will be your printer's network IP). **Clear Auto detect the printer driver to use**, and then click **Next.**

![](images/10-3.png)

Step 12. Under Device Type, click **Generic Network Card**, and then click Next.

![](images/11-2.png)

Step 13. On the Printer Driver interface, click **Install a new driver**, and then click **Next.**

![](images/12-3.png)

Step 14. On the Printer Installation interface, under **Manufacturer**, click **Microsoft. Under** **Printers**, click **Microsoft XPS Class Driver**, and then click Next.

![](images/13-3.png)

Step 15. On the **Printer Name and Sharing Settings** interface, change the Printer Name to **Windows Sales Printer**, and then click **Next.**

![](images/14-2.png)

Step 16. Click **Next** 2 times to accept the default printer name and share name and to install the printer.

![](images/15-2.png)

Step 17. Click **Finish** to close the Network Printer Installation Wizard.

![](images/16-2.png)

Step 18. In the Print Management console, **right-click the Windows Sales Printer**, and then click **Enable Branch Office Direct Printing.**

![](images/17-2-1024x528.png)

Step 19. Right-click the Windows Sales Printer, and then select **Properties.**

![](images/18-2.png)

Step 20. On the **Windows Sales Printer** properties, click the **Sharing** tab, select **List in the directory**, and then click **OK.**

![](images/19-2.png)

Step 21. Next, let's configure printer pooling in the Print Management console. Under SUB-01 Server, **right-click Ports**, and then click **Add Port.**

![](images/20-2.png)

Step 22. In the Printer Ports dialog box, click **Standard TCP/IP Port**, and then click **New Port.**

![](images/21-2.png)

Step 23. In the Add Standard TCP/IP Printer Port Wizard, click **Next.**

![](images/22-2.png)

Step 24. On the Add Port Interface, in **Printer Name or IP Address**, type **172.16.1.200**, and then click **Next.**

![](images/23-2.png)

Step 25. In the Additional port information required dialog box, click **Next.**

![](images/24-2.png)

Step 26.Click **Finish** to close the Add Standard TCP/IP Printer Port Wizard.

![](images/25-2.png)

Step 27. Click **Close** to close the Printer Ports dialog box.

![](images/26-2.png)

Step 28. In the Print Management console, right-click **Windows Sales Printer**, and then click **Properties.**

![](images/27-2.png)

Step 29. In the **Windows Sales Printer** Properties dialog box, click the **Ports** tab, select **Enable printer pooling**, and then click the **172.16.1.200 port** to select it as the second port.

![](images/28-2.png)

Step 30. Switch to your **client PC,** open the **Control Panel**, then click **Devices and Printers**.

![](images/29.1-2.png)

![](images/29.2-2.png)

Step 31. In the Devices and Printers console, click **Add a printer.**

![](images/30-1-1024x538.png)

Step 32. On the Add Printer interface, under Search for Available Printers, click **your** **existing printer name** and then click **Next.** Then **the wizard will install the printer driver from the DC-CLOUD. Windows server**

![](images/31-1.png)

Step 33. On the Add Printer interface (it will state that “**You’ve successfully added a Windows Sales Printer** **on** **172.16.1.254**”), click **Next.**

![](images/32-1.png)

Step 34. Click **Finish.**

![](images/33-1.png)

Step 35. Verify that you have a **Windows Sales Printer** at **172.16.1.254 that** is listed in your **Devices and Printers control panel.**

![](images/34-1-1024x547.png)

Step 36. Installation was completed successfully.

Thank You!
