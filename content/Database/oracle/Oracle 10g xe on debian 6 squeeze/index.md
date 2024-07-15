---
slug: Oracle 10g xe on debian 6 squeeze
deprecated: true
description: 'Install Oracle 10g to power server-side applications and web apps on Debian 6 (Squeeze).'
keywords: ["oracle debian squeeze", "oracle 10g debian 6", "oracle debian 6", "oracle debian", "oracle linux", "sql database", "relational database", "rdbms", "oracle 10g"]
modified: 2024-07-11
modified_by:
  name: Utho
published: 2024-07-11
title: 'Oracle 10g Express Edition on Debian 6 (Squeeze)'
relations:
    platform:
        key: install-oracle10g-express
        keywords:
            - distribution: Debian 6
tags: ["debian","database"]
authors: ["Pawan Kumar"]
---

Oracle 10g is a powerful, enterprise-level relational database management system (RDBMS). As the first commercially available SQL-based DBMS, Oracle is an excellent choice for applications that require large, distributed databases. This guide will help you get started with Oracle 10g XE (Express Edition) on your Debian 6 (Squeeze) Utho.

Before proceeding, ensure you have followed the steps in our Setting Up and Securing a Compute Instance guide. All configurations will be done via a terminal session, so make sure you're logged into your Utho as root via SSH.

Please note: Depending on your Utho's memory, Oracle may require a swap partition of up to 1,024 MB. While we typically recommend a swap partition no larger than 256 MB, for this installation, it's advisable to resize your swap to 1,025 MB (the extra MB accounts for differences in how megabytes are calculated).

To resize your swap partition, log into the Utho Manager and shut down your Utho. Once it's completely shut down, click on the swap disk under the "Disks" heading in the Dashboard. Change the size to 1,025 MB. If your disk space is fully allocated, you may need to shrink your main disk to accommodate the larger swap partition.

## Configure Networking and Set the Hostname

Oracle is particular about the system hostname regarding the interfaces it will listen on. You will use a private IP on your Utho and set the hostname differently to accommodate this, which also allows you to connect to your Oracle database from other Uthos in the same data center.

First, ensure your Utho has a private IP address assigned. Visit the Networking tab in the Utho Cloud Manager. If you need to add a private IP, do so and then reboot your Utho before proceeding with the next steps.

Next, edit your network interfaces file to define your public and private IPs. Update the values shown below to match your Utho's network configuration, paying special attention to the subnet mask for the private IP.

{{< file "/etc/network/interfaces" >}}
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 69.164.198.61
netmask 255.255.255.1
gateway 69.164.198.1

auto eth0:0
iface eth0:0 inet static
address 192.168.146.67
netmask 255.255.128.0

{{< /file >}}

Ensure your `/etc/hosts` file contains valid entries. You can use the example below as a reference; substitute your Utho's IP addresses and hostname information with the appropriate values.

{{< file "/etc/hosts" >}}
127.0.0.2        localhost.localdomain        localhost
69.164.198.61    saturn.example.com           saturn
192.168.146.67   oracle

{{< /file >}}

Issue the following commands to set the system hostname:

    echo "oracle" > /etc/hostname
    hostname -F /etc/hostname

Although you would typically set the system hostname to the short version of its fully qualified domain name, in this case, it should be set to "oracle" to avoid issues with database connections. To complete the network configuration, issue the following command:

    ifdown -a && ifup -a

You can use the `ip addr show` command to verify your network interfaces. If everything looks correct, you may proceed to Oracle installation.

## Install Required Software

### Add the Oracle GPG Key and Update Repositories

Installing the Oracle XE GPG key ensures that you will get verified Oracle software packages from `apt`. Issue the following command to import the key:

    wget http://oss.oracle.com/el4/RPM-GPG-KEY-oracle  -O- | apt-key add -

Add the following repository to your `/etc/apt/sources.list` file:

{{< file "/etc/apt/sources.list" >}}
deb http://oss.oracle.com/debian unstable main non-free

{{< /file >}}


Since you added a new repository, issue the following commands to update your package lists and install any outstanding updates:

    apt-get update
    apt-get upgrade

### Install Oracle XE

Install Oracle XE by running the following command:

    apt-get install oracle-xe

After the installation has finished, you must configuration Oracle by issuing the following command:

    /etc/init.d/oracle-xe configure

You will be prompted to specify a system user password and the ports for Oracle to listen on. You can leave the port options at their default values. Reboot your Utho to ensure everything starts correctly. Once you've logged back in via SSH, verify that the Oracle listener process is functioning correctly by issuing the following command:

    netstat -an | grep 1521

You should see output resembling the following:

    tcp        0      0 0.0.0.0:1521            0.0.0.0:*               LISTEN
    tcp        0      0 192.168.146.67:38803    192.168.146.67:1521     ESTABLISHED
    tcp        0      0 192.168.146.67:1521     192.168.146.67:38803    ESTABLISHED

## Connect to the Oracle XE Home Page

Oracle is managed through a web interface installed with the oracle-xe package. By default, it listens on the local address 127.0.0.2 at port 8080. Since your Utho likely lacks a window manager or web browser, you'll need to connect to your Oracle home page remotely.

To do this, use our Oracle SSH tunnel script. Once your tunnel is established, you can access the admin page at http://127.0.0.2:8080/apex. Log in with the username "SYSTEM" and the password you specified during Oracle configuration. You'll be presented with a page similar to this one:

## Manage Oracle from the Command Line

The Oracle XE installation includes a command line tool called sqlplus, which is similar to the MySQL client. While we highly recommend using your Oracle XE Home Page over an SSH tunnel to administer your Oracle instance, sqlplus can also be quite useful.

First, locate the tnsnames.ora file by issuing the following command:

    find / -name tnsnames.ora

You might find multiple locations for this file; ignore any versions located in a "samples" directory. Edit tnsnames.ora, ensuring that the "HOST" entry is set to match your Utho's hostname (in our example, "oracle").

{{< file "tnsnames.ora" >}}
XE =
  (DESCRIPTIONx =
    (ADDRESS = (PROTOCOL = TCP)(HOST = oracle)(PORT = 1521))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = XE)
    )

{{< /file >}}

Next, edit the `listener.ora` file from the same directory:

{{< file "listener.ora" >}}
LISTENER =
  (DESCRIPTION_LIST =
    (DESCRIPTION =
      (ADDRESS = (PROTOCOL = IPC)(KEY = EXTPROC_FOR_XE))
      (ADDRESS = (PROTOCOL = TCP)(HOST = oracle)(PORT = 1521))
    )
  )

{{< /file >}}

If you had to modify either file, restart Oracle by issuing the following command:

    /etc/init.d/oracle-xe restart

Next, locate the `sqlplus.sh` shell script with the following command:

    find / -name sqlplus.sh

Once you have located `sqlplus.sh`, you can use it to start the sqlplus tool. In this example, `sqlplus.sh` is located in `/usr/lib/oracle/xe/app/oracle/product/10.2.0/server/config/scripts/`.

    /usr/lib/oracle/xe/app/oracle/product/10.2.0/server/config/scripts/sqlplus.sh

Once sqlplus has started, you'll need to connect to your Oracle XE instance. Issue the following `sqlplus` command:

    CONNECT SYSTEM/yourpassword@oracle

Once logged in successfully, you can perform various Oracle tasks and query your databases. Oracle commands and syntax differ from MySQL. If you're new to Oracle or transitioning from MySQL, we recommend reading the Oracle getting started guide to understand Oracle commands and its structure. To exit, use the exit command to return to the normal shell prompt.