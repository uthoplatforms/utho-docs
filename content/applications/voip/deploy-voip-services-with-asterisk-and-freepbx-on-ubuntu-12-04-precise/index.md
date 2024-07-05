---
slug: Deploy VoIP Services with Asterisk and FreePBX on Ubuntu 12.04 (Precise)
deprecated: true
description: 'This guide demonstrates how to install Asterisk and FreePBX on your Utho for setting up and managing a Telephone Exchange, commonly known as a PBX, on Ubuntu 12.04'
keywords: ["ubuntu 12.04", "asterisk", "freepbx", "pbx", "voip", "google voice", "grub", "lamp stack", "apache", "php"]
tags: ["ubuntu", "lamp"]
modified: 2024-06-18
modified_by:
  name: Pawan Kumar
published: 2044-06-18
title: 'Deploy VoIP Services with Asterisk and Freepbx on Ubuntu 12.04'
relations:
    platform:
        key: asterisk-freepbx-telephone
        keywords:
            - distribution: Ubuntu 12.04
authors: ["Pawan Kumar"]
---

Asterisk is a robust open-source telephony platform designed to operate over the Internet rather than traditional copper lines. It offers extensive features such as voicemail and conference calling, akin to those found in traditional landline systems.

In this guide, we will install Asterisk from source instead of using Ubuntu's repositories. This approach provides access to newer versions with enhanced capabilities, such as integration with Google Voice as a trunk. We will utilize FreePBX as a web-based interface to configure Asterisk.

Important Note: Due to specific configuration requirements for this setup, it's advisable not to run additional services on the Utho intended for Asterisk. Additionally, this guide assumes the use of PV-GRUB. Any deviations from these instructions may exceed the scope of support.

Note: The steps outlined in this guide require root privileges. Ensure to execute the following steps as root or using the sudo prefix. For more details on managing privileges, refer to our Users and Groups guide.

## Terms

If you're new to setting up a digital phone system, familiarizing yourself with some commonly used protocols and acronyms can be helpful:

- PBX (Private Branch Exchange) - A system that manages telephone switching and connections within an internal network. Asterisk with the FreePBX frontend will function as our PBX in this setup.

- Trunk - A service provider that facilitates incoming and outgoing phone calls for your PBX platform. Various providers offer different solutions. While this guide focuses on using Google Voice as your trunk, you can adapt these instructions for other trunk providers by following their specific setup procedures.

- DID (Direct Inward Dialing) number - Assigned by the trunk provider, this is the phone number(s) used by external callers to reach your PBX system.

- SIP (Session Initiation Protocol) - A protocol used to initiate and manage multimedia sessions over Internet Protocol (IP). Our PBX server will utilize SIP to communicate with both the trunk provider and client devices.

## Prerequisites

Before proceeding, ensure the following prerequisites are in place. We assume you have already completed the Setting Up and Securing a Compute Instance guide, which includes setting the hostname, configuring the timezone, and networking for your Utho. These steps are crucial for the proper functioning of your Asterisk installation. If you plan to utilize Asterisk's email features, consider adding an A record for your domain as well.

Several prerequisites must be met before installing Asterisk and FreePBX. This includes installing a kernel module and adjusting your Utho's configuration profile. We will detail these steps in this document. For a deeper understanding, refer to the comprehensive information available in the PV-GRUB guide.

This guide also covers the integration of a Google Voice account. To proceed, ensure you have a Google account with a configured Google Voice number.

### Install Required System Packages

Run the following commands to update your package lists and upgrade packages on your Utho:

                                        apt-get update
                                        apt-get upgrade

Install the necessary set of programs listed below, which will be required later to compile the Asterisk software from source:

        apt-get install build-essential wget libssl-dev libncurses5-dev libnewt-dev  libxml2-dev libsqlite3-dev uuid-dev libiksemel-utils libiksemel-dev

### Create Asterisk User

To enhance security, we will create a user specifically to run the Asterisk process instead of using the root account. Execute the following command:

                                         adduser asterisk

You will be prompted to enter a password and provide details such as the user's name and phone number. While you must fill out the password, you can safely press `Enter` for the other entries.

## Install the Kernel

To use the PV-GRUB kernel provided by Utho, follow these steps carefully. Note that any kernel modifications beyond those outlined in this document are not supported by Utho. Before updating your configuration profile, ensure your Utho is prepared by executing the commands in the next section.

Asterisk utilizes the dahdi_dummy kernel module, which requires adjustments to both the kernel and your Utho's configuration profile. This process is straightforward. Issue the following command to proceed:

                                        apt-get install linux-virtual

You will encounter a menu similar to the one below, prompting you to select a disk for installing GRUB. You can proceed without making a selection, as this version will be removed in the subsequent step.

### Install and Configure Grub

GRUB is a bootloader essential for booting the kernel we're configuring. You must install it and create a default configuration file. Issue the following commands:

                                       apt-get purge grub2 grub-pc
                                       apt-get install grub
                                       update-grub

