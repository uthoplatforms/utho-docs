---
title: "How to install Elinks on CentOS"
date: "2023-07-18"
title_meta: "Guide for Install Elinks on Centos7"
description: "This guide details the steps to install Elinks, a free and lightweight text-based web browser, on your CentOS system. It covers enabling the PowerTools repository (as Elinks is not in the default repositories), using the yum package manager to install Elinks, and launching the browser.
"
keywords: ['Elinks', 'CentOS', 'text-based web browser', 'console browser', 'lightweight browser', 'PowerTools repository', 'yum']

tags: ["Elinks", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-install-elinks-on-centos/']
tab: true
---

![How to install Elinks on CentOS](images/How-to-Install-Elinks-on-CentOS-1-1024x576.jpg)

## Introduction

In this article, you will learn how to install Elinks on CentOS.

[Elinks](https://en.wikipedia.org/wiki/ELinks) is a free text-based web browser that is compatible with operating systems that are similar to Unix. In the latter half of 2001, Petr Baudi created a fork of the Links Web browser that he referred to as the E branch for its focus on experimentation. Since then, the E has come to represent either Enhanced or Extended in many contexts.

Elinks is a text-mode Web browser that can show colours, render tables, download in the background, be set up with a menu, search with tabs, and use thin code. Frames are okay. Various file types can be linked to external readers. Mail-to and telnet are supported by viewers on the outside.

## CentOS Elinks Web browser installation

**Step 1: We will use the following command to update our operating system.**

```
# yum update -y

```

**Step 2: The Elinks package can be found in the CentOS repository. It is not only the easiest way but also the best way to load a package from a distribution's main repository. To start the installation, use the command below.**

```
# yum install elinks -y

```

**Step 3: Start the Internet browser. Now that we've installed the browser, we'll run this command and see what happens.**

```
# elinks

```

**The following screen will show up on your terminal when the link is made. You will get a browser instead of an actual computer. It will be fully functional and show all of the text on a page.**

**Step 4: Press "OK"**

![How to install Elinks on CentOS](images/1-30.png)

**Now we have successfully installed the Elinks web browser on our CentOS operating system.**

**Step 5: Type the URL and then press "OK".**

![How to install Elinks on CentOS](images/2-23.png)

**After pressing OK, below content will show in your terminal.**

![install Elinks on CentOS](images/image-1209.png)

## Conclusion

Hopefully, now you have learned how to install Elinks on CentOS.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
