---
title: "How to reset forgotten root password in centos 7/8"
date: "2020-07-29"
title_meta: "reset forgotten root password"
description: ' This guide outlines steps to regain access to your CentOS system by resetting your forgotten root password. The process involves booting into single-user mode, modifying the GRUB configuration, and changing the root password within a temporary environment..'

keywords:  [' CentOS 7, CentOS 8, root password reset, single-user mode, chroot.']
tags: ["reset forgotten root password" , "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-reset-centos-7-8-root-password/']
tab: true
---

![](images/How-to-reset-forgotten-root-password-in-centos-7_8_utho.jpg)

First, reboot or power on your **CentOS 8** system. Select the kernel that you want to boot into. Next, press `‘e’` to interrupt the boot process and make changes.

![](https://lh6.googleusercontent.com/jEzWTBQhXTmtrl8nq6Hqk62jW78nOzHxI3FMqLFLc6_ILEBbbfE1N8Iphn1n4otT1f0bb3hsjcu12Dg1B2Fu9i4-pGpCq-fS21yOS5Kkj6Wmx84xoiH9KMoWhyBF2tI9tUOIOWiQ)

Replace the kernel parameter `ro` with `rw` and append an extra kernel parameter `init=/sysroot/bin/sh`.

![](https://lh3.googleusercontent.com/FrtOMxvirR7KmGE7KyDa-Jn4bACzdtkmShseP3WHwRAeBzFDn0Dvix6U_nW86W6q6Euj4xk_WqCtwqFjlrZtr1Ztp1LApTmzgG0huZHRBkkxy6lSztseeeWIJkzfvQuguryiZnBO)

Press **Ctrl+x** combination on the keyboard to enter single-user mode.

Next, run the command below to mount the root file system in read and write mode.

```
chroot /sysroot
```

![](https://lh3.googleusercontent.com/NkHbMpkgrRv7S7Lht5Dmo53EkqqJUUE5GYmpl7f3xKRNoTuTBL2PMOw3vWaUQl6uvi1nqmFvOKSyQsTYNV2XI36VWwc8mCjL-5fqCMH8xULzsYeZ5sSxQGHAurpXpQOHe-UHlB-x)

You can now change the root password by executing the command:

```
passwd
```

![](images/1-1.png)

To apply the changes, exit and reboot the CentOS 8 system.

![](images/2.png)

Thankyou.
