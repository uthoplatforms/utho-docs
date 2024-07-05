---
slug: "Light Up Your Server: Building a LAMP Stack with Salt States"
description: 'Discover how to leverage Salt States to effortlessly deploy a LAMP stack on your Utho, with adaptable steps suitable for Debian 8 and other Linux distributions.'
keywords: ["salt", "salt states", "linux", "apache", "mysql", "php", "debian 8"]
tags: ["automation","salt","debian","lamp","apache"]
modified: 2024-04-02
modified_by:
    name: Utho
published: 2024-04-02
title: Use Salt States to Configure a LAMP Stack on a Minion
authors: ["Utho"]
---
This guide will help you set up a LAMP stack on your Minion using Salt States. While it's written for Debian 8, you can adapt it for other Linux distributions. Before starting, ensure you have a functional Salt master and minion configuration. If not, refer to our [Salt installation guide](/docs/guides/getting-started-with-salt-basic-installation-and-setup/) to get started.

## Create the LAMP Configuration States
Follow these steps to configure all Salt Minions for a 2GB Utho, and feel free to adjust as needed.
1.  Open the `/etc/salt/base/top.sls` file and add the following line:

        {{< file "/etc/salt/base/top.sls" >}}
        base:
        '*':
        - lamp
        - extras
        - lampconf

        {{< /file >}}


2.  Create and edit the `/etc/salt/base/lampconf.sls` file with the following content:

        {{< file "/etc/salt/base/lampconf.sls" >}}

#Apache Conguration for 2GB Utho

            /etc/apache2/apache2.conf-KA:
            file.replace:
            - name: /etc/apache2/apache2.conf
            - pattern: 'KeepAlive On'
            - repl: 'KeepAlive Off'
            - show_changes: True

            /etc/apache2/apache2.conf-IM:
            file.append:
            - name: /etc/apache2/apache2.conf
            - text: |
            <IfModule mpm_prefork_module>
            StartServers 4
            MinSpareServers 20
            MaxSpareServers 40
            MaxClients 200
            MaxRequestsPerChild 4500
            </IfModule>

# MySQL Configuration for 2GB Utho

      /etc/mysql/my.cnf-br:
      file.blockreplace:
      - name: /etc/mysql/my.cnf
      - marker_start: '# * Fine Tuning'
      - marker_end: '# * Query Cache Configuration'
      - content: |
        #
        key_buffer             = 32M
        max_allowed_packet     = 1M
        thread_stack           = 128K
        thread_cache_size      = 8
        # This replaces the startup script and checks MyISAM tables if
        # needed the first time they are touched
        myisam-recover         = BACKUP
        max_connections        = 75
        table_cache            = 32
        #thread_concurrency    = 10
        #
     - backup: '.bak'
     - show_changes: True

# PHP Configuration for 2GB Utho

    /etc/php5/apache2/php.ini-er:
      file.replace:
    - name: /etc/php5/apache2/php.ini
    - pattern: 'error_reporting = E_ALL & ~E_DEPRECATED & ~E_STRICT'
    - repl: 'error_reporting = E_COMPILE_ERROR|E_RECOVERABLE_ERROR|E_ERROR|E_CORE_ERROR'
    - show_changes: True

    /etc/php5/apache2/php.ini-el:
    file.replace:
    - name: /etc/php5/apache2/php.ini
    - pattern: ';error_log = php_errors.log'
    - repl: 'error_log = /var/log/php/error.log'
    - show_changes: True

    /var/log/php/error.log:
    file.managed:
    - user: www-data
    - group: root
    - dir_mode: 755
    - file_mode: 644
    - makedirs: True

# Restart
    apache2-run-at-boot-restart:
    service.running:
    - name: apache2
    - enable: True
    - watch:
    - pkg: apache2

    mysql-run-at-boot-restart:
    service.running:
    - name: mysql
    - enable: True
    - watch:
    - pkg: mysql-server

    {{< /file >}}

The above file utilizes the [file](http://docs.saltproject.io/en/latest/ref/states/all/salt.states.file.html) and [service](http://docs.saltproject.io/en/latest/ref/states/all/salt.states.service.html) Salt State modules.

3.  Apply the state settings to the minions.

        salt '*' state.highstate

## Create Virtual Hosts Files
Salt State Modules are designed to configure settings across groups of minions. If you need to adjust a configuration on a single minion, consider using Salt Execution Modules instead. Keep in mind that there are multiple ways to utilize Salt's capabilities.

1.  To disable the default Apache virtual host on either a single minion or all minions, you can use the following Salt command:

For a specific Minion:

        salt '<hostname or Minion ID>' cmd.run "a2dissite *default"

For all Minions:

        salt '*' cmd.run "a2dissite *default"

2. Create directories for the website's files, logs, and backups. Replace `example.com` with the name of your website:

        salt '<hostname or Minion ID>' file.makedirs /var/www/example.com/public_html/
        salt '<hostname or Minion ID>' file.makedirs /var/www/example.com/log/
        salt '<hostname or Minion ID>' file.makedirs /var/www/example.com/backups/

3.  Create a directory on the Master to store all of the Minion virtual host files. This directory will serve as an index for all of the Minion websites.

        mkdir /etc/salt/base/minionsites

4.  Create the `/etc/salt/base/minionsites/example.com.conf` vhost file for the specified Minion. Make sure to replace `example.com` in all commands and configurations.

        {{< file "/etc/salt/base/minionsites/example.com.conf" >}}

        domain: example.com
        public: /var/www/example.com/public_html/

        <VirtualHost *:80>
        # Admin email, Server Name (domain name), and any aliases
        ServerAdmin webmaster@example.com
        ServerName  www.example.com
        ServerAlias example.com

        # Index file and Document Root (where the public files are located)
        DirectoryIndex index.html index.php
        DocumentRoot /var/www/example.com/public_html
        # Log file locations
        LogLevel warn
        ErrorLog  /var/www/example.com/log/error.log
        CustomLog /var/www/example.com/log/access.log combined
        </VirtualHost>

         {{< /file >}}

5. Copy the vhost file from the Master to the `/sites-available` directory of the Minion.

        salt-cp '<hostname or Minion ID>' /etc/salt/base/minionsites/example.com.conf /etc/apache2/sites-available/example.com.conf

6.  Enable the new website and restart Apache to apply the changes.

        salt '<hostname or Minion ID>' cmd.run "a2ensite example.com.conf"
        salt '<hostname or Minion ID>' cmd.run "service apache2 reload"

The above steps utilized the Salt Execution modules [cmdmod](http://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.cmdmod.html), [file](http://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.file.html), and [cp](http://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.cp.html).

At this point, you should have successfully configured a LAMP stack across your desired Minions. For further customization and the application of specific variables to each host, you can optionally utilize [grains](http://docs.saltproject.io/en/latest/topics/targeting/grains.html).