You will be prompted with a warning indicating that the /boot/grub/menu.lst file does not exist and asked if you want to create it. Enter y to proceed with creating a new file.

Open and edit the `/boot/grub/menu.lst` file. 

Note: In this file, directives appear commented out with hash marks ("#"). Ensure to leave these hash marks unchanged.

Modify the groot line to match the following format:

                                      {{< file "/boot/grub/menu.lst" >}}

## default grub root device
## e.g. groot=(hd0,0)
# groot=(hd0)

                                        {{< /file >}}

Modify the `indomU` line to resemble the following:

                                       {{< file "/boot/grub/menu.lst" >}}

# indomU=false

                                         {{< /file >}}

Save and exit the file. Next, update GRUB to apply the changes by issuing the following command:

                                          update-grub

### Edit Configuration Profile

To update your Utho's configuration profile for Asterisk, follow these steps:

1. Log in to the Utho Manager.
2. Navigate to the Dashboard page of the Utho intended for Asterisk.
3. Click on the current profile and choose pv-grub-x86_64 (or pv-grub-x86_32 for 32-bit systems) from the kernel dropdown menu.
4. Save this updated configuration profile. Consider renaming it to indicate it's no longer the default profile.
5. Reboot your system to apply these changes. This step is necessary before proceeding. Monitor the shutdown and reboot phases via LISH to check for any errors.

### Troubleshoot

It is crucial to carefully follow the steps outlined above to ensure your system boots correctly. We strongly recommend monitoring the console during both the shutdown and reboot phases using LISH. If your Utho fails to boot and you encounter an error, revert your configuration profile to the latest Paravirt kernel and review this guide to ensure no steps were missed.

## Install Asterisk

The version of Asterisk available in the Ubuntu repository is outdated and may not support all the features required for this guide. Therefore, start by downloading the latest build of Asterisk 11 directly. Additionally, download the libpri library and the DAHDI module:

                               cd /usr/src/
                               wget http://downloads.asterisk.org/pub/telephony/libpri/libpri-1.4-current.tar.gz
                               wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-11-current.tar.gz
                               wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz

2.  Extract the files:

        tar zxvf dahdi*
        tar zxvf libpri*
        tar zxvf asterisk*

3.  Build DAHDI:

        cd /usr/src/dahdi-linux-complete*
        make
        make install
        make config

4.  Build libpri:

        cd /usr/src/libpri*
        make
        make install

5.  Begin Building Asterisk:

        cd /usr/src/asterisk*
        ./configure
        make menuselect

6. This will display a menu of additional modules to install. Ensure that under Resource Modules, res_xmpp is selected.

7. Next, navigate to Channel Drivers and choose `chan_motif`.

8. Lastly, under Compiler Flags, ensure that `BUILD_NATIVE` is unchecked.

9.  Once you have selected these modules and any other desired build options, click on **Save & Exit**. Then, execute the following commands to complete the installation:

                                make
                                make install
                                make config
                                make samples

10. Start the DAHDI and Asterisk services by executing the following commands:

                                service dahdi start
                                service asterisk start

### Verify the Installation

To verify proper installation of all required packages, launch the Asterisk CLI by entering the following command:

                                 asterisk -rvvv

The output should resemble the excerpt below:

                               Asterisk 11.8.1, Copyright (C) 1999 - 2013 Digium, Inc. and others.
                               Created by Mark Spencer <markster@digium.com>
                               Asterisk comes with ABSOLUTELY NO WARRANTY; type 'core show warranty' for details.
                               This is free software, with components licensed under the GNU General Public
                               License version 2 and other licenses; you are welcome to redistribute it under
                               certain conditions. Type 'core show license' for details.
                               =========================================================================
                                Connected to Asterisk 11.8.1 currently running on localhost (pid = 6963)
                                localhost*CLI>

2.  Use the following commands and review the output to verify that DAHDI and libpri are properly installed:

                                  localhost*CLI> dahdi show version
                                  DAHDI Version: 2.9.0 Echo Canceller:
                                  localhost*CLI> pri show version
                                  libpri version: 1.4.14

## Install FreePBX

FreePBX is a PHP-based application designed to manage and control your Asterisk installation via a web interface.

### Set Up LAMP Stack

Before using FreePBX, you must set up a LAMP stack. Here's a basic step-by-step guide, but for more detailed information, refer to our LAMP documentation.

1.  Start by installing Apache:

        apt-get install apache2

2.  Install MySQL:

        apt-get install mysql-server

MySQL will prompt you to set a password for the root user. Remember the password you choose, as you will need it later.

3. Secure the default installation by running the following command:

        mysql_secure_installation

If you have already set a strong root user password in the previous step, you can skip the option to change the root password. For all other prompts, choose **Y**.

4. Begin by creating the databases to be used by the Asterisk system. Start by launching the MySQL CLI:

        mysql -p

You will be prompted to enter the password you set during the MySQL installation, after which you will access the MySQL command line interface.

