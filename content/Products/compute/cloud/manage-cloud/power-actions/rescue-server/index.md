---
weight: 30
title: "Rescue server"
page_title: "Rescue a Cloud Server on Utho Platform"
page_cardtitle: "Rescue a Cloud Server on Utho Platform"
title_meta: "Manage Cloud on the Utho Platform"
description: "Rescue your cloud instance using simply clicks on utho platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/compute/cloud/manage-cloud/power-actions/rescue-server']
icon: "config"
tab: true
---
### Introduction

Rescue mode is a critical feature for troubleshooting and repairing issues with your cloud server. This guide will walk you through the steps to put your cloud server into rescue mode on the Utho platform. This can be particularly useful if your server is unresponsive or has critical issues preventing it from booting normally.

### What You Need

Before starting, ensure you have:

* An active Utho account.
* Proper access rights to the cloud instance you want to rescue.

### Step-by-Step Guide

##### 1. Log In to the Utho Platform

First, navigate to the Utho platform and log in with your credentials.

##### 2. Navigate to Your Cloud Instances

Once logged in, go to the 'Cloud' section from the main dashboard. Here, you'll see a list of all your cloud instances.

##### 3. Select the Instance

Click on the cloud instance you want to put into rescue mode. This will take you to the instance's details page.

##### 4. Access the Power Tab

On the instance's details page, locate the 'Power' tab.

##### 5. Initiate Rescue Mode

In the 'Power' tab:

1. Find the 'Rescue Server' option.
2. Click on the 'Rescue Server' button.

   ![1719311996042](image/index/1719311996042.png)

### What Happens During Rescue Mode

* **Booting into Rescue Environment:** The server will reboot into a special rescue environment instead of its usual operating system.
* **Access for Troubleshooting:** This environment allows you to access the server's file system and perform troubleshooting steps without the usual OS running.

### Common Uses for Rescue Mode

* **Fixing Boot Issues:** Repair boot loader problems or corrupted system files.
* **Data Recovery:** Access and recover important data from the server.
* **Resetting Passwords:** Reset forgotten or lost administrative passwords.
* **Diagnosing Hardware Issues:** Identify and troubleshoot hardware-related issues.

### Additional Tips

* **Backup Data:** Regularly back up your data to prevent data loss in case of server issues.
* **Familiarize with Rescue Tools:** Know the tools available in the rescue environment for effective troubleshooting.
* **Document Changes:** Keep a record of any changes made during the rescue process for future reference.

### Troubleshooting

If you encounter issues during rescue mode:

* **Check Logs:** Review the server logs for any errors or issues that might indicate the problem.
* **Verify Rescue Environment:** Ensure the rescue environment is properly loaded and accessible.
* **Contact Support:** If problems persist, contact the Utho support team for assistance.

### Conclusion

Using the rescue mode on the Utho platform is a powerful tool for troubleshooting and repairing your cloud server. By following this guide, you can effectively address critical issues and restore your server to normal operation.
