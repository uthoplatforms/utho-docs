---
slug: "Get Started with LXD: The Ultimate Reverse Proxy Guide for Beginners"
description: "In this tutorial, you'll learn how to set up a reverse proxy within an LXD container, enabling you to host multiple websites, each in its own dedicated container"
keywords: ["container", "lxd", "lxc", "apache", "nginx", "reverse proxy", "virtual machine", "virtualization"]
tags: ["proxy","ubuntu","container","apache","nginx"]
published: 2024-05-24
modified: 2024-05-24
modified_by:
  name: Utho
title: "A Beginner's Guide to LXD: Setting Up a Reverse Proxy to Host Mulitple Websites"
title_meta: "How to Set Up a Reverse Proxy to Host Websites in LXD"
authors: ["Pawan Kumar"]
---

## Introduction

LXD (pronounced "Lex-Dee") is a container manager built on top of Linux Containers (LXC) and supported by Canonical. LXD aims to deliver a virtual machine-like experience through containerization instead of hardware virtualization. Unlike Docker, which focuses on delivering applications, LXD provides nearly full operating-system functionality with additional features like snapshots, live migrations, and storage management.

A reverse proxy acts as an intermediary server between internal applications and external clients, directing client requests to the appropriate server. Although many applications, such as Node.js, can function as servers, they may lack advanced load balancing, security, and acceleration capabilities.

In this guide, you'll learn how to create a reverse proxy within an LXD container to host multiple websites, each in its own container. We'll use NGINX and Apache web servers, with NGINX serving as the reverse proxy.

Refer to the diagram below to understand the reverse proxy setup covered in this guide.

