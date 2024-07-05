---
title: "How to install Certbot on CentOS 7"
date: "2023-04-04"
---

![How to install Certbot on CentOS 7](images/How-to-install-Certbot-on-CentOS-7_utho.jpg)

## Introduction

In this article, you will learn how to install Certbot on CentOS.

[Certbot](https://en.wikipedia.org/wiki/Let%27s_Encrypt) is a Python client that automates the process of obtaining free certificates from Let's Encrypt and configuring web servers to establish a secure https connection using these certificates. It does this by acquiring the certificates through Let's Encrypt.

As a starting point, we will look at the process of downloading Cerbot on CentOS, learn how to acquire a free certificate by utilising it, and automatically configure nginx and apache.

## Install Certbot on CentOS 7 with nginx / apache

**In order to install certbot, we first need to install the epel-release repository (all of the following commands are performed with root privileges):**

```
# yum install epel-release

```

**Installing the client application and the package necessary for communicating with the server (using nginx as an example) is the next step.**

```
# yum install certbot python2-certbot-nginx

```

**Install certbot along with the appropriate package if you are utilising Apache.**

```
# yum install certbot python2-certbot-apache

```

**The next step is to immediately acquire a free certificate and set up https configuration. If you are using nginx, run:**

```
# certbot --nginx

```

**If Apache is already installed on your system:**

```
# certbot --apache

```

**When you use Certbot, it will ask for an email account that can be used for critical renewals and notifications related to security. After pointing to your message and pressing Enter, we will:**

![How to install Certbot on CentOS 7](images/1-21.png)

**Following that, you will need to approve the terms of service by entering Y and then pressing Enter:**

![How to install Certbot on CentOS 7](images/2-17.png)

**After the successful completion of the certificate retrieval process, we will be prompted to publicise our email address. We do not accept by typing N and pressing Enter:**

![output](images/3-14.png)

The nginx and Apache configuration files will be scanned by Certbot for domain names. If they are found, it will display a list of them and offer to indicate which ones you want to acquire certificates for and configure the web server for. If they are not found, it will not display the list. (position numbers are entered in the console, possibly several separated by a space).

In the event that you do not yet have any configured domains, you will be required to explicitly provide the name:

After that, we have to wait until the completion of the task for the client:

> In order for Certbot to function properly, both port 80 and port 443 need to be open.

**Run the following command to check the version of Certbot.**

```
# certbot --version

```

![certbot version](images/certbot-version.png)

**We navigate to the browser's address bar and type in our website. A secure connection will be suggested by the presence of a lock icon to the left of it:**

**Let's Encrypt certificates are valid for a period of three months after they are granted, so we will set up automatic renewal for them. In order to accomplish this, start any text editor and type cron:**

```
# vi /etc/crontab

```

**In addition, we will assign a recurring action to periodically update all certificates by using certbot:**

0 0,12 \* \* \* root python -c 'import random; import time; time.sleep(random.random() \* 3600)' && certbot renew -q

Save the by file pressing Escape :wq

## Conclusion

Hopefully, now you have learned how to install Certbot on CentOS.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
