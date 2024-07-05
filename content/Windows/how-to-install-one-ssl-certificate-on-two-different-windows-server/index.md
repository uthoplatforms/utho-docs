---
title: "HOW TO INSTALL ONE SSL CERTIFICATE ON TWO DIFFERENT WINDOWS SERVER"
date: "2022-06-22"
Title_meta: GUIDE TO Install One SSL Certificate on Two Different Windows Servers

Description: This guide provides instructions on how to install one SSL certificate on two different Windows servers. Learn how to generate or obtain the SSL certificate, export it with private key, import it to another server, and configure bindings to secure multiple servers with the same SSL certificate.

Keywords: ['SSL certificate', 'Windows Server', 'install SSL certificate', 'server security', 'certificate management', 'SSL configuration']

Tags: ["SSL Certificate", "Windows Server", "Install SSL Certificate", "Server Security", "Certificate Management", "SSL Configuration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: [/Windows/how-to-install-one-ssl-certificate-on-two-different-windows-server']
tab: true
---

![](images/HOW-TO-INSTALL-ONE-SSL-CERTIFICATE-ON-TWO-DIFFERENT-WINDOWS-SERVER_utho.jpg)

**Introduction :**  
SSL (Secure Sockets Layer) and its successor, TLS (Transport Layer Security), are protocols for establishing authenticated and encrypted links between networked computers. We can have secure communication over the internet using SSL.

**Prerequisite**

1. windows server with administrator permission

3. One Fully qualified domain name

5. IIS should be installed on the server

7. A SSL certificate

**Step 1:** The first thing , we have to do that, we need to generate a CSR certificate from the server, where we have to install the SSL certificate. We can generate the CSR certificate in the IIS ( Internet Information Services ) in windows server. Please have a look on the below screenshot for your reference.

![](images/ss1-1024x470.png)

**Step 2**: We have to click the option " Click Certificate Request" given in the write hand side of the screenshot. 

![](images/ss2-1024x491.png)

**Step 3**: Once you will click on the " Click Certificate Request", you will get the new prompt as per the above screenshot . We need to fill all the required details correctly as per the below screenshot and then click on the next button.

![](images/ss3-1024x608.png)

**Step 4**  : While clicking on the next button , a new prompt will appear, there we have to select the encryption bit. It can be of your choice. Usually we create it using 2048 bit.

![](images/ss4-1024x477.png)

**Step 5** : While clicking on the next button , a new prompt will appear, there we have to give the CSR file storage location . Weather, we can create a text file prior the installation or we can defile the file at that moment also.  

![](images/ss5.png)

**Step 6** : Once you will click on finish , a CSR certificate will be create. We can view the CSR file content while opening the file using notepad or with any text editor.

![](images/ss6-1024x668.png)

**Step 7**: Now we have to share this CSR certificate with the concerned person to generate the SSL certificate. Once we will receive the certificate, we will proceed with the installation. In IIS there is a option of " Complete Certificate Request" just below the CSR option. We need to click on that, afterward a new prompt will appear where you need to select the .crt file afterward enter the domain name and then click on OK button. 

![](images/ss10-1024x396.png)

**Step 8**: Now as per the above screenshot, we have successfully completed the installation of the certificate on the server. Next we will see how we can import and export the certificate.

\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*\*

HOW TO EXPORT AND IMPORT SSL CERTIFICATE USING IIS (Internet Information Services)

**Step 1**: Firstly we need to export the certificate, so we will move to the SERVER CCERTIFICATE option of IIS. There we need to select the certificate which we want to export as .pfx file . While exporting we have to assign a password for the certificate which will be used while importing the ssl in another server.

Please have a look on the below screenshot.

![](images/ss8.png)

**Step 2:**  One the certificate  will be exported we need to import it on another server, where we want to install that ssl certificate. We can find the import option in the Server Certificate . We have to browse to the path of .pfx file then we need to enter the password which had been generated while exporting the ssl certificate. Please have a look on the screenshot.

![](images/ss11-1024x426.png)

We have completed the export and import task of ssl certificate now.

Thank you :)
