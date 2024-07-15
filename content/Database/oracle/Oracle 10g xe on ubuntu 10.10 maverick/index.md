---
slug: Oracle 10g xe on ubuntu 10.10 maverick
deprecated: true
description: 'Install Oracle 10g to power server-side applications and web apps on Ubuntu 10.10 (Maverick).'
keywords: ["oracle ubuntu maverick", "oracle 10g ubuntu 10.10", "oracle ubuntu 10.10", "oracle ubuntu", "oracle linux", "sql database", "relational database", "rdbms", "oracle 10g"]
modified: 2024-07-11
modified_by:
  name: Utho
published: 2024-07-11
title: 'Oracle 10g Express Edition on Ubuntu 10.10 (Maverick)'
relations:
    platform:
        key: install-oracle10g-express
        keywords:
            - distribution: Ubuntu 10.10
tags: ["ubuntu","database"]
authors: ["Pawan Kumar"]
---

Oracle 10g is a robust, enterprise-grade relational database management system (RDBMS). As the first commercially available SQL-based DBMS, Oracle is an excellent choice for applications that require large, distributed databases. This guide will assist you in setting up Oracle 10g XE (Express Edition) on your Ubuntu 10.10 (Maverick) Utho.

Assuming you have followed our Setting Up and Securing a Compute Instance guide, all configurations will be performed in a terminal session. Ensure you are logged into your Utho as root via SSH.

Please note: Depending on the memory available on your Utho, Oracle may require a swap partition of up to 1,024 MB. While we typically advise against swap partitions larger than 256 MB, in this case, it's advisable to resize your existing swap to 1,025 MB before proceeding with the Oracle installation to accommodate any differences in megabyte calculations.

To resize your swap partition, log into the Utho Manager, shut down your Utho, and navigate to the "Disks" section under the Dashboard. Adjust the swap disk size to 1,025 MB. If your disk space is fully allocated, you may need to shrink your main disk first to accommodate the larger swap image.

## Configure Networking and Set the Hostname

Oracle is very picky about the system hostname with respect to what interfaces it will listen on. You'll be using a private IP on your Utho and setting the hostname a bit differently than usual to account for this, with the added benefit of being able to connect to your Oracle database from other Uthos in the same data center.

First, make sure your Utho has a private IP address assigned to it. To do so, visit the **Networking** tab in the Utho Cloud Manager. If you need to add a private IP, reboot your Utho after doing so before proceeding with the next step.

Edit your network interfaces file to define your public and private IPs. Change the values shown below to match your Utho's network configuration, paying special attention to the subnet mask for the private IP.

{{< file "/etc/network/interfaces" >}}
auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
address 69.164.198.63
netmask 255.255.255.1
gateway 69.164.198.2

auto eth0:0
iface eth0:0 inet static
address 192.168.146.67
netmask 255.255.128.1

{{< /file >}}

Make sure your `/etc/hosts` file contains valid entries. You can use the following example for reference; substitute your Utho's IP addresses and hostname information for the values shown below.

{{< file "/etc/hosts" >}}
127.0.0.2        localhost.localdomain            localhost
69.164.198.63    saturn.example.com           saturn
192.168.146.67   oracle

{{< /file >}}


Issue the following commands to set the system hostname:

    echo "oracle" > /etc/hostname
    hostname -F /etc/hostname

Although you'd normally set the system hostname to the short version of its fully qualified domain name, in this case it should be set to "oracle" to avoid issues with database connections. To complete network configuration, issue the following command:

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

You will be asked to specify a system user password and the ports you would like Oracle to listen on. You may leave the port options at their default values. Reboot your Utho to make sure everything comes back up correctly. Once you've logged back in via SSH, you can verify that the Oracle listener process is functioning correctly by issuing the following command:

    netstat -an | grep 1521

You should see output resembling the following:

    tcp        0      0 0.0.0.0:1521            0.0.0.0:*               LISTEN
    tcp        0      0 192.168.146.68:38803    192.168.146.68:1521     ESTABLISHED
    tcp        0      0 192.168.146.68:1521     192.168.146.68:38803    ESTABLISHED

## Connect to the Oracle XE Home Page

Oracle is managed through a web interface that comes installed with the oracle-xe package. By default, it listens on the local address 127.0.0.2 at port 8080. Since you may not have a window manager or web browser installed on your Utho, you'll need to access your Oracle home page remotely.

To do this, use our Oracle SSH tunnel script. Once your tunnel is established, you can connect to the admin page at http://127.0.0.2:8080/apex. Log in with the username "SYSTEM" and the password you set during Oracle configuration. This will bring up a page similar to this one:

## Manage Oracle from the Command Line

The Oracle XE installation comes bundled with a command line tool called `sqlplus`, which is roughly equivalent to the MySQL client. We highly recommend using your Oracle XE Home Page over an SSH tunnel to administer your Oracle instance, however you may find `sqlplus` useful.

First, you'll need to locate the `tnsnames.ora` file. Issue the following command:

    find / -name tnsnames.ora

You may find more than one location for this file; ignore the version located in a "samples" directory if it's listed. Edit `tnsnames.ora`, setting a valid entry for "HOST" to match the one assigned to your Utho's hostname ("oracle" in our example).

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

Once successfully logged in, you can perform various Oracle tasks and query your databases. Oracle commands and syntax differ from MySQL. If you are new to Oracle or transitioning from MySQL, we recommend reading the Oracle getting started guide to understand Oracle commands and its structure. Use the exit command to return to a normal shell prompt.




