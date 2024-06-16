---
title: "How to install tcpping on CentOS"
date: "2023-07-18"
---

![How to install tcpping on CentOS](images/How-to-install-tcpping-on-CentOS-1-1024x576.jpg)

## Introduction

In this article, you will learn how to install tcpping on CentOS.

The TCPPING protocol starts with a group of known members and then [pings](https://en.wikipedia.org/wiki/Ping_(networking_utility)) them one by one in order to discover new ones.

There are situations that may arise in which an external server has blocked ICMP traffic headed in its direction. Because of this, you are unable to ping the host in order to verify that it is online. You can check the presence of the host in this kind of scenario by either using telnet to connect to a port that is already known to you or by attempting to make a TCP connection to the host.

When you attempt to set up a TCP connection to the remote host, the remote host will either accept the connection or refuse it by sending a RST package. This happens whenever you try to create a connection. Even with only this piece of evidence, it is possible to make a positive identification of the host.

**Step 1: Update your machine.**

```
# yum update -y

```

**Step 2: Run the following command to install tcptraceroute:**

```
# yum install tcptraceroute

```

**Step 3: After that, get tcpping from the internet and install it.**

```
# cd /usr/bin

```

```
# wget http://www.vdberg.org/~richard/tcpping

```

![install tcpping on CentOS](images/image-1200.png)

```
# chmod 755 tcpping

```

**Step 4: Executing tcping in the manner described below is all that is required to test the latency of a network.**

```
# tcpping google.com

```

![How to install tcpping on CentOS](images/image-1199.png)

```
# tcpping other\_server\_ip

```

## Conclusion

Hopefully, you have learned how to install tcpping on CentOS.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
