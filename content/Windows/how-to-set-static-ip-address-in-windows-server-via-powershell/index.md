---
title: "How to Set Static IP Address in Windows Server via PowerShell"
date: "2023-06-24"
---

## INTRODUCTION Set Static IP Address

A static IP address is a 32 bit number assigned to a computer as an address on the internet. This number is in the form of a [dotted quad](https://support.google.com/fiber/answer/3547208?hl=en) and is typically provided by an internet service provider (ISP). In this tutorial, we will learn how to Set Static IP Address in Windows Server via PowerShell.

#### Prerequisites

- [Windows Server](https://utho.com/docs/tutorial/how-to-install-active-directory-domain-service-on-windows-server/?preview_id=11159&preview_nonce=171803715d&preview=true)

- PowerShell with Administrator rights

- Internet connectivity

Step 1. Login to your Windows Server

Step 2. Open PowerShell as an Administrator

![](images/Screenshot_11-25.png)

**Step 3. Run the following command to get Network Interface**

![Set Static IP Address](images/Screenshot_1-43.png)

![Set Static IP Address](images/Screenshot_2-53-1024x305.png)

**Step 4. Run the following command to set DHCP off**

![](images/Screenshot_3-43-1024x348.png)

**Step 5. Run the following command to set DNS**

![Set Static IP Address](images/Screenshot_4-41-1024x57.png)

![](images/Screenshot_5-32.png)

_PS: 10.0.0.10 is just a test IP._

**Step 6. **Run the following command to**** **confirm settings**

![Set Static IP Address](images/Screenshot_6-33.png)

Thank You.
