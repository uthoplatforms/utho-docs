---
title: "How to host a domain on Windows Server 2019"
date: "2021-12-19"
Title_meta: GUIDE TO Host a Domain on Windows Server 2019

Description: Learn how to host a domain on Windows Server 2019 with this detailed guide. Explore step-by-step instructions to configure DNS (Domain Name System), set up web hosting services, manage domain settings, and ensure proper domain resolution and accessibility on your Windows Server 2019 environment.

Keywords: ['Windows Server 2019', 'host a domain', 'DNS configuration', 'web hosting', 'domain management', 'server administration']

Tags: ["Windows Server 2019", "Host a Domain", "DNS Configuration", "Web Hosting", "Domain Management", "Server Administration"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-host-a-domain-on-windows-server-2019/']
---

![](images/How-to-host-a-domain-on-Windows-Server-2019_utho.jpg)

**Prerequisites**

1. Windows Server

2\. IIS

3\. Domain (html files)

**Step 1. Login into your server via RDP.**

![](images/Screenshot_1-11.png)

**Step 2. Open IIS (Install IIS if you don’t have it on your system)**

Link on How to Install **IIS:**

[Install IIS via Powershell](https://utho.com/docs/tutorial/how-to-install-iis-via-powershell-in-windows-server/)

[Install IIS through GUI](https://utho.com/docs/tutorial/installation-and-configuration-of-iis-web-server-on-windows-server/)

![](images/Screenshot_1_-1024x576.png)

Step 3. Paste your domain files in a folder in this location **C:\\inetpub\\wwwroot**

Naming the folder according to your domain name We will name it "**mysite**"

![](images/Screenshot_2_-1024x576.png)

Step 4. Go to IIS > Sites and click on **Add Site.**

![](images/Screenshot_3_-1024x576.png)

Input your site name, physical path, and hostname. Click OK.

![](images/Screenshot_4_-1024x576.png)

**Step 5. Go to this location: C:\\Windows\\System32\\drivers\\etc  ....and edit the Hosts file.**

![](images/Screenshot_6_-1024x576.png)

**Step 6. Go to IIS and click on "Browse mysite.local"**

![](images/Screenshot_5_-1024x576.png)

Your domain will open in the browser.

![](images/Screenshot_7_-1024x576.png)

Thank you!!
