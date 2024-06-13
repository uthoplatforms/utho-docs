---
title: "Configure Let's Encrypt SSL on Ubuntu with Certbot"
date: "2024-02-07"
---

Let's Encrypt offers [SSL certificates](https://utho.com/ssl-certificate) at no cost, enabling secure connections for your websites. Certbot, a free and open-source tool, simplifies the process of generating Let's Encrypt SSL certificates on your unmanaged Linux server. To get started, log into SSH as root.  

**Install Certbot in Ubuntu 20.04**

Certbot now suggests using the snapd package manager for installing on Ubuntu, Instead Python Installs Packages (PIP) is a suitable alternative.  
  
**Install Certbot in Ubuntu with PIP  
  
Ubuntu users of cloud servers have the option to install Certbot using PIP**  

## **Step 1: Initially, install PIP:**  

<table><tbody><tr><td>sudo apt install python3 python3-venv libaugeas0</td></tr></tbody></table>

## **Step 2: Establish a virtual environment:**  

<table><tbody><tr><td>sudo python3 -m venv /opt/certbot/</td></tr></tbody></table>

<table><tbody><tr><td>sudo /opt/certbot/bin/pip install --upgrade pip</td></tr></tbody></table>

## **Step 3: Install Certbot for Utho**  

<table><tbody><tr><td>sudo /opt/certbot/bin/pip install certbot certbot-utho</td></tr></tbody></table>

<table><tbody><tr><td>sudo /opt/certbot/bin/pip install certbot certbot-nginx</td></tr></tbody></table>

## **Step 4: Create a symbolic link to ensure Certbot operates smoothly:**  

<table><tbody><tr><td>sudo ln -s /opt/certbot/bin/certbot /usr/bin/certbot</td></tr></tbody></table>

**To install Certbot on Ubuntu, utilize snapd  
**  
Snapd is available for use by [Dedicated](https://utho.com/dedicated-cpu) Server Hosting users

**Set up snapd:**

<table><tbody><tr><td>sudo apt install snapd</td></tr></tbody></table>

**Verify that you have the latest version of snapd installed:**

<table><tbody><tr><td>sudo snap install core; sudo snap refresh core</td></tr></tbody></table>

  
**Installing Certbot using snapd:**

<table><tbody><tr><td>sudo snap install --classic certbot</td></tr></tbody></table>

  
**Establish a symlink to guarantee Certbot's operation:**

<table><tbody><tr><td>sudo ln -s /snap/bin/certbot /usr/bin/certbot</td></tr></tbody></table>

## **Generate an SSL certificate using Certbot**

Execute Certbot to generate SSL certificates and adjust your web server configuration file to redirect HTTP requests to HTTPS automatically. Alternatively, include "certonly" to create SSL certificates without altering system files, which is recommended for staging sites not intended for forced SSL usage.  

## **Step 1:** Select the most suitable option based on your requirements.  

Generate SSL certificates for all domains and set up redirects in the web server configuration.  

<table><tbody><tr><td>sudo certbot --utho</td></tr></tbody></table>

<table><tbody><tr><td>sudo certbot --nginx</td></tr></tbody></table>

  
Generate SSL certificates for a specified domain, which is recommended if you're utilizing your system hostname

<table><tbody><tr><td>sudo certbot --utho -d example.com -d www.example.com</td></tr></tbody></table>

  
Only install SSL certs:

<table><tbody><tr><td>sudo certbot certonly --utho</td></tr></tbody></table>

<table><tbody><tr><td>sudo certbot certonly --nginx</td></tr></tbody></table>

## **Step 2:** Provide an email address for renewal and security notifications.   

## **Step 3:** Accept the terms of service.   

## **Step 4:** Decide if you wish to receive emails from EFF.   

## **Step 5:** If prompted, select whether to redirect HTTP traffic to HTTPS: Option 1 for no redirect and no additional server changes, or Option 2 to redirect all HTTP requests to HTTPS.  

**SSL Maintenance and Troubleshooting**Once you've installed a Let’s Encrypt certificate on your Ubuntu Certbot setup, you can check your website's SSL status at [https://WhyNoPadlock.com](https://whynopadlock.com/). This will help you detect any mixed content errors.  
  
The certificate files for each domain are stored in:  

<table><tbody><tr><td>cd /etc/letsencrypt/live</td></tr></tbody></table>

Let’s Encrypt certificates have a lifespan of 90 days. To avoid expiration, Certbot automatically monitors SSL status twice daily and renews certificates expiring within thirty days. You can review settings using Systemd or cron.d.  

<table><tbody><tr><td>systemctl show certbot.timer</td></tr></tbody></table>

<table><tbody><tr><td>cat /etc/cron.d/certbot</td></tr></tbody></table>

Verify that the renewal process functions correctly:  

<table><tbody><tr><td>sudo certbot renew --dry-run</td></tr></tbody></table>

  
Simply having an SSL certificate and implementing 301 redirects to enforce HTTPS may not always suffice to thwart hacks. Cyber attackers have devised methods to circumvent both security measures, potentially compromising server communications.

HTTP Strict Transport Security (HSTS) is a security HTTP header designed to counteract this by instructing web browsers to serve your website only when a valid SSL certificate is received. If the browser encounters an insecure connection, it outright rejects the data, safeguarding the user.

Configuring HSTS within your web server, is straightforward and enhances security significantly.
