---
title: "Create a Zabbix action to deliver an alert message to the user"
date: "2023-05-27"
---

![Create a Zabbix action to deliver an alert message to the user.](images/Create-a-Zabbix-action-to-deliver-an-alert-message-to-the-user-1024x576.png)

Description

Create a Zabbix action to deliver an alert message to the user. [zabbix](https://en.wikipedia.org/wiki/Zabbix) is a free and open-source monitoring software application for various IT components such as networks, servers, virtual machines (VMs), and cloud services. Monitoring indicators provided by Zabbix include network usage, CPU load, and disc space utilisation.

Follow the below steps to Create a [Zabbix](https://utho.com/docs/tutorial/how-to-install-zabbix-agent-on-centos-7/) action to deliver an alert message to the user.

You must first read this article so that you can appropriately configure email so that emails can be sent to the specific user:- [https://utho.com/docs/tutorial/add-user-and-give-limited-permission-to-the-host-in-zabbix/](https://utho.com/docs/tutorial/add-user-and-give-limited-permission-to-the-host-in-zabbix/)

## Configuring an action

Overview

Actions are something that need to be configured if you want certain processes to take place as a result of events (like alerts being delivered, for instance).

Follow these steps if you want to configure an action:

- Navigate to Alerts --- Actions and select the appropriate action type from the submenu (you may change the type later using the title dropdown).

- "Click the Create action" button."

- Call out the action.

- Select the conditions under which operations are performed.

- Choose which operations to do.

General action attributes:

![](images/image-1084-1024x269.png)

Finally, navigate to operations and create an email alert in the manner outlined in the following picture.

![](images/image-1085-1024x511.png)

Finally, we carefully established all of the processes if the alerts occur in Zabbix, so that you will receive email as your specified email address.

Must read:- [https://utho.com/docs/tutorial/add-user-and-give-limited-permission-to-the-host-in-zabbix/](https://utho.com/docs/tutorial/add-user-and-give-limited-permission-to-the-host-in-zabbix/)
