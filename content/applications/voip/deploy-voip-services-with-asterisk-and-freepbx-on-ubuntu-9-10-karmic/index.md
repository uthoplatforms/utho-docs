---
slug: 'Deploy VoIP Services with Asterisk and FreePBX on Ubuntu 9.10 (Karmic)'
deprecated: true
description: 'This guide will show you how to install Asterisk and FreePBX on Your Utho to Use and Manage a Telephone Exchange, also known as a PBX, on Ubuntu 9.10 "Karmic'
keywords: ["asterisk ubuntu 9.10", "asterisk", "asterisk linux", "freepbx", "freepbx ubuntu", "pbx", "voip"]
tags: ["ubuntu", "lamp"]
modified: 2024-06-18
modified_by:
  name: Utho
published: 2024-06-18
title: 'Deploy VoIP Services with Asterisk and FreePBX on Ubuntu 9.10 (Karmic)'
dedicated_cpu_link: true
relations:
    platform:
        key: asterisk-freepbx-telephone
        keywords:
            - distribution: Ubuntu 9.10
authors: ["Utho"]
---

Asterisk is a free and open-source telephone system that operates over the internet instead of traditional phone lines. It offers various features such as voicemail and conference calling, similar to a standard landline phone.

Before starting, ensure a few prerequisites are met. Assuming you've already followed the steps in Setting Up and Securing a Compute Instance to set the hostname, timezone, and configure networking. These steps are crucial for Asterisk to function correctly. If you plan to use Asterisk's email capabilities, consider adding an A record for your domain as well.

Important: Due to specific configuration needs, avoid running other services on the Utho you're using for Asterisk. Additionally, this guide uses `pv_grub`, and any deviations from these steps won't be supported.

This guide is primarily based on Ryan Tucker's guide, adapted with some changes to procedures and software used.

## Prerequisites

Before installing Asterisk and FreePBX, there are several prerequisites to fulfill. Specifically, you'll need to install a kernel module and adjust your Utho's configuration profile. This document will provide step-by-step instructions for these tasks, but for more detailed information, refer to the `pv-grub` guide.

### Edit Sources List

To install Asterisk, you must enable the universe repositories. Modify `/etc/apt/sources.list` to resemble the following configuration:


                                          {{< file "/etc/apt/sources.list" >}}

## main & restricted repositories

                                       deb http://us.archive.ubuntu.com/ubuntu/ karmic main restricted
                                       deb-src http://us.archive.ubuntu.com/ubuntu/ karmic main restricted

                                       deb http://security.ubuntu.com/ubuntu karmic-security main restricted
                                       deb-src http://security.ubuntu.com/ubuntu karmic-security main restricted

## universe repositories

                                        deb http://us.archive.ubuntu.com/ubuntu/ karmic universe
                                        deb-src http://us.archive.ubuntu.com/ubuntu/ karmic universe
                                        deb http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe
                                        deb-src http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe

                                        deb http://security.ubuntu.com/ubuntu karmic-security universe
                                        deb-src http://security.ubuntu.com/ubuntu karmic-security universe

                                          {{< /file >}}

Run these commands to update your package lists and upgrade packages on your Utho:

                                       apt-get update
                                       apt-get upgrade

### Create Asterisk User

Let's create a user specifically to run Asterisk, ensuring we don't operate everything as root. Enter the following command:

                                        adduser asterisk

You'll be asked to provide a password and some details for the user, such as name and phone number. Fill in the password, but for the other entries, you can safely press "Enter".

## Configure the Kernel

To prepare your Utho for using the "pv-grub" kernel provided by Utho, follow these steps before updating your configuration profile. Asterisk utilizes the "dahdi_dummy" kernel module, which necessitates editing certain kernel settings and your Utho's configuration profile. To simplify this process, you can utilize the EC2 image. Issue the following command:

                                         apt-get install linux-image-ec2

### Install and Configure Grub

GRUB is a bootloader necessary for booting the kernel we're configuring. To set it up, install GRUB and create a default configuration file using the following commands:

                                          apt-get install grub
                                          update-grub

You'll get a prompt indicating that the `/boot/grub/menu.lst` file doesn't exist and asking if you want to create it. Type "y" to create the file. Next, you'll need to edit the `/boot/grub/menu.lst` file, specifically focusing on the "groot" line. Note: Lines in this file may appear commented out with hash marks ("#"). Keep the hash marks unchanged.

Modify the "groot" line to look like this:

                                          {{< file "/boot/grub/menu.lst" >}}

