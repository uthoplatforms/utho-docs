---
title: "How to test internet speed on Debian 12"
date: "2023-08-05"
---

![How to test internet speed on Debian 12](images/How-to-test-internet-speed-on-Debian-12-1-1-1024x576.jpg)

## Introduction

In this article, you will learn how to test internet speed on Debian 12.

It is not unusual for users to inquire about the bandwidth of their [Internet connection](https://en.wikipedia.org/wiki/Speedtest.net). When using the desktop version of the operating systems, all you have to do to check the speed is type the relevant query into a search engine and observe how long it takes for the results to load on any of the websites that come up in the search. This is the only thing you need to do in order to check the speed. In contrast to this, the process will be different while utilising the server-based alternative. You will learn how to check the speed of your connection using Debian 12 by following the steps in this guide. When typing commands, it is imperative that one always do it as the root user.

ISPs have reported seeing traffic loads that are greater than they have ever been due to an increase in the number of individuals staying at home and spending more time on the internet. If you have observed that your network speed has slowed down at certain periods, the cause is likely a worldwide overflow.

## **Install Speedtest**

**You must add the Speedtest repository in order to install it. Put in place the necessary packages first.**

```
# apt install gnupg1 apt-transport-https dirmngr -y

```

**Step 1. Add a key for the repository**

```
# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 379CE192D401AB61

```

**Step 2. Add the repository itself**

```
# curl -s https://install.speedtest.net/app/cli/install.deb.sh | sudo bash

```

**Step 3. Update the packages list**

```
# apt update

```

**Step 4. The final step is to download an application that will estimate how quickly your Internet performs**

```
# apt install speedtest-cli

```

**Step 5. Internet connection speed test**

```
# speedtest-cli

```

![How to test internet speed on Debian 12](images/image-1054.png)

## Conclusion

Hopefully, Now you know how to test internet speed on Debian 12.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
