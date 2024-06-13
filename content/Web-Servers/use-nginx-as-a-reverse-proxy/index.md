---
title: "Use NGINX as a Reverse Proxy"
date: "2020-06-19"
---

A reverse proxy is a server between internal and external clients which transmits clients requests to a different server. Although other standard applications, such as Node.js, can support themselves, NGINX has a range of advanced load balancing, safety and speed features that most specialized applications do not have. The reverse proxy of NGINX helps you to apply these functions to any program.

![](images/Use-NGINX-as-a-Reverse-Proxy-1-1024x576.png)

A simple Node.js app to illustrate how to configure NGINX as reverse proxy is used for this tutorial.

## Install NGINX

These measures are used to install NGINX Mainline from the official NGINX Inc repository on Ubuntu. See the NGINX admin guide for other distributions. See our Getting Started with the NGINX series for information about configuring NGINX for production environments.

1\. In the text editor, Open `/etc/apt/sources.list` and add the next line to the right. Substitute `CODENAME`  by your Ubuntu release codename in this case. For eg, the bionic insert in place of `CODENAME`  below for Ubuntu 18.04 named `bionic`  Beaver:

```file {title="/etc/apt/sources.list" lang="aconf"}
deb http://nginx.org/packages/mainline/ubuntu/ CODENAME nginx
```

2\. Import the signing key for the repository and link it to `apt`:

```
sudo wget http://nginx.org/keys/nginx_signing.key sudo apt-key add nginx_signing.key
```

3\. Install NGINX:

```
sudo apt update sudo apt install nginx
```

4\. Ensure that NGINX works and can automatically continue when reboot:

```
sudo systemctl start nginx sudo systemctl enable nginx
```

## Create an Example App

### Install Node.js

1\. Using curl to access the NodeSource installation file. Replace the version of the node with the version you want to install in the curl command:

```
curl -sL https://deb.nodesource.com/setup_9.x -o nodesource_setup.sh
```

2\. Run the script:

```
sudo bash nodesource_setup.sh
```

3\. The setup script will run an `apt-get update` it automatically, so you can install Node.js right away:

```
sudo apt install nodejs npm
```

Next to Node.js will be installed the Node Package Manager (NPM).

## Configure the App

1\. Make a directory for the example app:

```
mkdir nodeapp && cd nodeapp
```

2\. Initialize a Node.js app within the directory:

```
npm init
```

Accept all the defaults when prompted.

3\. Install Express.js:

```
npm install --save express
```

4\. Use a **text** editor to create app.js and add **the below** content:

```file {title="app.js" lang="aconf"}
const express = require('express') const app = express() app.get('/', (req, res) => res.send('Microhost Cloud!')) app.listen(3000, () => console.log('Node.js app listening on port 3000.')) 7
```

5\. Run the app:

```
node app.js
```



6\. In a new terminal window, use curl to verify that the app is working on localhost

```
curl localhost:3000
```

```
 Microhost Cloud!
```

## Configure NGINX

You can configure Node.js to use a sample app on the public IP address of your cloud server to view the device on the internet. This segment then configures NGINX such that all requests from the public IP address are forwarded to the server on `localhost` already listening.

## Basic Configuration for NGINX Reverse Proxy

1\. Create a configuration **file** for the app in `/etc/nginx/conf.d/`. Replace `abc.com`  into your app’s domain or public IP address:

```file {title="/etc/nginx/conf.d/nodeapp.conf" lang="aconf"}
server { listen 80; listen [::]:80; server_name abc.com; location / { proxy_pass http://localhost:3000/; b } }
```

This setup is a reverse proxy through the `proxy_pass`  command. It specifies to forward to port `3000`  on locals all applications corresponding to the location block (in this case, the root / path) on the `localhost`, where the Node.js application is running.

2\. Disable or delete default Welcome to NGINX page:

```
sudo mv /etc/nginx/conf.d/default.conf /etc/nginx/conf.d/default.conf.disabled
```

3\. Test the configuration:

```
sudo nginx -t
```

4\. Reload the new configuration if no errors are reported:

```
sudo nginx -s reload
```

5\. In a browser, navigate **your** cloud server’s public IP address. Now it should be show the “Microhost cloud” message displayed.

## Advanced Options

The `proxy_pass`  directive is enough for a simple application. More complex apps can however require additional guidance. For instance, Node.js is often used for applications that involve several real-time interactions. Disable NGINX buffering feature to accommodate:

```file {title="/etc/nginx/conf.d/nodeapp.conf" lang="aconf"}
location / { proxy_pass http://localhost:3000/; proxy_buffering off; }
```

You may also modify or delete the headers forwarded with the `proxy_set_header` request:

```file {title="/etc/nginx/conf.d/nodeapp.conf" lang="aconf"}
location / { proxy_pass http://localhost:3000/; proxy_set_header X-Real-IP $remote_addr; }
```

This configuration uses the `$remote_addr`  variable to give the original client IP address to the proxy host.

## Configure HTTPS with Certbot

A reverse proxy has the advantage that HTTPS can easily be set up with a TLS certificate. Certbot is a device that enables you to get free Let's Encrypt certificates quickly. On Ubuntu 16.04 this guide is going to use Certbot, but the official website holds complete installation and guidance for all major districts.

Use below these steps to receive a Certbot certificate. In order to use the new certificate, Certbot automatically updates your NGINX settings files:

1\. Install the Certbot and **internet** server-**specific** packages, then run Certbot:

```
sudo apt-get update sudo apt-get install software-properties-common sudo add-apt-repository ppa:certbot/certbot sudo apt-get update sudo apt-get install python-certbot-nginx sudo certbot --nginx
```

2\. Certbot is going to ask for website details. As part of the certificate, the answer will be saved:

```
 # sudo certbot --nginx Saving debug log to /var/log/letsencrypt/letsencrypt.log Plugins selected: Authenticator nginx, Installer nginx What names do you want HTTPS allowed for? 1: abc.com 2: www.abc.com Select the correct commas and/or spaces separated numbers, or leave the input Select all of the choices displayed blankly (Enter ' c ' to cancel):
```

3\. Certbot will also ask if you would like to routinely redirect HTTP visitors to HTTPS visitors. It is suggested that you choose this option.

4\. Once the tool is completed, Certbot will store all of the created keys and certificates into`/etc/letsencrypt/live/$domain` , where `$domain`  is the name of the domain entered during the generation stage of the certificate.

Note:- Certbot recommends pointing your **web** server configuration to the default **certificate directory** or **creating** symlinks. Keys and **certificate** should **not** be moved to a **special directory**.

Finally, Certbot will replace your internet server configuration in order that it makes use of the new certificate, and also redirects HTTP visitors to HTTPS if you selected that option.

5\. If you've a firewall configured on your Cloud server, you need to add a firewall rule to allow incoming and outgoing connections to the HTTPS service. On Ubuntu, UFW is a commonly used and simple tool for handling firewall rules. for HTTP and HTTPS traffic Install and configure UFW:

```
# sudo apt install ufw sudo systemctl start ufw sudo systemctl enable ufw sudo ufw allow http sudo ufw allow https sudo ufw enable
```

Thankyou.......
