---
slug: Installing Asterisk on CentOS 7
description: 'This guide explains the installation of Asterisk, an open-source private branch exchange (PBX) application used to run VoIP services, on CentOS 7.'
keywords: ["asterisk 13", "centos 7", "centos", "open source", "private branch exchange", "pbx", "asterisk pbx", "sip", "session initiation protocol", "sip protocol", "IP PBX systems", "VoIP gateways"]
tags: ["centos"]
published: 2024-06-19
modified: 2024-06-19
modified_by:
    name: Utho
title: 'How to Install Asterisk on CentOS 7'
dedicated_cpu_link: true
relations:
    platform:
        key: asterisk-freepbx-telephone
        keywords:
            - distribution: CentOS 7
authors: ["Pawan Kumar"]
---

## What is Asterisk?

Asterisk is an open-source Private Branch Exchange (PBX) server that utilizes Session Initiation Protocol (SIP) to handle and manage telephone calls. Key features include customer service queues, music on hold, conference calling, and call recording.

This guide outlines the steps to set up a new CentOS 7 Utho as a dedicated Asterisk server for your home or office.

Note: This guide is designed for non-root users. Commands requiring elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, please refer to our Users and Groups guide.

## Before You Begin

Deploy a CentOS 7 Utho in your nearest data center. A 2GB Utho should suffice to manage 10-20 concurrent calls using a non-compressed codec, depending on the processing demands of each channel.

Prior to proceeding, ensure you have followed the Getting Started and Setting Up and Securing a Compute Instance guides to prepare your Utho. Do not execute the firewall setup steps.

1.  Update your system:

                              sudo yum update

1.  Disable SELinux and restart your Utho. If you have Lassie enabled, your Utho will be back online in a few minutes.

                               sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config

                               sudo systemctl reboot

## Configure firewalld

By default, CentOS 7 activates firewalld's `public` zone for the primary interface (`eth0`), along with enabling SSH and DHCPv6 services. To check your current firewalld zone configuration:

                                sudo firewall-cmd --get-active-zones
                                sudo firewall-cmd --permanent --list-services

That should return:

            {{< output >}}

            [user@asterisk ~]$ sudo firewall-cmd --get-active-zones
            public
            interfaces: eth0
           
            {{< /output >}}

            And:

            {{< output >}}

             [user@asterisk ~]$ sudo firewall-cmd --permanent --list-services
             ssh dhcpv6-client
            
            {{< /output >}}

1.  Add the SIP services.

Note: All of the following firewalld rules include the `--permanent` flag to ensure their persistence after a system reboot.

                   sudo firewall-cmd --zone=public --permanent --add-service={sip,sips}

Depending on your requirements, you might need to add additional related ports:

    - MGCP - If you utilize the Media Gateway Control Protocol in your setup.

                        sudo firewall-cmd --zone=public --permanent --add-port=2727/udp

    - RTP - Handles the media stream; configuration can be adjusted in /etc/asterisk/rtp.conf.

                        sudo firewall-cmd --zone=public --permanent --add-port=10000-20000/udp

    - If you intend to use FreePBX to manage Asterisk, include the following rule:

                        sudo firewall-cmd --zone=public --permanent --add-service={http,https}

    - IAX - If you require IAX, add the following rule. IAX, or "Inter-Asterisk Exchange," facilitates communication between multiple Asterisk servers. While some VOIP trunking providers use IAX, SIP is more commonly used. Unless mandated by your VOIP provider or operating multiple Asterisk servers, IAX or IAX2 may not be necessary.

                         sudo firewall-cmd --zone=public --permanent --add-port=4569/udp

Verify your updated configuration by running:

        sudo firewall-cmd --permanent --list-services
        sudo firewall-cmd --permanent --list-ports

You should observe the newly added services and ports alongside the default SSH and DHCPv6 services:

         {{< output >}}

         [user@asterisk ~]$ sudo firewall-cmd --list-ports
         2727/udp 10000-20000/udp 4569/udp
        
         {{< /output >}}

         {{< output >}}

         [user@asterisk ~]$ sudo firewall-cmd --permanent --list-services
         ssh dhcpv6-client sip sips http https
         
         {{< /output >}}

