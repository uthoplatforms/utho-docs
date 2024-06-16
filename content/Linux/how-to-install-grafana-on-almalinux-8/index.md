---
title: "How to install Grafana On Almalinux 8"
date: "2023-06-12"
---

![How to install Grafana On Almalinux 8](images/How-to-install-Grafana-On-Almalinux-8-1024x576.jpg)

## Introduction

In this article, you will learn how to install Grafana on Almalinux 8.

[Grafana](https://en.wikipedia.org/wiki/Grafana) is a free monitoring and data visualisation application that works across multiple platforms. It provides insightful analytics by rendering your data in graphical form and is cross-platform. It is possible to create dynamic dashboards that can be reused, to explore metrics by means of ad hoc queries, to set up alert rules for critical metrics that are constantly evaluated and notify relevant parties of any changes, and to collaborate with members of the team by means of in-built sharing.

##### Step 1: Add Grafana Repository:

**Open a terminal session or log in via SSH to access your AlmaLinux 8 server.**

**Run the following command to add the Grafana repository:**

```
# dnf install https://dl.grafana.com/oss/release/grafana-release.rpm

```

![How to install Grafana On Almalinux 8](images/image-1161.png)

##### Step 2: Install Grafana

**Once the repository is added, run the following command to install Grafana:**

```
# dnf install grafana

```

![How to install Grafana On Almalinux 8](images/image-1162.png)

##### Step 3: Start and Enable Grafana Service:

**Start the Grafana service using the following command:**

```
# systemctl start grafana-server

```

**To ensure that Grafana starts automatically at system boot, enable it with the following command:**

```
# systemctl enable grafana-server

```

##### Step 4: Adjust Firewall

**If you have a firewall enabled on your AlmaLinux server, you need to allow incoming traffic to the Grafana port (default is TCP 3000) to access the Grafana web interface. You can use the following command to open the port:**

```
# firewall-cmd --add-port=3000/tcp --permanent

```

```
# firewall-cmd --reload

```

![firewall rules](images/image-1163.png)

##### Step 5: Access Grafana Web Interface:

**Open a web browser on your local machine and enter the IP address or hostname of your AlmaLinux server, followed by port 3000 (e.g., http://server\_IP:3000).**

![install Grafana on Almalinux 8](images/image-1160.png)

##### Step 6: Log in to Grafana:

**The default username and password for Grafana is "admin".**

**Enter "admin" as the username and "admin" as the password.**

**When you first log in, you'll be asked to change the password.**

## Conclusion

Hopefully, now you have learned how to install Grafana on Almalinux 8.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