## default grub root device
## e.g. groot=(hd0,0)
# groot=(hd0)
 
                                           {{< /file >}}

You should also modify the "indomU" line to look like this:

                                          {{< file "/boot/grub/menu.lst" >}}

# indomU=false

                                           {{< /file >}}

You now need to update Grub again to apply the changes. Issue the following command:

                                            update-grub

### Edit Configuration Profile

To change your Utho's configuration profile, log in to the Utho Manager. Navigate to the Dashboard page of your Utho intended for Asterisk. Click on the current profile and choose "pv-grub-x86_32" (or "pv-grub-x86_64" for 64-bit systems) from the kernel drop-down menu. Save this updated configuration profile. Consider renaming it to indicate it's no longer the default profile.

Reboot your system to apply these changes before proceeding further. Monitor the shutdown and reboot processes via Lish to check for any errors.

### Troubleshooting

It's crucial to follow the outlined steps carefully to ensure your system boots successfully. We highly recommend monitoring the console during shutdown and reboot phases using Lish. If your Utho fails to boot and displays an error, revert your configuration profile to the latest Paravirt kernel and review this guide to ensure all steps have been correctly followed.

## Install the Dahdi Module

Now, you'll install the Dahdi module to enable features such as conference calling. Use the following commands:

                                apt-get install build-essential module-assistant
                                module-assistant -t update
                                module-assistant -t prepare
                                apt-get install gawk
                                apt-get install dahdi dahdi-dkms dahdi-linux

## Install Asterisk

Now you're set to install Asterisk. Below is the command to install Asterisk along with some additional features. It's recommended to include these packages unless you're certain you won't need them:

              apt-get install asterisk asterisk-config asterisk-doc asterisk-mp3 asterisk-mysql asterisk-sounds-main asterisk-sounds-extra

If you want to use a US telephone number, enter "1" for the ITU-T telephone code. For numbers in other countries, choose the appropriate code for your country.

Asterisk comes with voice prompts in English by default. To install support for other languages, use the command(s) below:

                        apt-get install asterisk-prompt-de # German voice prompts for the Asterisk PBX
                        apt-get install asterisk-prompt-es-co # Colombian Spanish voice prompts for Asterisk
                        apt-get install asterisk-prompt-fr-armelle # French voice prompts for Asterisk by Armelle Desjardins
                        apt-get install asterisk-prompt-fr-proformatique # French voice prompts for Asterisk
                        apt-get install asterisk-prompt-fr # French voice prompts for Asterisk
                        apt-get install asterisk-prompt-it # Italian voice prompts for the Asterisk PBX
                        apt-get install asterisk-prompt-se # Swedish voice prompts for Asterisk

To start Asterisk, enter the following command:

                                     asterisk

To check if Asterisk is running, use:

                                     asterisk -r

This connects you to the Command Line Interface (CLI) for Asterisk. You can `exit` this interface by typing exit. For a list of available commands, type `help`.


After starting Asterisk, it's recommended to reboot your Utho to ensure everything functions correctly. Use the Utho Manager to perform the reboot.

## Installing FreePBX

FreePBX is a web-based PHP application designed for managing your Asterisk installation.

### Set Up LAMP Stack

Before you can use FreePBX, you need to set up a LAMP stack. Here's a quick overview, but for detailed instructions, you can refer to our LAMP documentation. To start installing Apache, run the following command:

                                     apt-get install apache2

To restrict Apache to listen only on specified IP addresses, start by editing the NameVirtualHost entry in `/etc/apache2/ports.conf` as shown below:

                                    {{< file "/etc/apache2/ports.conf" apache >}}
                                    NameVirtualHost 12.34.56.78:81

                                     {{< /file >}}

Make sure to replace "12.34.56.77" with your Utho's public IP address.

Next, adjust the virtual hosting configuration for the default site in the file `/etc/apache2/sites-available/default` so that the <VirtualHost > entry appears as follows:

                                      {{< file "/etc/apache2/sites-available/default" apache >}}
                                        <VirtualHost 12.34.56.78:81>

                                         {{< /file >}}

To start, install MySQL with the following command:

                                        apt-get install mysql-server

After the installation completes, secure the default MySQL installation by running:

                                        mysql_secure_installation

Follow the prompts to set a secure password and adjust other security settings.

Next, create the necessary databases with these commands:

                                         mysql -p

Make sure to replace 'CHANGEME' with a strong and secure password that you will remember.

                                       create database asterisk;
                                       create database asteriskcdr;
                                       GRANT ALL PRIVILEGES ON asteriskcdr.* TO asterisk@localhost IDENTIFIED BY 'CHANGEME';
                                       GRANT ALL PRIVILEGES ON asterisk.* TO asterisk@localhost IDENTIFIED BY 'CHANGEME';
                                       flush privileges;
                                        exit

