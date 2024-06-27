---
weight: 30
title: "Hard Reboot"
page_title: "How to Perform a Hard Reboot of a Cloud Server on Utho Platform"
page_cardtitle: "How to hard reboot Cloud Server on Utho Platform"
title_meta: "How to Perform a Hard Reboot of a Cloud Server on Utho Platform"
description: "Hard Reboot your cloud instance using simply clicks on utho platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/compute/cloud/manage-cloud/power-actions/hard-reboot']
icon: "config"
tab: true
---
### Introduction

A hard reboot is sometimes necessary to resolve critical issues with a cloud server. This process involves stopping and restarting the server, which can help clear problems that can't be fixed through a simple restart. This guide will walk you through the steps to perform a hard reboot on the Utho platform.

### What You Need

Before starting, ensure you have:

* An active Utho account.
* Proper access rights to the cloud instance you want to reboot.

### Step-by-Step Guide

#### 1. Log In to the Utho Platform

First, navigate to the Utho platform and log in with your credentials.

#### 2. Navigate to Your Cloud Instances

Once logged in, go to the 'Cloud' section from the main dashboard. Here, you'll see a list of all your cloud instances.

#### 3. Select the Instance

Click on the cloud instance you want to perform a hard reboot on. This will take you to the instance's details page.

#### 4. Access the Power Tab

On the instance's details page, locate the 'Power' tab.

#### 5. Perform the Hard Reboot

In the 'Power' tab:

1. Find the 'Hard Reboot' option.
2. Click on the 'Hard Reboot' button.
3. ![1719309632933](image/index/1719309632933.png)

### What Happens During a Hard Reboot

* **Server Stop and Restart:** The server will immediately stop and then start again. This process forcibly shuts down the server and reinitializes it.
* **Potential Downtime:** There will be a brief period of downtime while the server stops and restarts. This can impact any services or applications running on the server.
* **Data Integrity:** Ensure that all critical data is saved, as unsaved data might be lost during a hard reboot.

### When to Use a Hard Reboot

* **Unresponsive Server:** When the server is completely unresponsive and doesn't respond to standard restart commands.
* **Severe Issues:** To resolve severe issues that cannot be fixed by a soft reboot or other troubleshooting methods.

### Additional Tips

* **Regular Backups:** Always maintain regular backups of your data to prevent loss during unexpected reboots.
* **Monitor Performance:** After performing a hard reboot, monitor the server to ensure it returns to normal operation.

### Troubleshooting

If the server does not restart properly or you encounter issues:

* **Check Logs:** Review the server logs for any errors or issues during the reboot process.
* **Verify Configuration:** Ensure that all configurations are correct and up-to-date.
* **Contact Support:** If problems persist, contact the Utho support team for assistance.

Performing a hard reboot on the Utho platform is a straightforward process that can help resolve critical issues with your cloud server. By following this guide, you can safely and effectively reboot your server, ensuring minimal downtime and disruption.
