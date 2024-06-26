---
title: "How to migrate accounts from CWP to CWP"
date: "2022-08-25"
---

![How to migrate accounts from CWP to CWP](images/How-to-migrate-accounts-from-CWP-to-CWP-3-1024x576.png)

## Introduction

In this article, you will learn how to migrate accounts from CWP to CWP.

## **what is cwp?**

Control Web Panel – an AI powered Free Web Hosting control panel designed for quick and easy management of (Dedicated & VPS) as well as offering an intuitive and modern interface for users, as a web hosting panel.

## **SOURCE SERVER**

1.  Go to “CWP Settings” > "API Manager” in the left menu.

![](images/api.png)

2. Generate and save the new API key by using the green button “Allow New Api Access”

![](images/select.png)

IP origin: IP destination

Click on **Generate code**

Format request: [**JSON**](https://en.wikipedia.org/wiki/JSON)

Enable function for: **CWP to CWP Migration**, then click on **Create**

3. Go to **Dashboard > Firewall** and whitelist the **Source IP** and **Destination IP**

![](images/desti.png)

Under **Whitelist Configuration**, Click on **Add an entry**: Source IP and DestinationIP

![](images/white.png)

4. Search **SSH Key Generator** in the left search box, click on **Generate an SSH key**, from the Server “**Settings**” module -> “**[SSH Key](https://utho.com/docs/tutorial/how-to-access-a-server-through-password-less-authentication/) Generator**”

Generate **new key** and **Add a Public key** to authorized

## **DESTINATION SERVER**

1. Login to CWP of destination server

2\. Go in firewall then whitelisted the Source IP and Destination IP

![](images/white1.png)

3\. Generate an SSH key, from the Server “Settings” module -> “SSH Key Generator”

4\. Generate new key and Add a Public key to authorized

5\. Login destination server via putty, then

ssh-copy-id root@x.x.x.x > source IP

Password:yxyxyxyx

6\. User accounts> CWP->CWP migration

![](images/migration.png)

Server IP: source IP

7\. Api Key cPanel: Paste key which is generated in source server  
Maximum simultaneous transfers: according to you select maximum number of accounts transfer

Test and save

![](images/save.png)

8\. Click on the authentication method,and select what you want to migrate

9\. And start migration

**NOTE**: you can check the migration log with the following command

```
# *tail -f /var/log/cwp/account_transfer.log*-
```

## Conclusion

Hopefully, you have learned how to migrate accounts from CWP to CWP.

Thank You 🙂
