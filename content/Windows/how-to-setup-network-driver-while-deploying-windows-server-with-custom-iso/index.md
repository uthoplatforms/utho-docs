---
title: "How to setup Network Driver while deploying Windows Server with custom ISO"
date: "2023-01-03"
---

![](images/How-to-setup-Network-Driver-while-deploying-Windows-Server-with-custom-ISO_utho.jpg)

Step 1. After installing OS using custom ISO, login to Microhost.com and go to ISO section.

![](images/Screenshot_1-21-1024x502.png)

Step 2. Upload Virtio Network Adapter ISO

LINK: [Virtio Win ISO](https://github.com/virtio-win/virtio-win-pkg-scripts/blob/master/README.md)

![](images/Screenshot_2-27-1024x377.png)

Step 3. Check if the ISO is downloaded properly.

![](images/Screenshot_3-21.png)

Step 4. Go to Mnage Cloud and Click on ISO sub-section

![](images/Screenshot_5-19-1024x460.png)

Step 5. Select Virtio ISO and click on "Mount and Boot from ISO"

![](images/Screenshot_6-17-1024x357.png)

![](images/Screenshot_9-13-1024x448.png)

Step 6. After the ISO is mounted and server is booted from the ISO, go to **Console** and open **This PC** > Open Virtio Driver

![](images/Screenshot_10-8.png)

Step 7. Double click on "virtio-win-guest-tools"

![](images/Screenshot_11-8.png)

Step 8. Proceed with the installation process as follows:

![](images/Screenshot_12-7.png)

![](images/Screenshot_13-6.png)

![](images/Screenshot_14-4.png)

![](images/Screenshot_15-4.png)

Network Driver installed once you see the following icon.

![](images/Screenshot_16-3.png)

Now follow the below mentioned document to configure IP manually on windows Server.

URL: [How to configure IP manually on Windows Server.](https://utho.com/docs/tutorial/how-to-configure-ip-manually-on-windows-server/)

Thank You.
