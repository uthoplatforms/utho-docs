---
title: "How to Test Internet Speed on Ubuntu 20.04"
date: "2022-10-06"
title_meta: "Test Internet Speed on Ubuntu 22.04"
description: "Learn how to test internet speed on Ubuntu 20.04 with this comprehensive guide. Follow step-by-step instructions to measure your network performance using various tools, ensuring you get accurate and reliable speed test results on your Ubuntu system.
"
keywords: ["test internet speed Ubuntu 20.04", "internet speed check Ubuntu 20.04", "measure internet speed Ubuntu 20.04", "Ubuntu 20.04 speed test tools", "Ubuntu 20.04 network speed test", "how to check internet speed Ubuntu 20.04", "Ubuntu 20.04 bandwidth test", "internet speed testing Ubuntu 20.04"]

tags: ["Test Internet Speed", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/how-to-test-internet-connection-speed-in-ubuntu-20-04/']
tab: true
---

![How to Test Internet Speed on Ubuntu 20.04](images/How-to-Test-Internet-Connection-Speed-in-Ubuntu-20.04_utho.jpg)

## Introduction

In this article, you will learn how to test internet speed on ubuntu 20.04.

It is not unusual for users to inquire about the bandwidth of their Internet connection. When using the desktop version of the [operating systems](https://en.wikipedia.org/wiki/Ubuntu)[,](https://utho.com/docs/tutorial/how-to-install-netstat-on-ubuntu-20-04-lts/) all you have to do to check the speed is type the relevant query into a search engine and observe how long it takes for the results to load on any of the websites that come up in the search. This is the only thing you need to do in order to check the speed. In contrast to this, the process will be different while utilising the server-based alternative. You will learn how to check the speed of your connection using [Ubuntu 20.04](https://utho.com/docs/tutorial/how-to-install-git-on-ubuntu-20-04/) by following the steps in this guide. When typing commands, it is imperative that one always do it as the root user.

ISPs have reported seeing traffic loads that are greater than they have ever been due to an increase in the number of individuals staying at home and spending more time on the internet. If you have observed that your network speed has slowed down at certain periods, the cause is likely a worldwide overflow.

## **Install Speedtest**

You must add the Speedtest repository in order to install it. Put in place the necessary packages first.

```
# apt install gnupg1 apt-transport-https dirmngr -y
```

**Step 1. Add a key for the repository**

```
# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 379CE192D401AB61
```

**Step 2. Add the repository itself**

```
# curl -s https://install.speedtest.net/app/cli/install.deb.sh | sudo bash
```

**Step 3. Update the packages list**

```
# apt update
```

**Step 4. The final step is to download an application that will estimate how quickly your Internet performs**

```
# apt install speedtest-cli
```

**Step 5. Internet connection speed test**

```
# speedtest-cli
```

## Conclusion

Hopefully, Now you know how to test internet speed on Ubuntu.

Thank You 🙂