## Install PJPROJECT

PJPROJECT is Asterisk's SIP channel driver, designed to enhance call clarity and performance compared to older drivers.

1.  Install build dependencies:

        sudo yum install epel-release gcc-c++ ncurses-devel libxml2-devel wget openssl-devel newt-devel kernel-devel-`uname -r` sqlite-devel libuuid-devel gtk2-devel jansson-devel binutils-devel bzip2 patch libedit libedit-devel

1.  As a `non-root user`, establish a working directory for the build:

        mkdir ~/build-asterisk

1.  Change to that directory:

        cd ~/build-asterisk

Utilize `wget` to fetch the PJSIP driver source code:

        wget https://www.pjsip.org/release/2.8/pjproject-2.8.tar.bz2

1.  Extract it:

        tar -jxvf pjproject-2.8.tar.bz2

Navigate to the newly created directory:

        cd pjproject-2.8

1.  Specify the compiling flags and options:

        ./configure CFLAGS="-DNDEBUG -DPJ_HAS_IPV6=1" --prefix=/usr --libdir=/usr/lib64 --enable-shared --disable-video --disable-sound --disable-opencore-amr

1. Verify that all dependencies are installed:

        make dep

1.  If `make dep` completes successfully, proceed to build the plugin. This process should only take a few minutes.

                       make

1.  Install the packages:

                       sudo make install
                       sudo ldconfig

1.  Confirm that the libraries have been installed correctly:

                       sudo ldconfig -p | grep pj

You should see:

           {{< output >}}
    
    libpjsua2.so.2 (libc6,x86-64) => /lib64/libpjsua2.so.2
    libpjsua2.so (libc6,x86-64) => /lib64/libpjsua2.so
    libpjsua.so.2 (libc6,x86-64) => /lib64/libpjsua.so.2
    libpjsua.so (libc6,x86-64) => /lib64/libpjsua.so
    libpjsip.so.2 (libc6,x86-64) => /lib64/libpjsip.so.2
    libpjsip.so (libc6,x86-64) => /lib64/libpjsip.so
    libpjsip-ua.so.2 (libc6,x86-64) => /lib64/libpjsip-ua.so.2
    libpjsip-ua.so (libc6,x86-64) => /lib64/libpjsip-ua.so
    libpjsip-simple.so.2 (libc6,x86-64) => /lib64/libpjsip-simple.so.2
    libpjsip-simple.so (libc6,x86-64) => /lib64/libpjsip-simple.so
    libpjnath.so.2 (libc6,x86-64) => /lib64/libpjnath.so.2
    libpjnath.so (libc6,x86-64) => /lib64/libpjnath.so
    libpjmedia.so.2 (libc6,x86-64) => /lib64/libpjmedia.so.2
    libpjmedia.so (libc6,x86-64) => /lib64/libpjmedia.so
    libpjmedia-videodev.so.2 (libc6,x86-64) => /lib64/libpjmedia-videodev.so.2
    libpjmedia-videodev.so (libc6,x86-64) => /lib64/libpjmedia-videodev.so
    libpjmedia-codec.so.2 (libc6,x86-64) => /lib64/libpjmedia-codec.so.2
    libpjmedia-codec.so (libc6,x86-64) => /lib64/libpjmedia-codec.so
    libpjmedia-audiodev.so.2 (libc6,x86-64) => /lib64/libpjmedia-audiodev.so.2
    libpjmedia-audiodev.so (libc6,x86-64) => /lib64/libpjmedia-audiodev.so
    libpjlib-util.so.2 (libc6,x86-64) => /lib64/libpjlib-util.so.2
    libpjlib-util.so (libc6,x86-64) => /lib64/libpjlib-util.so
    libpj.so.2 (libc6,x86-64) => /lib64/libpj.so.2
    libpj.so (libc6,x86-64) => /lib64/libpj.so

{{< /output >}}

<!--
## Install DAHDI (Optional)

DAHDI, or *Digium/Asterisk Hardware Device Interface*, is the kernel module that controls telephone interface cards. This type of card is usually used when adding Asterisk to an existing call center that uses older technology.

