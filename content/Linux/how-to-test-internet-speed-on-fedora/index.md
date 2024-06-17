---
title: "How to Test Internet Speed on Fedora"
date: "2023-06-28"
---

![How to Test Internet Speed on Fedora](images/How-to-Test-Internet-Speed-on-Fedora-1024x576.jpg)

## Introduction

In this article you will learn how to test internet speed on Fedora.

When you notice that the speed at which you can access the internet on your server is slow, the first thing you need to do in order to remedy the problems caused by the slow connectivity is to check the [connection speed](https://en.wikipedia.org/wiki/Speedtest.net).

The SpeedTest CLI makes it possible to monitor the current internet speed of your Fedora server from the command line. Additionally, it delivers dependable technology and a worldwide service network to the command line, which is the driving force behind SpeedTest.

**Step 1: Python can be installed on your machine by executing the command that is provided in the following section.**

```
# dnf install python2 -y

```

![How to Test Internet Speed on Fedora](images/image-1145.png)

**Step 2: Using the command that is given below, download the file called speedtest\_cli.py.**

```
# wget -O speedtest-cli https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py

```

![How to Test Internet Speed on Fedora](images/image-1144.png)

**Step 3: To make the script file executable, use the command that is listed below.**

```
# chmod +x speedtest-cli

```

**Step 4: You are now able to test the speed of your system's internet connection by using the speedtest-cli command.**

```
# python2 speedtest-cli

```

![speed test](images/image-1143.png)

**Step 5: Execute the following command to determine how fast your internet connection is in bytes.**

```
# python2 speedtest-cli --bytes

```

![test internet speed on Fedora](images/image-1142.png)

## Conclusion

Hopefully, Now you have learned how to test internet speed on Fedora.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
