---
slug: setting-up-jupyter-notebook-server-on-Utho-with-apache-reverse-proxy
description: 'This tutorial illustrates the process of securely installing and accessing a Jupyter notebook on a Utho server remotely, using an Apache reverse proxy'
keywords: ["Apache2", "Jupyter notebook", "SSL", "websocket"]
tags: ["ssl", "proxy", "apache"]
published: 2024-03-18
modified: 2024-03-18
modified_by:
    name: Utho
title: 'Setting Up a Jupyter Notebook on Utho with Apache Reverse Proxy'
title_meta: 'Installing a Jupyter Notebook Server Behind a Reverse Proxy'
dedicated_cpu_link: true
authors: ["Pawan Kumar"]
---

Jupyter Notebook is an interactive, feature-rich shell that operates directly within a web browser. Widely embraced by data scientists, it offers inline rendering of figures, versatile exporting options, and LaTeX support for mathematical notation. This guide focuses on setting up a public Jupyter Notebook server on a Utho, enabling remote access to your computational tasks via Apache acting as a reverse proxy.

{{< note respectIndent=false >}}

Jupyter Notebook is being succeeded by [JupyterLab](https://jupyterlab.readthedocs.io/en/stable/getting_started/overview.html), the next-generation solution that incorporates Notebooks. Prior to proceeding, evaluate whether JupyterLab better aligns with your requirements.

{{< /note >}}

## Before You Begin

As this guide is tailored for Uthos operating on Ubuntu 16.04, please ensure to:

1. If not already completed, establish a Utho account and Compute Instance.
2. Refer to our system update guide. Additionally, consider adjusting the timezone, configuring the hostname, creating a restricted user account, and enhancing SSH access security.

## Install Anaconda Package Manager

Anaconda is a package manager that includes built-in support for virtual environments and is endorsed by Jupyter's official documentation.

1.  Access your Utho via SSH and install the latest Anaconda version. The command below downloads Anaconda version with Python 3.6 (Python 2.7 is also available):

        wget https://repo.continuum.io/archive/Anaconda3-4.4.0-Linux-x86_64.sh

2.  Execute the installation script:

        bash ~/Anaconda3-4.4.0-Linux-x86_64.sh

3.  Follow the instructions in the terminal, agree to the terms, and permit the installer to create a PATH in `.bashrc`.

4.  Update the changes made to `.bashrc` by reloading it with:

        exec bash

## Generating a Self-Signed Certificate

To enhance security and prevent the transmission of unencrypted passwords from the browser to the Notebook, it's recommended to generate a self-signed SSL certificate, as advised in the official documentation. This precaution is particularly crucial since Jupyter Notebooks can execute bash scripts. If you have a domain name, it's preferable to utilize [Certbot](/docs/guides/secure-http-traffic-certbot/) instead of a self-signed certificate.

1.  Generate a self-signed certificate valid for 365 days:

        openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout mykey.key -out mycert.pem

Executing this command will generate `mykey.key` and `mycert.pem`.

2.  Limit the files to be readable solely by the owner:

        chmod 400 mykey.key
        chmod 400 mycert.pem

## Configuring Jupyter Notebook Settings

1.  Create a new configuration file, which will result in the creation of a `~/.jupyter directory`:

        jupyter notebook --generate-config

2.  Set a password for the notebook:

        jupyter notebook password

3.  Copy the password from the newly generated `jupyter_notebook_config.json` file.

4.  Uncomment the following lines in the configuration file:

        {{< file "~/.jupyter/jupyter_notebook_config.py" py >}}
        c.NotebookApp.allow_origin = '*'
        c.NotebookApp.base_url = '/jupyter'
        c.NotebookApp.certfile = '/absolute/path/to/mycert.pem'
        c.NotebookApp.ip = 'localhost'
        c.NotebookApp.keyfile = '/absolute/path/to/mykey.key'
        c.NotebookApp.open_browser = False
        c.NotebookApp.password = 'paste_hashed_password_here'
        c.NotebookApp.trust_xheaders = True
        {{< /file >}}

## Configuring Apache Reverse Proxy

1.  Install Apache 2.4:

        sudo apt install apache2

2.  Enable a2enmod:

        sudo a2enmod

You will see a prompt displaying a list of Apache mods:

        Your choices are: access_compat actions alias allowmethods asis auth_basic auth_digest auth_form authn_anon authn_core authn_dbd authn_dbm authn_file authn_socache authnz_fcgi authnz_ldap authz_core authz_dbd authz_dbm authz_groupfile authz_host authz_owner authz_user autoindex buffer cache cache_disk cache_socache cgi cgid charset_lite data dav dav_fs dav_lock dbd deflate dialup dir dump_io echo env expires ext_filter file_cache filter headers heartbeat heartmonitor ident include info lbmethod_bybusyness lbmethod_byrequests lbmethod_bytraffic lbmethod_heartbeat ldap log_debug log_forensic lua macro mime mime_magic mpm_event mpm_prefork mpm_worker negotiation proxy proxy_ajp proxy_balancer proxy_connect proxy_express proxy_fcgi proxy_fdpass proxy_ftp proxy_html proxy_http proxy_scgi proxy_wstunnel ratelimit reflector remoteip reqtimeout request rewrite sed session session_cookie session_crypto session_dbd setenvif slotmem_plain slotmem_shm socache_dbm socache_memcache socache_shmcb spelling ssl status substitute suexec unique_id userdir usertrack vhost_alias xml2enc

        Which module(s) do you want to enable (wildcards ok)?

3. Activate `mod_proxy`, `mod_proxy_http`, `mod_proxy_wstunnel`, `mod_ssl`, and `mod_headers`:

        proxy proxy_http proxy_https proxy_wstunnel ssl headers

4.  Navigate to the `/etc/apache2/sites-available` directory. Copy the default configuration file then add directives on virtualhost:

        sudo cp 000-default.conf jupyter.conf

5.  To enable redirection from `https://your-domain-name/` to `https://your-domain-name/jupyte`, comment out the `DocumentRoot` directive. Additionally, utilize the `<Location>` directive to establish a connection for the websocket, enabling the default kernel to run:

        {{< file "/etc/apache2/sites-available/jupyter.conf" apache >}}
        <VirtualHost *:443>
        ServerAdmin webmaster@localhost

#   DocumentRoot /var/www/html

    ErrorLog ${APACHE_LOG_DIR}.error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    SSLCertificateFile /absolute/path/to/mycert.pem
    SSLCertificateKeyFile /absolute/path/to/mykey.key
    SSLProxyEngine On
    SSLProxyVerify none
    SSLProxyCheckPeerCN off
    SSLProxyCheckPeerName off
    SSLProxyCheckPeerExpire off

    ServerName localhost
    ProxyPreserveHost On
    ProxyRequests Off
    LogLevel debug

    ProxyPass /jupyter https://localhost:8888/jupyter
    ProxyPassReverse /jupyter https://localhost:8888/jupyter
    RequestHeader set Origin "https://localhost:8888"
    Redirect permanent / https://your-domain-name/jupyter

    <Location "/jupyter/api/kernels">
        ProxyPass wss://localhost:8888/jupyter/api/kernels
        ProxyPassReverse wss://localhost:8888/jupyter/api/kernels
    </Location>

</VirtualHost>

{{< /file >}}


    {{< note respectIndent=false >}}

You can choose any name for the `/jupyter` URL path, as long as it corresponds to the base URL path specified in the Jupyter notebook configuration file.

{{< /note >}}

6.  Activate the newly created configuration:

        sudo a2ensite jupyter.conf

7.  Restart the Apache server to apply changes:

        sudo service apache2 restart

8.  Launch the Jupyter Notebook:

        jupyter notebook

## Run Jupyter Notebook

1.  On your local machine, go to `https://your-domain-name/` where `your-domain-name` is the IP address of your Utho or your chosen domain name. If you're using a self-signed certificate, your browser might prompt you to confirm a security exception:

2.  If Apache is configured correctly, Jupyter will prompt you to log in:

3.  Create a new notebook using a Python kernel:

4.  The Notebook is prepared to execute Python code or any additional kernels added later:

Please note that this setup is designed for single-user usage only. Concurrent users on the same Notebook may lead to unpredictable outcomes. For a multi-user server, it's advisable to utilize [JupyterHub](https://github.com/jupyterhub/jupyterhub)instead.
