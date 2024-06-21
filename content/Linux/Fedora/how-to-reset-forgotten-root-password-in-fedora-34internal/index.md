---
title: "How to reset forgotten root Password in Fedora 34."
date: "2022-01-09"
title_meta: "GUIDE TO reset forgotten root Password on Fedora 34"
description: "A step-by-step guide on how to reset a forgotten root password in Fedora 34, a Linux distribution."

keywords: ['Fedora 34', 'reset root password', 'forgotten password', 'Linux', 'system administration']

Tags: ["reset forgotten root Password", "fedora"]
icon: "fedora"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/Fedora/how-to-reset-forgotten-root-password-in-fedora-34internal/']
tab: true
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
