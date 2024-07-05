---
slug: "Get Started with Riot: Installing Riot on Debian 10"
description: 'Riot is a highly secure instant messaging application utilizing the Matrix protocol. This guide offers comprehensive instructions for setting up Riot/Matrix on Debian 10, ensuring a seamless installation process'
keywords: ['riot', 'matrix', 'chat', 'debian']
published: 2024-05-09
modified_by:
  name: Utho
title: "Install Riot on Debian 10"
title_meta: "How to Install Riot on Debian 10"
authors: ["Pawan Kumar"]
---
Element (formerly known as **Riot**) is a comprehensive open-source communication platform. With Element, you can chat, share files, make voice or video calls, and incorporate bots—all while maintaining control over your data. Element serves as a web client for the[Matrix](https://matrix.org/clients/) protocol.

## Before You Begin

If you haven't already, create a Utho account and set up a Compute Instance.

Follow our Setting Up and Securing a Compute Instance guide to ensure your system is up to date. Additionally, consider setting the timezone, configuring the hostname, creating a limited user account, and enhancing SSH security.

Note: If you opt to [configure a firewall](/docs/products/compute/compute-instances/guides/set-up-and-secure/#configure-a-firewall), remember to open ports 80 and 443 for the server when you reach the configure a firewall section of the guide.

To access Synapse/Matrix services with a client other than Element, you'll need a compatible [Matrix client](https://matrix.org/clients/).

Note: This guide is tailored for a non-root user. Commands requiring elevated privileges are prefixed with sudo. If you're unfamiliar with the `sudo` command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide.

## Setup DNS

In this walkthrough, the base domain used is `demochat.com`. Please ensure to replace demochat.com with your registered domain name in the following steps. It's recommended to assign a separate subdomain for each service as a standard practice.

For instance, create DNS records for:

- `demochat.com` (for the general website and hosting, including the `.well-known` path to advertise Matrix services)
- `matrix.demochat.com` (for Synapse)
- `riot.demochat.com` (for Riot)

Assign the public IP address of the Utho instance to each of the aforementioned DNS records.

For detailed guidance on configuring DNS entries, refer to [Add DNS Records](/docs/guides/set-up-web-server-host-website/#add-dns-records). Alternatively, consult your DNS provider's documentation if you're using an external DNS provider.

## Install Riot
### Install Synapse

1. Install Synapse using its Debian packages:

        sudo apt install -y lsb-release wget apt-transport-https
        sudo wget -O /usr/share/keyrings/matrix-org-archive-keyring.gpg https://packages.matrix.org/debian/matrix-org-archive-keyring.gpg
        echo "deb [signed-by=/usr/share/keyrings/matrix-org-archive-keyring.gpg] https://packages.matrix.org/debian/ $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/matrix-org.list
        sudo apt update
        sudo apt install matrix-synapse-py3

Enable registration on the Synapse instance by setting `enable_registration: true in your /etc/matrix-sysnapse/homeserver.yaml` file:

             {{< file "/etc/matrix-sysnapse/homeserver.yaml">}}
              ...
             enable_registration: true
              ...
             {{</ file >}}

              Restart Synapse to apply your configuration:

              sudo systemctl restart matrix-synapse

Configure the rest of Matrix to locate the server. The simplest method is to publish a `.well-known` file indicating the `hostname` and `port` where the Synapse for `demochat.com` is available.

             sudo mkdir -p /var/www/html/demochat.com/.well-known/matrix
             echo '{ "m.server": "demochat.com:443" }' | sudo tee /var/www/html/demochat.com/.well-known/matrix/server

### Install the Latest Riot/Web Release
In this section, you'll utilize the latest `.tgz` release from the [Element repository (formerly know as Vector and Riot)](https://github.com/vector-im/element-web/releases) to install the Matrix web client. Fetch the latest .tgz release from here and verify the GPG signature.

Create a new directory to store Riot, navigate into it, and install Riot from the latest release. As of writing, the latest version is `v1.5.15`. Remember to replace any instance of `1.5.15` with your desired version.

             sudo mkdir -p /var/www/html/riot.demochat.com
             cd /var/www/html/riot.demochat.com
             sudo wget https://github.com/vector-im/riot-web/releases/download/v1.5.15/riot-v1.5.15.tar.gz

Verify your installation's GnuPG signature

        sudo apt install -y gnupg
        sudo wget https://github.com/vector-im/riot-web/releases/download/v1.5.15/riot-v1.5.15.tar.gz.asc

Obtain the signing key for the riot releases repository, preferably from a key server

        sudo gpg --keyserver keyserver.ubuntu.com --search-keys releases@riot.im

Verify that you receive a `Good signature` response when you validate the signature.

        sudo gpg --verify riot-v1.5.15.tar.gz.asc

Extract the Riot `.tar.gz` file you installed. Be sure to replace `v1.5.15` with your own installation's version number.

        sudo tar -xzvf riot-v1.5.15.tar.gz
        sudo ln -s riot-v1.5.15 riot

Navigate into the `riot` directory and create a copy of the `config.sample.json`, naming it config.json.

        cd riot
        sudo cp config.sample.json config.json

Open the `config.json` file you created and update `base_url` and `server_name` with the values provided in the example file.


              {{< file "/var/www/html/riot.demochat.com/riot/config.json" >}}
              {
              "default_server_config": {
              "m.homeserver": {
              "base_url": "https://matrix.demochat.com"
              "server_name": "demochat.com"
               },
              "m.identity_server": {
              "base_url": "https://matrix.demochat.com"
              }
             }
             ...
             }
             ...
            {{</ file >}}

### Install and Configure NGINX and Let's Encrypt

In this segment, you'll set up [NGINX](https://www.nginx.com/) as your web server and [Let's Encrypt](https://letsencrypt.org/) to secure your services.

Install NGINX:

        sudo apt -y install nginx

Create the vhost files for each subdomain:

        sudo touch /etc/nginx/sites-available/{demochat.com,matrix.demochat.com,riot.demochat.com}
        ln -s /etc/nginx/sites-available/demochat.com /etc/nginx/sites-enabled/demochat.com
        ln -s /etc/nginx/sites-available/matrix.demochat.com /etc/nginx/sites-enabled/matrix.demochat.com
        ln -s /etc/nginx/sites-available/riot.demochat.com /etc/nginx/sites-enabled/riot.demochat.com

Add the site configuration to each of the configuration files you created in the preceding step.

        {{< file "/etc/nginx/sites-available/demochat.com" nginx >}}
        server {
        listen 80;
        listen [::]:80;

        server_name demochat.com;
        root /var/www/html/demochat.com;
        index index.html;

        location / {
            try_files $uri $uri/ =404;
        }
      }
        {{< /file >}}

        {{< file "/etc/nginx/sites-available/riot.demochat.com" nginx >}}
        server {
        listen 80;
        listen [::]:80;

        server_name riot.demochat.com;
        root /var/www/html/riot.demochat.com/riot;
        index index.html;

        location / {
            try_files $uri $uri/ =404;
        }
      }
        {{< /file >}}

        {{< file "/etc/nginx/sites-available/matrix.demochat.com" nginx >}}
        server {
        listen 80;
        listen [::]:80;

        server_name matrix.demochat.com;
        root /var/www/html/matrix.demochat.com;
        index index.html;

        location / {
            proxy_pass http://localhost:8008;
         }
        }
        {{< /file >}}

Install [certbot](https://certbot.eff.org/) and configure your Let's Encrypt certificates:

        sudo apt install -y python3-certbot-nginx && certbot --nginx -d demochat.com -d riot.demochat.com -d matrix.demochat.com

### Enable Your Installation's Services

Add services to the default run level and restart your services:

        sudo systemctl enable nginx
        sudo systemctl enable matrix-synapse
        sudo systemctl restart nginx

You should now be able to visit https://riot.demochat.com, register an account, sign in, and start chatting.