Since it's not possible to add physical cards to a virtual machine you probably won't need the DAHDI driver installed. There is one exception: if you plan to host conference calls on your Asterisk box where more than one person can join a conference room. DAHDI provides the required timing source for this feature to work.

#### Build DAHDI

1.  Be sure you're in your build directory. You don't want to build DAHDI from the `pjproject-*` directory you changed into earlier.

        cd ~/build-asterisk

1.  Download the latest version of DAHDI (version 3.0.0 at the time of this writing):

        wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz

1.  Untar the file:

        tar -zxvf dahdi-linux-complete-current.tar.gz

1.  Change to the new directory:

        cd dahdi-linux-complete-3.0.0+3.0.0/

    If the directory cannot be found, run the `ls` command and take note of the folder name and `cd` into that directory instead.

1.  Build DAHDI:

        make

1.  Install DAHDI:

        sudo make install
        sudo make config
-->

## Install Asterisk

1.  Return to your build directory:

        cd ~/build-asterisk

1.  Download the latest version of Asterisk 16:

                       wget http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-16-current.tar.gz

1.  Untar the file:

                        tar -zxvf asterisk-16-current.tar.gz

1.  Navigate to the new Asterisk directory, replacing 16.1.2 with the latest version if needed:

                        cd asterisk-16.1.2

### Enable MP3 Support

1.  To enable the use of MP3 files for Music on Hold, install Subversion:

                        sudo yum install svn

1.  Execute the configuration script:

                        contrib/scripts/get_mp3_source.sh


### Configure and Build Asterisk

Navigate to your Asterisk build directory and execute the `configure` script to prepare the source code for compilation:

                    ./configure --libdir=/usr/lib64 --with-jansson-bundled

Begin the build process. After a brief moment, a menu will appear on screen, allowing you to configure the features you wish to include. This process generates generic binaries rather than architecture-specific optimized binaries.

                     make menuselect --disable BUILD_NATIVE menuselect.makeopts

If you intend to utilize the MP3 format for Music on Hold, navigate to Add-Ons in the menu. Use the right arrow to move to the list on the right, locate format_mp3, and press Enter to select it.

Select additional core sound packages and Music on Hold packages from the left-hand menu. Enable the `.wav` format for your preferred language (e.g., select the `EN` package for English).

1.  Press **F12** to save and exit.

1. Compile Asterisk. Once finished, you should see a message confirming that Asterisk has been successfully built.

                          sudo make

1.  Install Asterisk:

                          sudo make install

1.  Install sample configuration files by executing the following command:

                          sudo make samples

1. Set up Asterisk to start automatically on boot:

                           sudo make config

### Test Connection

You now have a functional Asterisk phone server. Start Asterisk to ensure it is running correctly.

1.  Start Asterisk:

                          sudo systemctl start asterisk

To ensure that the Asterisk service starts automatically after a reboot, enable the service:

                          sudo systemctl enable asterisk

1.  Connect to Asterisk:

                          sudo asterisk -rvv

You should see output similar to the following:

                           {{< output >}}

Asterisk 16.0.0, Copyright (C) 1999 - 2018, Digium, Inc. and others.
Created by Mark Spencer <markster@digium.com>
Asterisk comes with ABSOLUTELY NO WARRANTY; type 'core show warranty' for details.
This is free software, with components licensed under the GNU General Public
License version 2 and other licenses; you are welcome to redistribute it under
certain conditions. Type 'core show license' for details.
=========================================================================
Connected to Asterisk 16.0.0 currently running on li73-122 (pid = 980)

                           {{< /output >}}

To view a list of available commands:

                          core show help

1.  To disconnect type:

                          exit

After disconnecting, Asterisk continues to run in the background.

## Next Steps

Now that you have Asterisk running on your Utho, it's time to connect phones, add extensions, and configure various options available with Asterisk. For detailed instructions, refer to the Asterisk Project's guide on Configuring Asterisk.

Note: When deploying a phone system on a remote server like Utho, it's advisable to secure signaling data with TLS and audio calls with SRTP to prevent eavesdropping. Once your dial-plan is operational, follow the Secure Calling Guide to encrypt your communications effectively.