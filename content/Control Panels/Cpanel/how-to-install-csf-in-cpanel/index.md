---
title: "How to install CSF in cPanel"
date: "2021-12-17"
---

![](images/How-to-install-CSF-in-cPanel_utho.jpg)

ConfigServe Firewall (CSF) is a firewall configuration tool that was created to enhance server security by providing a simple yet powerful interface for configuring firewall settings on cPanel servers.

This programme is freely downloadable as a WHM plugin. To perform a simple CSF installation, follow these steps.

**Install CSF:** Log into your server as root, using SSH.  
Follow below given command

```
# cd /usr/local/src/ 
```

You will obtain the following image after running this command:

![](images/csf1.png)

After changing the directory, use the following command to start the download:

```
# wget https://download.configserver.com/csf.tgz 
```

![](images/csf2-1024x163.png)

After you've finished downloading, run the command listed below.

```
# tar -xzf csf.tgz 
```

```
# cd csf 
```

```
# sh install.sh 
```

```
# systemctl status csf.service 
```

![](images/csf_3.png)

Configure CSF: By logging into WHM as root and going to the bottom left menu. Go to ConfigServer Security Firewall in the Plugins area.

![](images/CsF-4-1024x290.png)

Thank you!!
