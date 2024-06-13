---
title: "How to Generate CSR Certificate for SSL Installation in Windows Server"
date: "2022-06-22"
---

**Step 1: Login into your server using RDP using Administrator login details.**

![](images/pasted_image_1-1.png)

**Step 2: Open Information Services (IIS) Manager**

![](images/pasted_image_2-1-1024x533.png)

**Step 3: Select the required server and open the "Server Certificates" option.**

![](images/pasted_image_3-1-1024x531.png)

**Step 4: On the right-hand side options, click on the "Create Certificate Request" button.**

![](images/pasted_image_4-1-1024x327.png)

**Step 5: Enter all the required information for the CSR and then click on Next.**

![](images/pasted_image_5-2.png)

**Step 6: Choose a bit length of 2048 and go to Step 7.**

![](images/pasted_image_8-2.png)

**Step 7: Choose the location where you want to save your CSR and click "Finish."** The CSR.txt file will be saved at your preferred location.

![](images/pasted_image_7-1.png)

A **CSR.txt** file has been generated.
