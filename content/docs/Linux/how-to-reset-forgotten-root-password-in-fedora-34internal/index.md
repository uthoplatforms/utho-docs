---
title: "How to reset forgotten root Password in Fedora 34."
date: "2022-01-09"
---

![](images/How-to-reset-forgotten-root-Password-in-Fedora-34Internal.png)

**Resetting Password in Fedora 34**

**For resetting password Reboot the server and interrupt booting process by pressing up and down arrow key and press e button from keyboard**

![](images/image-16-1.png)

**After that find the line starting with linux and append that line with “rw init=/bin/bash”** **And then press Ctrl+x**

![](images/image-17-1.png)

**Now enter passwd command for resetting password**

**#passwd root**

![](images/image-18.png)

**Now reboot the system by using /sbin/reboot -f command**

**#sbin/reboot -f**

**Now After reboot you can access your server with new** password

**Thankyou** :)
