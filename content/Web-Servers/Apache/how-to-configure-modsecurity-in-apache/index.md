---
title: "How to Configure ModSecurity in Apache"
date: "2020-05-21"
Title_meta: GUIDE TO Configure ModSecurity in Apache

Description: This guide provides detailed instructions on configuring ModSecurity in Apache. Learn how to set up and enable ModSecurity, a powerful web application firewall, to protect your Apache server from various web-based attacks and vulnerabilities.

Keywords: ['ModSecurity', 'Apache', 'configure ModSecurity', 'web application firewall', 'server security']

Tags: ["ModSecurity", "Apache", "Web Application Firewall", "Server Security"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Web-Servers/Apache/how-to-configure-modsecurity-in-apache/']
tab: true
---

## Introduction

ModSecurity is a web firewall application for an Apache web server. In addition to providing logging capabilities, ModSecurity can monitor HTTP traffic in real time to detect attacks. ModSecurity also operates as an intrusion detection tool that allows you to respond to suspicious events taking place on your web systems.

![](images/How-to-Configure-ModSecurity-in-Apache_utho.jpg)

## Install ModSecurity

You need Apache installed on your Microhost cloud before you install ModSecurity. The LAMP stack is used in this guide; see LAMP guidelines for installation.

## Debian

```
sudo apt install libapache2-modsecurity
```

Restart Apache:

```
/etc/init.d/apache2 restart
```

Check the ModSecurity version is 2.8.0 or later:

```
apt-cache show libapache2-modsecurity
```

\[ht\_message mstyle="alert" title="NOTE" " show\_icon="true" id="" class=""style="" \]When you list all mods using **apachectl -M**, ModSecurity is listed under the name **security2\_module**.\[/ht\_message\]

## Ubuntu

```
sudo apt-get install libapache2-mod-security2
```

Restart Apache:

```
/etc/init.d/apache2 restart
```

Check the version of ModSecurity is 2.8.0 or higher:

```
apt-cache show libapache2-mod-security2
```

## CentOS

```
yum install mod_security
```

Restart Apache by entering the below command:

```
/etc/init.d/httpd restart
```

Check the version of ModSecurity is 2.8.0 or higher:

```
yum info mod_security
```

## OWASP ModSecurity Core Rule Set

The following steps are for distributions based on Debian. The paths and commands for RHEL will differ slightly.

1\. Move and update the default ModSecurity file name:

```
mv /etc/modsecurity/modsecurity.conf-recommended modsecurity.conf
```

2\. If needed, install git:

```
sudo apt install git
```

3\. OWASP ModSecurity CRS can be downloaded from Github:

```
git clone https://github.com/SpiderLabs/owasp-modsecurity-crs.git
```

4\. Navigate into the directory you are downloading. Switch to `crs-setup.conf.example`, and rename `crs-setup.conf`.  Then pass the `rules/`  likewise.

```
cd owasp-modsecurity-crs mv crs-setup.conf.example /etc/modsecurity/crs-setup.conf mv rules/ /etc/modsecurity/
```

5\. The config file should match the above path as specified in the `IncludeOptional` directive. Add a further Guideline that refers to the collection of rules:

```file {title="etc/apache2/mods-available/security2.conf" lang="aconf"}
```
# Default Debian dir for modsecurity's persistent data SecDataDir /var/cache/modsecurity

```

```
    # Include all the *.conf files in /etc/modsecurity.
    # Keeping your local configuration in that directory
    # will allow for an easy upgrade of THIS file and
    # make your life easier
    IncludeOptional /etc/modsecurity/*.conf
    Include /etc/modsecurity/rules/*.conf
```
```

6\. Restart Apache to give effect to changes:

```
/etc/init.d/apache2 restart
```

## ModSecurity Test

OWASP CRS builds on top of ModSecurity in order to extend existing rules.

1\. Navigate to the default Apache configuration and use the default configuration as an example to add two additional directives:

```file {title="/etc/apache2/sites-available/000-default.conf" lang="aconf"}
ServerAdmin webmaster@localhost DocumentRoot /var/www/html

```

SecRuleEngine On
SecRule ARGS:testparam "@contains test" "id:1234,deny,status:403,msg:'Our test rule has triggered'"
```
```

2\. Restart Apache and then curl the index page to intentionally trigger the alarms:

```
curl localhost/index.html?testparam=test
```

The response code is set to be 403. A message that shows the given ModSecurity rule worked should be in the logs. Use : `sudo tail -f /var/log/apache2/error.log`

```
 ModSecurity: Access denied with code 403 (phase 2). String match “test” at ARGS:testparam. [file “/etc/apache2/sites-enabled/000-default.conf”] [line “24”] [id “1234”] [msg “Our test rule has triggered”] [hostname “localhost”] [uri “/index.html”] [unique_id “WfnEd38AAAEAAEnQyBAAAAAB”]
```

3\. Verify the OWASP CRS is valid:

```
curl localhost/index.html?exec=/bin/bash
```

Check the error logs again: attempted execution of an arbitrary bash script has been captured by statute.

```
 ModSecurity: Warning. Matched phrase “bin/bash” at ARGS:. [file “/etc/modsecurity/rules/REQUEST-932-APPLICATION-ATTACK-RCE.conf”] [line “448”] [id “932160”] [rev “1”] [msg “Remote Command Execution: Unix Shell Code Found”] [data “Matched Data: bin/bash found within ARGS:: exec/bin/bash”] [severity “CRITICAL”] [ver “OWASP_CRS/3.0.0”] [maturity “1”] [accuracy “8”] [tag “application-multi”] [tag “language-shell”] [tag “platform-unix”] [tag “attack-rce”] [tag “OWASP_CRS/WEB_ATTACK/COMMAND_INJECTION”] [tag “WASCTC/WASC-31”] [tag “OWASP_TOP_10/A1”] [tag “PCI/6.5.2”] [hostname “localhost”] [uri “/index.html”] [unique_id “WfnVf38AAAEAAEqya3YAAAAC”]
```

Thankyou..
