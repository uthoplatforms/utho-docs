---
title: "Command-line internet speed tests in CentOS 7"
date: "2022-06-22"
title_meta: "Guide for Command-line internet speed tests in CentOS 7"
description: "Learn how to perform command-line internet speed tests in CentOS 7 using various tools and commands. This guide covers CLI methods to measure network performance, check bandwidth, and assess internet speed directly from the terminal in CentOS 7.
"
keywords: ["command-line speed test CentOS 7", "CLI internet speed test CentOS 7", "CentOS 7 network speed test", "terminal speed test CentOS 7", "CentOS 7 speed test commands", "bandwidth test CentOS 7", "network performance test CentOS 7", "CentOS 7 speed test tools"]

tags: ["internet speed tests", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/command-line-internet-speed-tests-in-centos-7']
tab: true
---

![](images/Command-line-internet-speed-tests-in-CentOS-7_utho.jpg)

**Speedtest**

Speed test is an old standby. It's written in Python, packaged in Apt, and also available through pip. It can be used as a command-line tool or as part of a Python script.

Step1. Login into the server using root credentials on putty .

![](images/BB1.png)

![](images/BB2.png)

Step 2. Then enter the following command to install Python.

```
 # yum install -y python 
```

Step 3. Enter the following command after installing Python to download SpeedTest-Cli on your server.

```
 # # wget -O speedtest-cli [https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py](https://raw.githubusercontent.com/sivel/speedtest-cli/master/speedtest.py) 
```

Step 4. To give the SpeedTest tool executable access, run the following command after downloading it.

```
 # chmod +x speedtest-cli 
```

Step 5. After you've completed the preceding steps, type the following command to begin the speed test.

```
 # ./speedtest-cli 
```

![](images/BB6.png)

Thank you!!