To install PHP, use these commands:

                                       apt-get install php5 php-pear php5-mysql php5-suhosin php5-cgi php-pear php-db

Next, edit PHP's configuration file to set the memory_limit directive to "100M" for FreePBX compatibility:

Find the `memory_limit` line and adjust it to:

Save the file and exit the editor (Ctrl + X, then Y, then Enter). This ensures FreePBX has sufficient memory allocation to function properly.

                                      {{< file "/etc/php5/apache2/php.ini" >}}
                                      max_execution_time = 30
                                      memory_limit = 100M
                                      error_reporting = E_COMPILE_ERROR|E_RECOVERABLE_ERROR|E_ERROR|E_CORE_ERROR
                                      display_errors = Off
                                      log_errors = On
                                      error_log = /var/log/php.log
                                      register_globals = Off

                                      {{< /file >}}

To disable "magic quotes" in PHP for FreePBX, locate the following directives in your php.ini file and adjust them as shown below:

                                      {{< file "/etc/php5/apache2/php.ini" >}}
                                      magic_quotes_gpc = Off

                                      {{< /file >}}

To configure Apache to run as the Asterisk user for this installation, ensure your `/etc/apache2/envvars` file contains the following settings:
These configurations will enable Apache to access all necessary files required to run FreePBX securely under the Asterisk user.

                                       {{< file "/etc/apache2/envvars" >}}
                                       export APACHE_RUN_USER=asterisk
                                       export APACHE_RUN_GROUP=asterisk

                                        {{< /file >}}

Finally, restart Apache to ensure that all configurations are loaded correctly:

                                         /etc/init.d/apache2 restart

### Download and Extract FreePBX

To obtain FreePBX and unpack it, use the following commands:

                                              cd /opt
                                              wget http://mirror.freepbx.org/freepbx-2.6.0.tar.gz
                                              tar -xvf freepbx-2.6.0.tar.gz
                                              chown -R asterisk:asterisk freepbx*
                                              su - asterisk
                                              cd /opt/freepbx-2.6.0

Next, insert the SQL data into the MySQL database created earlier with these commands:

                                                mysql -p asterisk < SQL/newinstall.sql
                                                mysql -p asteriskcdr < SQL/cdr_mysql_table.sql
                                                exit

### Configuration

During the FreePBX configuration process, carefully answer the questions presented. While some default values are suitable for production systems, ensure to change passwords to enhance security during setup.

To configure FreePBX with your MySQL credentials, use the following command, replacing "CHANGEME" with your actual MySQL password:

                                                 ./install_amp --username=asterisk --password=CHANGEME

### Create VirtualHost

Prior to proceeding with your FreePBX installation, it is advisable to set up a `VirtualHost` for the web interface. Additionally, ensure to enhance the security of your installation by implementing SSL and configuring .htaccess settings.

To set up your FreePBX installation, you must create a `VirtualHost`. FreePBX typically installs its files to /var/www/html/ by default, which you can keep unchanged. Your VirtualHost configuration should look similar to the following example:


                                 {{< file "VirtualHost Entry" apache >}}
                                  <VirtualHost 12.34.56.78:81>
                                  ServerAdmin webmaster@e-cabi.net
                                  ServerName pbx.e-cabi.net
                                  ServerAlias pbx.e-cabi.net
                                  DocumentRoot /var/www/html
                                  </VirtualHost>

                                  {{< /file >}}


To update your Apache configuration, restart the server by executing the following command:

                                     /etc/init.d/apache2 restart

You'll also need to modify your `/etc/rc.local` file using the nano editor to enable certain FreePBX features during boot. Add the line `perl /var/www/html/panel/op_server.pl -d` to this file. Ensure that this line is placed before any lines containing "exit", as shown below:

                                   {{< file "/etc/rc.local" bash >}}
                                    # [..]

                                    perl /var/www/html/panel/op_server.pl -d
                                    exit 0

                                    {{< /file >}}


Now, you can access your Utho's IP address or the A record you've directed to your Utho using any web browser. Log in with the username asterisk and the password you chose during the FreePBX installation process. After successful login, you'll have full control over your Asterisk installation via FreePBX!

## More Information

Here are some additional resources you may find helpful for further information on this topic. While we provide these resources with the intention of assisting you, please note that we cannot guarantee the accuracy or current relevance of externally hosted materials.