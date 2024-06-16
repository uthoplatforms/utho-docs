---
title: "How to Install SSL on Windows Server"
date: "2021-12-21"
---

![](images/How-to-Install-SSL-on-Windows-Server_utho.jpg)

**Step 1: Login into your server using RDP using Administrator login details.**

![](images/pasted_image_1.png)

**Step 2: Open Information Services (IIS) Manager**

![](images/pasted_image_2-1024x533.png)

**Step 3: Select the required server and open the "Server Certificates" option.**

![](images/pasted_image_3-1024x531.png)

**Step 4: On the right hand side options, click on the "Create Certificate Request" button.**

![](images/pasted_image_4-1024x327.png)

**Step 5: Enter all the required information for the CSR and then click on Next.**

![](images/pasted_image_5-1.png)

**Step 6: Choose a bit length of 2048 and go to Step 7.**

![](images/pasted_image_8-1.png)

**Step 7: Choose the location where you want to save your CSR and click "Finish."**

![](images/pasted_image_7.png)

A **CSR TXT** file has been generated. 

**Step 8: Download the certificate from the SSL panel.**

**Step 9: After receiving the certificate, submit the SSL certificate.**

completion of the certificate.

**9.1:** Open the IIS and Server Manager and click on the Complete Certificate Request.

![](images/pasted_image_A3-1024x506.png)

**9.2:** Add the certificate and fill in all the details, then click on "OK" to complete the request.

![](images/pasted_image_A4.png)

**9.3:** The Certificate has been completely submitted.

**Step 12: Bind the certificate and add domains.**

**10.1:** Open bindings options in default web sites as shown in the below screenshot.

![](images/pasted_image_A5-1024x525.png)

**1**0**.2:** After opening the bindings , click on "add" to add the domains for the SSL.

![](images/pasted_image_A6.png)

**10.3:** Choose Type, IP, port and SSL certificate and then click on OK.

![](images/pasted_image_A7.png)

SSL has been successfully installed on the domain, please verify through the URL.

Thank you.
