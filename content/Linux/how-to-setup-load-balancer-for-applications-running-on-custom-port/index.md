---
title: "HOW TO SETUP LOAD BALANCER FOR APPLICATIONS RUNNING ON CUSTOM PORT"
date: "2022-01-02"
title_meta: "HOW TO SETUP LOAD BALANCER FOR APPLICATIONS RUNNING ON CUSTOM PORT"
description: "HOW TO SETUP LOAD BALANCER FOR APPLICATIONS RUNNING ON CUSTOM PORT"
keywords:  ['loadbalancer', 'linux']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/how-to-setup-load-balancer-for-applications-running-on-custom-port']
---
---

![](images/HOW-TO-SETUP-LOAD-BALANCER-FOR-APPLICATIONS-RUNNING-ON-CUSTOM-PORT_utho.jpg)

**Introduction**: In this article, we are going to setup a testing environment to test the load distribution using loadbalancer among applications running on custom ports rather then port 80. In this testing , we are using 3 different servers installed and configured apache on 3 different ports.

**Step 1**: Firstly, we need to make 3 different servers ready with apache running on respective ports (8080, 8081, 8082) . You can refer "apache port change" article for more details on apache installation on custom port. 

![](images/Screenshot_19-5.png)

**Step 2**: We need to create a load balancer to check the traffic distribution among these above three servers. Please have a look on the below screenshot for your reference.

![](images/lb4-1024x359.png)

**Step 3**: Once the loadbalacer would be successfully completed, we need to add our backend servers in it to distribute the traffic of all three servers. For managing the loadbalancer we need to click on action button and then click on manage load  balancer option as per the below screenshot.

![](images/lb5-1024x393.png)

**Step 4** : Now we have to add the servers in the loadbalancer.

![](images/lb6-1024x406.png)

**Step 5** : Once the servers will be added then we need to modify the loadbalancer configuration file, as it takes by default traffic distribution on port 80. Hence we need to change the port to our respective ports that are 8080, 8081,8082. The path of configuration file is /etc/haproxy/haproxy.cfg. Please have a look on the highlighted part of screenshot for more details.

![](images/lb7.png)

**Step 6** : Once the modification will be done in the haproxy  conf file then you need to save the file and restart the haproxy service using below command

```
# **systemctl restart haproxy** 
```

**Step 7**: Now we can verify the testing while accessing the lodbalancer ip in browser.

![](images/lb8.png)

Thank you :)