In this guide you will:

 - [Install and configure containers](#creating-the-containers) for both NGINX and Apache web servers.

 - [Learn how to install and configure a reverse proxy in a container](#setting-up-the-reverse-proxy).

 - [Get SSL/TLS support through Let's Encrypt certificates with automated certificate renewal](#adding-support-for-https-with-lets-encrypt).

- [Troubleshooting common errors](#troubleshooting).


Note: For clarity and simplicity, this guide uses the term container to refer to LXD system containers.

### Before You Begin

Complete A Beginner's Guide to LXD: Setting Up an Apache Web Server In a Container. This guide will walk you through creating a container named web with an Apache web server for testing. Remove this container by running the following commands.

                              lxc stop web
                              lxc delete web

Note: For this guide, you need LXD version 3.3 or later. Check your version with the command below:

                              lxd --version

If your version is not 3.3 or later, update to the latest version by installing the snap package as instructed in A Beginner's Guide to LXD: Setting Up an Apache Webserver In a Container and use the following command:

                              sudo lxd.migrate

This guide uses the hostnames apache1.example.com and nginx1.example.com for the two example websites. Replace these with your own hostnames and set up their DNS entries to point to the IP address of your server. For help with DNS, see our DNS Manager Guide.

## Creating the Containers

Create two containers named apache1 and nginx1, one with the Apache web server and the other with the NGINX web server. For additional websites, create new containers with your chosen web server software.

                          lxc launch ubuntu:18.04 apache1
                          lxc launch ubuntu:18.04 nginx1

Create the proxy container to serve as the reverse proxy.

                          lxc launch ubuntu:18.04 proxy

List the containers with the list command.

                          lxc list

The output looks similar to the following.

    {{< output >}}
+---------+---------+---------------------+-----------------------------------------------+------------+-----------+
|  NAME   |  STATE  |        IPV4         |                     IPV6                      |    TYPE    | SNAPSHOTS |
+---------+---------+---------------------+-----------------------------------------------+------------+-----------+
| apache1 | RUNNING | 10.10.10.204 (eth0) | fd42:67a4:b462:6ae2:216:3eff:fe01:1a4e (eth0) | PERSISTENT |           |
+---------+---------+---------------------+-----------------------------------------------+------------+-----------+
| nginx1  | RUNNING | 10.10.10.251 (eth0) | fd42:67a4:b462:6ae2:216:3eff:febd:67e3 (eth0) | PERSISTENT |           |
+---------+---------+---------------------+-----------------------------------------------+------------+-----------+
| proxy   | RUNNING | 10.10.10.28 (eth0)  | fd42:67a4:b462:6ae2:216:3eff:fe00:252e (eth0) | PERSISTENT |           |
+---------+---------+---------------------+-----------------------------------------------+------------+-----------+
{{< /output >}}

The output should show three containers, all in the RUNNING state, each with its own private IP address. Note the IP addresses (both IPv4 and IPv6) for the proxy container. You will need these for configuring the proxy container in a later section.

Now that the containers are created, the following steps will guide you through setting up the web server software in the apache1 and nginx1 containers, and configuring the proxy container to make the web servers accessible from the internet.

### Configuring the Apache Web Server Container

When using a reverse proxy in front of a web server, the web server cannot see the IP addresses of visitors; it only sees the IP address of the reverse proxy. However, each web server can identify the real remote IP address of a visitor. For Apache, this is done with the Remote IP Apache module. To use this module, the reverse proxy must be configured to pass along the remote IP address information.

Start a shell in the apache1 container.

                        lxc exec apache1 -- sudo --user ubuntu --login

Update the package list in the apache1 container.

                        sudo apt update

Install the apache2 package in the container.

                        sudo apt install -y apache2

Create the file /etc/apache2/conf-available/remoteip.conf.

                           {{< file "remoteip.conf" >}}
                            RemoteIPHeader X-Real-IP
                            RemoteIPTrustedProxy 10.10.10.28 fd42:67a4:b462:6ae2:216:3eff:fe00:252e
                            {{</ file >}}

Use the nano text editor by running the command sudo nano /etc/apache2/conf-available/remoteip.conf. Enter the IP addresses of the proxy container shown earlier, for both IPv4 and IPv6. Replace these with the IPs from your lxc list output.

Note: Instead of specifying the IP addresses, you can use the hostname proxy.lxd. However, the RemoteIP Apache module can be inconsistent when using a hostname, as it may use only one of the IP addresses (either IPv4 or IPv6). This means the Apache web server may not know the real source IP address for some connections. By listing both IPv4 and IPv6 addresses explicitly, you ensure that RemoteIP successfully accepts the source IP information from all connections of the reverse proxy.

Enable the new remoteip.conf configuration.

                       sudo a2enconf remoteip

                      {{< output >}}
                      Enabling conf remoteip.
                      To activate the new configuration, you need to run:
                       systemctl reload apache2
                      {{< /output >}}

Enable the remoteip Apache module.

                  sudo a2enmod remoteip

                  {{< output >}}
                  Enabling module remoteip.
                  To activate the new configuration, you need to run:
                  systemctl restart apache2
                  {{< /output >}}

Edit the default web page for Apache to indicate that it runs inside an LXD container.

                   sudo nano /var/www/html/index.html
                   Change the line "It works!" (line number 224) to "It works inside a LXD container!" Save and exit.

                   Restart the Apache web server.

                   sudo systemctl reload apache2

Exit back to the host.

                  exit

You have created and configured the Apache web server, but it is not yet accessible from the Internet. It will become accessible after you configure the proxy container in a later section.

### Creating the NGINX Web Server Container

Like Apache, NGINX cannot see the IP addresses of visitors when a reverse proxy is used in front of the web server; it only sees the IP address of the reverse proxy. However, NGINX can identify the real remote IP address of a visitor using the Real IP module. To enable this, the reverse proxy must be configured to pass along the remote IP address information.

Start a shell in the nginx1 contain

                           lxc exec nginx1 -- sudo --user ubuntu --login

Update the package list in the nginx1 container.

                           sudo apt update

Install NGINX in the container.

                           sudo apt install -y nginx

Create the file /etc/nginx/conf.d/real-ip.conf.

                        {{< file "real-ip.conf" >}}
                        real_ip_header    X-Real-IP;
                        set_real_ip_from  proxy.lxd;
                         {{</ file >}}

Use the nano text editor to create and edit the file by running the command sudo nano /etc/nginx/conf.d/real-ip.conf.

Note: Specify the hostname of the reverse proxy as proxy.lxd. Each LXD container automatically gets a hostname in the format container-name.lxd. By setting the set_real_ip_from field to proxy.lxd, you instruct the NGINX web server to accept the real IP address information for each connection originating from proxy.lxd. The real IP address information is provided in the HTTP header X-Real-IP for each connection.

Edit the default web page for NGINX to indicate that it runs inside an LXD container.

                              sudo nano /var/www/html/index.nginx-debian.html

                      Change the line "Welcome to nginx!" (line number 14) to "Welcome to nginx running in a LXD system container!". Save and exit.

Restart the NGINX web server.

                            sudo systemctl reload nginx

Exit back to the host.

                            exit

You have successfully created and configured the NGINX web server. However, it is not accessible from the Internet yet. It will become accessible after you configure the proxy container in the next section.

## Setting up the Reverse Proxy

In this section, you'll configure the proxy container. You'll install NGINX and configure it as a reverse proxy. Additionally, you'll add the necessary LXD proxy devices to forward connections from the internet to ports 80 (HTTP) and 443 (HTTPS) on the server to the corresponding ports on the proxy container.

Add LXD proxy devices to redirect connections from the internet to ports 80 (HTTP) and 443 (HTTPS) on the server to the respective ports on the proxy container.

                       lxc config device add proxy myport80 proxy listen=tcp:0.0.0.0:80 connect=tcp:127.0.0.1:80 proxy_protocol=true
                       lxc config device add proxy myport443 proxy listen=tcp:0.0.0.0:443 connect=tcp:127.0.0.1:443 proxy_protocol=true

                      {{< output >}}
                       Device myport80 added to proxy
                       Device myport443 added to proxy
                     {{< /output >}}

The lxc config device add command requires the following arguments:

                                            | Argument | Explanation |
                                             | ----- | ----- |
                                             | `proxy` | The name of the container. |
                                             | `myport80` | A name for this proxy device. |
                                             | `proxy` | The type of the LXD device (LXD _proxy_ device). |
                                 | `listen=tcp:0.0.0.0:80` | The proxy device listens on the host (default) on port 80, protocol TCP, on all interfaces. |
                                 | `connect=tcp:127.0.0.1:80` | The proxy device connects to the container on port 80, protocol TCP, on the loopback interface. In previous versions of LXD you could have specified `localhost` here. However, in LXD 3.13 or newer, you can only specify IP addresses. |
                                 | `proxy_protocol=true` | Request to enable the PROXY protocol so that the reverse proxy gets the originating IP address from the proxy device. |

Note: To remove a proxy device, utilize lxc config device remove. For instance, to remove the device named myport80 from the container proxy, execute the following command:

                                    lxc config device remove proxy myport80

Replace proxy with the name of the container, and myport80 with the name of the device you want to remove.

Access a shell in the proxy container.

                                    lxc exec proxy -- sudo --user ubuntu --login

Update the package list.

                                    sudo apt update

Install NGINX in the container.

                                   sudo apt install -y nginx

Exit from the container.

                                     logout

### Direct Traffic to the Apache Web Server From the Reverse Proxy

The reverse proxy container is up and running, and the NGINX package has been successfully installed. To function as a reverse proxy, you need to configure NGINX to recognize the appropriate hostname (specified by server_name below) and then forward the connection (specified by proxy_pass below) to the relevant LXD container.

Access a shell in the proxy container.

                                  lxc exec proxy -- sudo --user ubuntu --login

Create the configuration file for your first website, apache1.example.com, in /etc/nginx/sites-available/.

                         {{< file "apache1.example.com" >}}
                       server {
                          listen 80 proxy_protocol;
                          listen [::]:80 proxy_protocol;

                          server_name apache1.example.com;

                          location / {
                             include /etc/nginx/proxy_params;

                              proxy_pass http://apache1.lxd;
                               }

                              real_ip_header proxy_protocol;
                              set_real_ip_from 127.0.0.2;
                               }
                             {{</ file >}}

                             Run sudo nano /etc/nginx/sites-available/apache1.example.com to open a text editor and add the configuration. Ensure to only modify the server_name to match the hostname of your website.

Enable the website.

                                sudo ln -s /etc/nginx/sites-available/apache1.example.com /etc/nginx/sites-enabled/

Restart the NGINX reverse proxy. By restarting the service, NGINX reads and applies the new site instructions just added to /etc/nginx/sites-enabled.

                                sudo systemctl reload nginx

Exit the proxy container and return to the host.

                                  logout

From your local computer, visit the URL of your website using your web browser. You should see the default Apache page.

Note: If you check the Apache access.log file (default location: /var/log/apache2/access.log), you may notice that it still displays the private IP address of the proxy container instead of the real IP address. This issue is specific to the Apache web server and relates to how the server logs are printed. Other software on the web server can use the real IP. To address this issue with Apache logs, refer to the section Troubleshooting.

### Direct Traffic to the NGINX Web Server From the Reverse Proxy

The reverse proxy container is up and running, and the NGINX package has been installed. To function as a reverse proxy, you need to configure NGINX with the appropriate website configuration. This configuration allows NGINX to identify the correct hostname (specified by server_name below) and forward the connection (specified by proxy_pass below) to the relevant LXD container containing the actual web server software.

Start a shell in the proxy container.

        lxc exec proxy -- sudo --user ubuntu --login

Create the file nginx1.example.com in /etc/nginx/sites-available/ to configure your second website.

                        {{< file "nginx1.example.com" >}}
                           server {
                            listen 80 proxy_protocol;
                            listen [::]:80 proxy_protocol;

                            server_name nginx1.example.com;

                             location / {
                             include /etc/nginx/proxy_params;

                              proxy_pass http://nginx1.lxd;
                             }

                             real_ip_header proxy_protocol;
                             set_real_ip_from 127.0.0.2;
                              }
                              {{</ file >}}

You can use sudo nano /etc/nginx/sites-available/nginx1.example.com to create the configuration. Remember to edit only the server_name field to match the hostname of your website.

Enable the website.

                               sudo ln -s /etc/nginx/sites-available/nginx1.example.com /etc/nginx/sites-enabled/

Restart the NGINX reverse proxy service.

                               sudo systemctl reload nginx

Exit the proxy container and return to the host.

                                logout

Visit the URL of your website from your local computer using your web browser. You should see the default NGINX page.

### Adding Support for HTTPS with Let's Encrypt

Access a shell in the proxy container.

                        lxc exec proxy -- sudo --user ubuntu --login

Add the repository ppa:certbot/certbot by running the following command.

                        sudo add-apt-repository ppa:certbot/certbot

1.  The output looks similar to the following.

                           {{< output >}}
                            This is the PPA for packages prepared by Debian Let's Encrypt Team and backported for Ubuntu(s).
                             More info: https://launchpad.net/~certbot/+archive/ubuntu/certbot
                             Press [ENTER] to continue or Ctrl-c to cancel adding it.

                             Get:1 http://security.ubuntu.com/ubuntu bionic-security InRelease [88.7 kB]
                              ...
                               Fetched 3360 kB in 2s (2018 kB/s)
                                Reading package lists... Done
                              {{< /output >}}

Install the following two packages to: a) support the creation of Let's Encrypt certificates; and b) auto-configure the NGINX reverse proxy to use Let's Encrypt certificates. These packages are pulled from the newly-added repository.

                                sudo apt-get install certbot python-certbot-nginx

Note: This configuration sets up the reverse proxy to act as a TLS Termination Proxy. Any HTTPS configuration is managed within the proxy container. This eliminates the need to handle certificate-related tasks inside the web server containers.

Run certbot as root with the --nginx parameter to auto-configure Let's Encrypt for the first website. You'll be prompted to provide a valid email address for urgent renewal and security notices, accept the Terms of Service, and opt-in for future contact by the Electronic Frontier Foundation. Then, specify the website for which you're activating HTTPS. Finally, choose whether to set up automatic redirection from HTTP to HTTPS connections.

                                sudo certbot --nginx

                          {{< output >}}
                       Saving debug log to /var/log/letsencrypt/letsencrypt.log
                       Plugins selected: Authenticator nginx, Installer nginx
                       Enter email address (used for urgent renewal and security notices) (Enter 'c' to
                       cancel): myemail@example.com

                       - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                       Please read the Terms of Service at
                       https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
                       agree in order to register with the ACME server at
                        https://acme-v02.api.letsencrypt.org/directory
                       - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                       (A)gree/(C)ancel: A

                       - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                       Would you be willing to share your email address with the Electronic Frontier
                       Foundation, a founding partner of the Let's Encrypt project and the non-profit
                       organization that develops Certbot? We'd like to send you email about our work
                       encrypting the web, EFF news, campaigns, and ways to support digital freedom.
                      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                      (Y)es/(N)o: N

                      Which names would you like to activate HTTPS for?
                      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                      1: apache1.example.com
                      2: nginx1.example.com
                      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                      Select the appropriate numbers separated by commas and/or spaces, or leave input
                      blank to select all options shown (Enter 'c' to cancel): 1
                      Obtaining a new certificate
                      Performing the following challenges:
                      http-01 challenge for apache1.example.com
                      Waiting for verification...
                      Cleaning up challenges
                      Deploying Certificate to VirtualHost /etc/nginx/sites-enabled/apache1.example.com

                     Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
                     - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                     1: No redirect - Make no further changes to the webserver configuration.
                     2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
                     new sites, or if you're confident your site works on HTTPS. You can undo this
                     change by editing your web server's configuration.
                     - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                     Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2
                     Redirecting all traffic on port 80 to ssl in /etc/nginx/sites-enabled/apache1.example.com

                     - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                     Congratulations! You have successfully enabled https://apache1.example.com

                     You should test your configuration at:
                      https://www.ssllabs.com/ssltest/analyze.html?d=apache1.example.com
                       - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

                      IMPORTANT NOTES:
                     - Congratulations! Your certificate and chain have been saved at:
                      /etc/letsencrypt/live/apache1.example.com/fullchain.pem
                      Your key file has been saved at:
                      /etc/letsencrypt/live/apache1.example.com/privkey.pem
                     Your cert will expire on 2019-10-07. To obtain a new or tweaked
                     version of this certificate in the future, simply run certbot again
                     with the "certonly" option. To non-interactively renew *all* of
                      your certificates, run "certbot renew"
                    - Your account credentials have been saved in your Certbot
                     configuration directory at /etc/letsencrypt. You should make a
                      secure backup of this folder now. This configuration directory will
                      also contain certificates and private keys obtained by Certbot so
                      making regular backups of this folder is ideal.
                     - If you like Certbot, please consider supporting our work by:

                      Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
                      Donating to EFF:                    https://eff.org/donate-le
                      {{< /output >}}

Run certbot as root with the --nginx parameter to auto-configure Let's Encrypt for the second website. Since this is the second run of certbot, you'll be prompted directly to select the website to configure.

                        sudo certbot --nginx

                    {{< output >}}
                     Saving debug log to /var/log/letsencrypt/letsencrypt.log
                     Plugins selected: Authenticator nginx, Installer nginx

                    Which names would you like to activate HTTPS for?
                     - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                     1: apache1.example.com
                     2: nginx1.example.com
                    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                    Select the appropriate numbers separated by commas and/or spaces, or leave input
                    blank to select all options shown (Enter 'c' to cancel): 2
                    Obtaining a new certificate
                    Performing the following challenges:
                    http-01 challenge for nginx1.example.com
                    Waiting for verification...
                    Cleaning up challenges
                    Deploying Certificate to VirtualHost /etc/nginx/sites-enabled/nginx1.example.com

                    Please choose whether or not to redirect HTTP traffic to HTTPS, removing HTTP access.
                    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                   1: No redirect - Make no further changes to the webserver configuration.
                   2: Redirect - Make all requests redirect to secure HTTPS access. Choose this for
                   new sites, or if you're confident your site works on HTTPS. You can undo this
                   change by editing your web server's configuration.
                   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                   Select the appropriate number [1-2] then [enter] (press 'c' to cancel): 2
                   Redirecting all traffic on port 80 to ssl in /etc/nginx/sites-enabled/nginx1.example.com

                   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                   Congratulations! You have successfully enabled https://nginx1.example.com

                   You should test your configuration at:
                    https://www.ssllabs.com/ssltest/analyze.html?d=nginx1.example.com
                    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

                   IMPORTANT NOTES:
                  - Congratulations! Your certificate and chain have been saved at:
                  /etc/letsencrypt/live/nginx1.example.com/fullchain.pem
                   Your key file has been saved at:
                  /etc/letsencrypt/live/nginx1.example.com/privkey.pem
                  Your cert will expire on 2019-10-07. To obtain a new or tweaked
                   version of this certificate in the future, simply run certbot again
                   with the "certonly" option. To non-interactively renew *all* of
                    your certificates, run "certbot renew"
                    - If you like Certbot, please consider supporting our work by:

                     Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
                      Donating to EFF:                    https://eff.org/donate-le
                    {{< /output >}}

After adding all websites, perform a dry run to test certificate renewal. Ensure that all websites are updating successfully to confirm that the automated facility updated the certificates without further effort.

                       sudo certbot renew --dry-run

                   {{< output >}}
                     Saving debug log to /var/log/letsencrypt/letsencrypt.log

                   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                     Processing /etc/letsencrypt/renewal/apache1.example.com.conf
                   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                   Cert not due for renewal, but simulating renewal for dry run
                   Plugins selected: Authenticator nginx, Installer nginx
                    Renewing an existing certificate
                    Performing the following challenges:
                    http-01 challenge for apache1.example.com
                    Waiting for verification...
                    Cleaning up challenges

                   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                   new certificate deployed with reload of nginx server; fullchain is
                   /etc/letsencrypt/live/apache1.example.com/fullchain.pem
                   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

                    - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                   Processing /etc/letsencrypt/renewal/nginx1.example.com.conf
                   - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                    Cert not due for renewal, but simulating renewal for dry run
                     Plugins selected: Authenticator nginx, Installer nginx
                       Renewing an existing certificate
                        Performing the following challenges:
                         http-01 challenge for nginx1.example.com
                         Waiting for verification...
                         Cleaning up challenges

                      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                       new certificate deployed with reload of nginx server; fullchain is
                        /etc/letsencrypt/live/nginx1.example.com/fullchain.pem
                      - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

                     - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
                      ** DRY RUN: simulating 'certbot renew' close to cert expiry
                      **          (The test certificates below have not been saved.)

                        Congratulations, all renewals succeeded. The following certs have been renewed:
                        /etc/letsencrypt/live/apache1.example.com/fullchain.pem (success)
                        /etc/letsencrypt/live/nginx1.example.com/fullchain.pem (success)
                        ** DRY RUN: simulating 'certbot renew' close to cert expiry
                        **          (The test certificates above have not been saved.)
                     - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

                      IMPORTANT NOTES:
                        - Your account credentials have been saved in your Certbot
                        configuration directory at /etc/letsencrypt. You should make a
                        secure backup of this folder now. This configuration directory will
                        also contain certificates and private keys obtained by Certbot so
                         making regular backups of this folder is ideal.
                         {{< /output >}}

Note: The certbot package sets up a systemd timer to automate the renewal of Let's Encrypt certificates. You can view details of this timer by running systemctl list-timers.

The certbot tool modifies NGINX configuration files for your websites. However, it doesn't respect the initial listen directive (listen 80 proxy_protocol;) and fails to add the proxy_protocol parameter to the newly added listen 443 ssl; lines. You need to manually edit the configuration files for each website and append "proxy_protocol" to each "listen 443 ssl;" line.

                             sudo nano /etc/nginx/sites-enabled/apache1.example.com
                             sudo nano /etc/nginx/sites-enabled/nginx1.example.com

                        {{< output >}}
                          listen 443 ssl proxy_protocol; # managed by Certbot
                          listen [::]:443 ssl proxy_protocol; # managed by Certbot
                        {{< /output >}}

Note: Each website configuration file has two pairs of listen directives: one for HTTP and one for HTTPS. The first pair is the original HTTP setup added in a previous section, while the second pair is added by certbot for HTTPS. These pairs cover both IPv4 and IPv6. The notation [::] refers to IPv6. When adding the proxy_protocol parameter, ensure it is placed before the ; on each line.

Restart NGINX.

                             sudo systemctl restart nginx

## Troubleshooting

### Browser Error "SSL_ERROR_RX_RECORD_TOO_LONG"

You've configured Certbot and set up the necessary Let's Encrypt configuration for each website. However, when you try to access the website from your browser, you encounter the following error.

                               {{< output >}}
                                Secure Connection Failed

                An error occurred during a connection to apache1.example.com. SSL received a record that exceeded the maximum permissible length. Error code: SSL_ERROR_RX_RECORD_TOO_LONG

                The page you are trying to view cannot be shown because the authenticity of the received data could not be verified.
                 Please contact the website owners to inform them of this problem.
                               {{< /output >}}

This error occurs when the NGINX reverse proxy in the proxy container lacks the proxy_protocol parameter in the listen 443 directives. Without this parameter, the reverse proxy doesn't interpret the PROXY protocol information before handling HTTPS requests. It mistakenly forwards the PROXY protocol information to the HTTPS module, resulting in the record too long error.

Follow the instructions from the previous section to add proxy_protocol to all listen 443 directives. Then, restart NGINX.

### Error "Unable to connect" or "This site can’t be reached"

If you encounter Unable to connect or This site can't be reached errors when attempting to connect to the website from your local computer, it's likely that the proxy devices haven't been configured.

Run the following command on the host to verify whether LXD is listening and able to accept connections on ports 80 (HTTP) and 443 (HTTPS).

                            sudo ss -ltp '( sport = :http || sport = :https )'

Note: The ss command is similar to netstat and lsof. It displays information about network connections. Here, we use it to verify whether there's a service listening on ports 80 and 443, and which service it is.

  -l: displays listening sockets,
  -t: shows only TCP sockets,
  -p: indicates which processes use those sockets,
  ( sport = :http || sport = :https ): filters only ports 80 and 443 (HTTP and HTTPS, respectively).
  In the output, verify that both ports 80 and 443 (HTTP and HTTPS, respectively) are in the LISTEN state, with the process listening identified as lxd.

                 {{< output >}}
                        State     Recv-Q  Send-Q   Local Address:Port   Peer Address:Port
                        LISTEN    0       128                  *:http              *:*       users:(("lxd",pid=1301,fd=7),("lxd",pid=1301,fd=5))
                        LISTEN    0       128                  *:https             *:*       users:(("lxd",pid=1349,fd=7),("lxd",pid=1349,fd=5))
                {{< /output >}}

If you see a process listed other than `lxd`, stop that service and restart the `proxy` container. By restarting the `proxy` container, LXD applies the proxy devices again.

### The Apache access.log Shows the IP Address of the Proxy Container

You've configured the apache1 container and confirmed its accessibility from the internet. However, the logs at /var/log/apache2/access.log continue to display the private IP address of the proxy container, either the private IPv4 (10.x.x.x) or the private IPv6 addresses. What went wrong?

The default log formats in Apache print the IP address of the last hop's host (i.e., the proxy server) using the %h format specifier, as shown below.

                   {{< output >}}
                        LogFormat "%v:%p %h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
                        LogFormat "%h %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
                        LogFormat "%h %l %u %t \"%r\" %>s %O" common
                  {{< /output >}}

To display the value as returned by the real RemoteIP Apache module, you need to manually replace %h with %a.

                   {{< output >}}
                      LogFormat "%v:%p %a %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" vhost_combined
                      LogFormat "%a %l %u %t \"%r\" %>s %O \"%{Referer}i\" \"%{User-Agent}i\"" combined
                      LogFormat "%a %l %u %t \"%r\" %>s %O" common
                   {{< /output >}}

Execute the following command in the apache1 container to edit the configuration file httpd.conf and update the format specifier from %h to %a.

                       sudo nano /etc/apache2/apache2.conf

Reload the Apache web server service.

                         sudo systemctl reload apache2

## Next Steps

You've configured a reverse proxy to host multiple websites on the same server, with each website installed in a separate container. You can install either static or dynamic websites in these containers. For dynamic websites, additional configuration may be required; refer to the respective documentation for setting up with a reverse proxy. Additionally, you can use NGINX as a reverse proxy for non-HTTP(S) services.
