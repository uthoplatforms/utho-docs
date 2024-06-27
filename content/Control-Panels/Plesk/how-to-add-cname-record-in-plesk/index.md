---
title: "How to add CNAME record in Plesk"
date: "2023-01-04"
homecard: true
title_meta: "How to add CNAME record in Plesk"
description: "Learn How do add CNAME record in Plesk"

keywords: ['CNAME record', 'plesk']

tags: ["CNAME record", "plesk"]
icon: "plesk"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Control-Panels/Plesk/how-to-add-cname-record-in-plesk/']
tab: true
---

![How to add CNAME record in Plesk](images/How-to-add-CNAME-record-in-Plesk_utho.jpg)

## Introduction

In this article, you will learn how to add CNAME record in Plesk.

Inside the Domain Name System ([DNS](https://en.wikipedia.org/wiki/Domain_Name_System)), there is a specific kind of resource record known as a Canonical Name (CNAME) record. This record transfers one domain name (an alias) to another (the canonical name).

Step 1. Log into your Plesk with your server password by searching server\_ip:8880 in your browser.

![command output](images/image-679-1024x367.png)

Step 2. Go to Hosting and DNS under the menu of websites and domains, then click on DNS settings.

![command output](images/image-735-1024x485.png)

Step 3. Click on "Add Record."

![command output](images/image-736-1024x485.png)

Step 4. Select CNAME record from the record type drop-down menu.

Enter the subdomain name in the domain name field; in our case, we used www.

In the TTL field, you can assign any value in seconds; we have entered 3600.

In the canonical name field, I entered the canonical name as micro.com for the main domain. then select OK. 

NOTE: Use your main domain instead of micro.com.

![add CNAME record in Plesk](images/image-740-1024x484.png)

## Conclusion

Hopefully, now you have learned how to add CNAME record in Plesk.

Also read: [How to add MX record in Plesk.](https://utho.com/docs/tutorial/how-to-add-mx-record-in-plesk/)

Thank You 🙂
