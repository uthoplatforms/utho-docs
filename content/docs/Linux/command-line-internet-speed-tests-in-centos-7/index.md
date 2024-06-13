---
title: "Command-line internet speed tests in CentOS 7"
date: "2022-06-22"
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
