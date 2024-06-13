---
title: "Speed Test in Ubuntu server"
date: "2022-06-22"
---

![](images/Speed-Test-in-Ubuntu-server_utho.jpg)

Speedtest.net is an online service that allows you to test your broadband connection by downloading a file from a speedtest.net server located near you. This utility allows you to use the command line to access the service.

Step 1. Login into the server using root credentials on putty.

![](images/BB1-3.png)

Step 2. Install python pip packages in order to install SpeedTest-cli with the below command.

```
 # apt-get install python-pip 
```

Step 3. To install the SpeedTest client with pip, run the following command.

```
 # pip install speedtest-cli 
```

Step 4. At last , run speedtest-cli for upload and download speed test.

```
 # speedtest-cli 
```

![](images/pasted_image_0_4_.png)

Thank you!!