Execute the following commands in sequence. Replace the password `(CHANGEME)` with a secure password that you can remember. These commands will create two databases named asterisk and asteriskcdrdb, and grant the asterisk system user permissions to access and modify their contents.

                                      create database asterisk;
                                      create database asteriskcdr;
                                      GRANT ALL PRIVILEGES ON asteriskcdr.* TO asterisk@localhost IDENTIFIED BY 'CHANGEME';
                                      GRANT ALL PRIVILEGES ON asterisk.* TO asterisk@localhost IDENTIFIED BY 'CHANGEME';
                                      flush privileges;
                                      exit

6. Install PHP and related packages

        apt-get install php5 php-pear php5-mysql php5-suhosin php5-cgi php-pear php-db libapache2-mod-php5

7. For this setup, Apache needs to run as the Asterisk user to ensure it can access all necessary files for FreePBX. Verify that your /etc/apache2/envvars file is configured as follows:

                                     {{< file "/etc/apache2/envvars" >}}
                                      export APACHE_RUN_USER=asterisk
                                      export APACHE_RUN_GROUP=asterisk

                                     {{< /file >}}

8. Change ownership of the Apache lock file:

        chown asterisk:asterisk /var/lock/apache2/

9. Lastly, restart Apache to ensure all configurations are loaded correctly:

        service apache2 restart

### Download and Extract FreePBX

1.  Obtain FreePBX and extract it using the following commands:

                   cd /opt
                   wget http://mirror.freepbx.org/freepbx-2.11.0.25.tgz
                   tar -xvf freepbx-2.11.0.25.tgz
                   chown -R asterisk:asterisk freepbx*
                   su - asterisk
                    cd /opt/freepbx/

2.  The FreePBX directory includes SQL files that need to be imported into the database created earlier. Execute the following commands to insert this data:

                      mysql -p asterisk < SQL/newinstall.sql
                      mysql -p asteriskcdr < SQL/cdr_mysql_table.sql
                      exit
 
### Configuration

During the FreePBX configuration process, you will be prompted with several questions that require your attention. While some default values may be suitable for a production system, it's crucial to change passwords to enhance security and prevent unauthorized access.

You must provide the credentials of the MySQL user and database you created earlier to the configuration script. Issue the following command:

                           cd /opt/freepbx/
                          ./install_amp

### Create VirtualHost

Before proceeding with your FreePBX installation, it's important to set up a VirtualHost for the web interface. Additionally, you should secure your installation using SSL and configure an .htaccess file. FreePBX typically installs files to `/var/www/html/` by default, which can remain unchanged. Your VirtualHost configuration might look similar to the following example:

                            {{< file "VirtualHost Entry" apache >}}
<VirtualHost *:80>
ServerAdmin webmaster@example.com
ServerName example.com
ServerAlias www.example.com
DocumentRoot /var/www/html
</VirtualHost>

{{< /file >}}

To apply your Apache configuration updates, restart the server using the following command:

                            service apache2 reload

After restarting Apache, you should be able to access your Utho's IP address or the domain you've pointed at your Utho using a web browser. The initial page will prompt you to create a user and password to administer the system.

## Configure FreePBX

Now we will use the FreePBX web interface to configure your phone server.

### Update the Modules

Access your FreePBX web interface from your web browser.

1.  Click on the `Admin menu` and select `Module Admin`.
2. Choose the Unsupported repository, then click the Check Online button.
3. Click on the Upgrade All link. In the Connectivity section, select Google Voice/Chan Motif. If there are any other modules you wish to 
4. Install from the list, select them at this time.

After selecting your modules, click the Process button, and then click Confirm to finalize the installation.

Note: If module downloading fails, execute the following command in the terminal: `su asterisk -c 'mkdir /var/www/html/admin/modules/_cache`. This should resolve the issue.

5. Click the red `Apply Config` button to activate the modules and updates you have just downloaded.
6. To prevent potential bugs later on, return to your terminal and execute the following commands:

                          cd /etc/asterisk
                          rm ccss.conf confbridge.conf features.conf sip.conf iax.conf logger.conf extensions.conf sip_notify.conf
7. Then repeat Steps 1-4, but this time select to install or reinstall the `Camp on` module.

### Create an Extension

Your PBX system requires at least one defined extension to handle incoming calls. You can repeat this section for additional extensions as needed.

Navigate to the `Applications` menu and select `Extensions`.

Unless you have a custom device to configure, leave the dropdown menu at `Generic SIP Device` and click `Submit`.

Create your SIP extension. The mandatory fields are the Extension number, Display Name, and Secret (password).

## Configure Integration with Google Voice

These steps will guide you to configure your Asterisk server to use Google Voice as a trunk for handling phone calls.

In the FreePBX web interface, navigate to `Connectivity > Google Voice (Motif)`. Enter your Google Voice account details and configure the options as shown below:

Go to `Connectivity > Inbound Routes`. Create an inbound route that directs calls to your extension:

You can now configure your SIP client to connect to your Utho. Use your extension number and password to sign in, and you'll be able to make and receive calls.