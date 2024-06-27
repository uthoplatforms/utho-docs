---
weight: 30
title: Manage Firewall
title_meta: "Manage Firewall on the Utho Platform"
description: "The Manage section allows users to view and update the configuration of their deployed Firewall. This section provides a comprehensive interface to manage Firewall users, configure firewalls, and destroy Firewall."
keywords: ["firewall", "security"]
tags: ["utho platform","firewall"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ['/products/networking/Firewall/manage-firewall']
icon: guides
tab: true
---
## Firewall Configuration Info

At the top of the Manage section, users can view the configuration information of the selected Firewall. This includes:

![Utho-Manage-firewall-config](image/Utho-Manage-firewall-config.png)

* **Firewall Name:** The unique name assigned to the Firewall.
<!-- * **Upload Rule:** Select a user from the list, click the **Upload Rule** button, which you will upload firewall rule into the firewall.
* **Export Rule:** Select a user from the list, click the **Export Rule** button, which you will export firewall rule from the firewall.
* **Rule:** Select a user from the list, click the **Rule** button, which you will add firewall rule manually into the firewall.
* **Sever:** Select a user from the list, click the **Sever** button, which you will add the selected firewall to that server.
* **Destroy:** Select a user from the list, click the **Sever** button, which you will add the selected firewall to that server.
* **Firewall Name:** The unique name assigned to the Firewall.
* **Firewall Name:** The unique name assigned to the Firewall.
* **Firewall Name:** The unique name assigned to the Firewall.
* **Firewall Name:** The unique name assigned to the Firewall.
* **Firewall Name:** The unique name assigned to the Firewall. -->

## Add Firewall Rules

In the Add Firewall Rules Section, users have the ability to add firewall and delete firewall. This section provides the following functionalities:


![Utho-Manage-firewall-rules](image/Utho-Manage-firewall-rules.png)

* **Add Firewall:** Click the **Add Firewall** button firewall will added to incoming and outgoing rules list after give reqiured details such as service, protocal, port range, source.
* **Delete:** Select a user from the list and click the **Remove** button to remove the incoming and outgoing rules from the Firewall.

## Upload Firewall Rules

In the Upload Firewall Rules Section, users have the ability to uploads input and outgoing firewall rules. This section provides the following functionalities:


![Utho-Manage-upload](image/Utho-Manage-upload.png)

* **Attach File:** Click the **Attach File** button you will upload file after select the rules type like incoming or outgoing and file type like .csv,.pdf,.xlsv.

## Export Firewall Rules

In the Export Firewall Rules Section, users have the ability to export input and outgoing firewall rules. This section provides the following functionalities:


![Utho-Manage-firewall-export](image/Utho-Manage-firewall-export.png)

* **Export Rule:** Click the **Export Rule** button you will export file like .csv,.pdf,.xlsv, which contain incoming and outgoing rules.

## Server

In the Server section, users have the ability to add a firewall to the server. This section provides the following functionalities:


![Utho-Manage-firewall-server](image/Utho-Manage-firewall-server.png)

* **Add Server:** Click the **Add Server** button to add a firewall to the selected server from the dropdown menu.
* **View Cloud:** Click the **View Cloud** button to open the manage section of that cloud.
* **Delete:** Click the **Delete** button to remove the firewall from the server.
## Destroy

In the Destroy section, users can terminate the Firewall. This action is irreversible and will permanently delete the Firewall and all associated data. To destroy a Firwall

![Utho-Manage-firewall-destroy](image/Utho-Manage-firewall-destroy.png)

Click the **Destroy Firwall** button.

##### **Confirmation:**

![Utho-Manage-firewall-popup](image/Utho-Manage-firewall-popup.png)

A confirmation dialog will appear. Confirm the action to proceed with destroying the Firewall.

When you provide the confirmation then your Firewall Instance will destroy.
