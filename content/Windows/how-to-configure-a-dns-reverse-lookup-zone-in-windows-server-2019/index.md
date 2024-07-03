---
title: "How to configure a DNS Reverse Lookup Zone in Windows Server 2019"
date: "2021-12-20"
Title_meta: GUIDE TO Configure DNS Reverse Lookup Zone in Windows Server 2019

Description: This guide provides detailed instructions on how to configure a DNS Reverse Lookup Zone in Windows Server 2019. Learn the steps to create and manage Reverse Lookup Zones, enabling efficient IP address to hostname resolution and enhancing network management capabilities.

Keywords: ['Windows Server 2019', 'DNS', 'Reverse Lookup Zone', 'IP address resolution', 'network management']

Tags: ["Windows Server 2019", "DNS", "Reverse Lookup Zone", "IP Address Resolution", "Network Management"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-configure-a-dns-reverse-lookup-zone-in-windows-server-2019']
---

![](images/How-to-configure-a-DNS-Reverse-Lookup-Zone-in-Windows-Server-2019_utho.jpg)

**Step 1. Login to your win server via RDP**

![](images/Screenshot_1-1-3.png)

**Step 2. Open Server Manager**

![](images/Screenshot_8_-1024x555.png)

**Step 3. Go to tools and open DNS** 

![](images/Screenshot_10_-1024x523.png)

**Step 4. In the DNS Manager, under your server, right-click on Reverse Lookup zones, and click on New Zone...**

![](images/Screenshot_11_.png)

**Step 5. In NEW ZONE wizard, click next**

![](images/Screenshot_12_.png)

**Step 6. Select Primary Zone and click Next.**

![](images/Screenshot_13_.png)

**Step 7. Select IPv4 Reverse Lookup Zone and click Next.**

![](images/Screenshot_14_.png)

**Step 8. Select Network ID, input your server IP and click Next.**

![](images/Screenshot_15_.png)

**Step 9. Select Create a new file name. A file name should automatically be created. Click Next.**

![](images/Screenshot_16_.png)

**Step 10. Click Finish.**

![](images/Screenshot_17_.png)

**Step 11. Expand Reverse Lookup Zones and click on the zone you just added.**

![](images/Screenshot_18_.png)

**Step 12. Open cmd and input the command:**

```
 C:UsersAdminnistrator>**ipconfig /registerdns** 
```

![](images/Screenshot_1-12.png)

![](images/Screenshot_2-16.png)

**Step 13. Add the pointer. Go to DNS Manager, right click on the mid screen, and click on "New Pointer (PTR)...".**

![](images/Screenshot_19_.png)

**Step 14.Add Host IP Address and Hostname, then click OK.**

![](images/Screenshot_20_.png)

Pointer Added

![](images/Screenshot_21_.png)

**Step 15. Exit your server and go to your main desktop. Open cmd and type the command** 

```
 C:UsersAdministrator> **nslookup your_server_ip** 
```

Example:

```
 C:UsersDivyanshu>**nslookup 103.127.30.85**
```

```
Name: 1031273085.network.microhost.in  
Address: 103.127.30.85
```

Thank you!
