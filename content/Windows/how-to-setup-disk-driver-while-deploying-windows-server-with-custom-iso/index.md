---
title: "How to setup Disk Driver while deploying Windows Server with custom ISO"
date: "2022-07-27"
Title_meta: GUIDE TO Setup Disk Driver When Deploying Windows Server with Custom ISO

Description: This guide provides detailed instructions on how to set up disk drivers during the deployment of Windows Server using a custom ISO. Learn how to integrate necessary disk drivers into the installation media, ensure compatibility with hardware configurations, and troubleshoot common driver installation issues.

Keywords: ['Disk driver setup', 'Windows Server deployment', 'custom ISO', 'hardware compatibility', 'driver integration', 'installation troubleshooting']

Tags: ["Disk Driver Setup", "Windows Server Deployment", "Custom ISO", "Hardware Compatibility", "Driver Integration", "Installation Troubleshooting"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-setup-disk-driver-while-deploying-windows-server-with-custom-iso']
tab: true
---

![](images/How-to-setup-Disk-Driver-while-deploying-Windows-Server-with-custom-ISO_utho.jpg)

Step 1. Download ISO

Step 2. Deploy a server using the ISO

Step 3. Open console to install OS

Step 4. Begin OS isntallation

![](images/Screenshot_1-14.png)

Click "I accept the license terms"

Step 5. Select "Custom"

![](images/Screenshot_2-21.png)

step 6. Select "Load Driver"

![](images/Screenshot_3-15.png)

Step 7. Click on "Browse"

![](images/Screenshot_4-16.png)

Step 8. Search for "Cd Drive (E:) virtio-win-version"

![](images/Screenshot_5-13.png)

Step 9. Search for the OS version same as your ISO OS version, select "and64" for 64bits

![](images/Screenshot_6-12.png)

Step 10. Red Hat Virtio SCSI controller will appear, selct that and click "next"

![](images/Screenshot_7-10.png)

Step 11. Then select "Windows Server Standard Evolution (Server with a Graphical Interface)"

![](images/Screenshot_8-12.png)

Step 12. Allocate Drive (Click on 'New')

![](images/Screenshot_9-11.png)

Step 13. Select "Primary Drive" and click on 'Next'

![](images/Screenshot_10-5.png)

Step 14. OS installation begins.

![](images/Screenshot_11-7-1024x751.png)

Thank You!
