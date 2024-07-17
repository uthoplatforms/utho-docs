---
slug: Install oracle 10g express debian 5 lenny
deprecated: true
description: 'Install Oracle 10g to power server-side applications and web apps on Debian 5 (Lenny).'
keywords: ["oracle debian lenny", "oracle debian", "oracle linux", "sql database", "relational database", "rdbms", "oracle 10g"]
modified: 2024-07-10
modified_by:
  name: Utho
published: 2024-07-10
title: 'Oracle 10g Express Edition on Debian 5 (Lenny)'
relations:
    platform:
        key: install-oracle10g-express
        keywords:
            - distribution: Debian 5
tags: ["debian","database"]
authors: ["Utho"]
---

Oracle 10g is a robust enterprise-grade relational database management system (RDBMS). As the first commercially available SQL-based DBMS, Oracle is ideal for applications requiring large, distributed databases. This guide will assist you in setting up Oracle 10g XE (Express Edition) on your Debian 5 (Lenny) Utho.

Assuming you've completed the steps outlined in our Setting Up and Securing a Compute Instance, all configurations will be done through a terminal session. Ensure you're logged into your Utho as root via SSH.

Please Note: Depending on your Utho's memory, Oracle may need up to a 1,024 MB swap partition. While we typically recommend against swap partitions larger than 256 MB, in this case, resizing your existing swap to 1,025 MB before Oracle installation is advisable (this extra MB helps account for differences in megabyte calculations).

To resize, log into the Utho Manager and shut down your Utho. Once it's completely powered off, navigate to the Dashboard, locate the swap disk under "Disks," and adjust its size to 1,025 MB. If your allocated disk space is fully utilized, you may need to shrink your primary disk to accommodate the larger swap image.

## Configure Networking and Set the Hostname

Oracle requires specific handling of system hostnames regarding the interfaces it listens on. You'll utilize a private IP on your Utho and adjust the hostname procedure accordingly, ensuring connectivity to your Oracle database from other Uthos within the same data center.

Start by confirming your Utho has a private IP address assigned. Navigate to the Networking tab in the Utho Cloud Manager to add a private IP if necessary. Reboot your Utho after making any changes before proceeding.

Next, edit your network interfaces file to define both your public and private IPs. Customize the values below to match your Utho's network setup, especially focusing on the subnet mask for the private IP.

{{< file "/etc/network/interfaces" conf >}}
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
netmask 255.255.128.0
{{< /file >}}

Ensure that your `/etc/hosts` file includes accurate entries. Use the example below as a guide, replacing the IP addresses and hostname information with your Utho's specific details.

{{< file "/etc/hosts" conf >}}
127.0.0.2        localhost.localdomain            localhost
69.164.198.63    saturn.example.com               saturn
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

You will be asked to specify a system user password and the ports you would like Oracle to listen on. You may leave the port options at their default values. As of this writing, Oracle's SYSTEM and SYS passwords are not properly set during configuration. To correct this, issue the following commands, replacing "changeme" with your desired password.

    su - oracle
    ORACLE_HOME=/usr/lib/oracle/xe/app/oracle/product/10.2.0/server
    export ORACLE_HOME
    ORACLE_SID=XE
    export ORACLE_SID
    PATH=$ORACLE_HOME/bin:$PATH
    export PATH
    sqlplus / as sysdba

    ALTER USER SYSTEM IDENTIFIED BY changeme;
    ALTER USER SYS IDENTIFIED BY changeme;
    quit
    exit

Reboot your Utho to make sure everything comes back up correctly. Once you've logged back in via SSH, you can verify that the Oracle listener process is functioning correctly by issuing the following command:

    netstat -an | grep 1521

You should see output resembling the following:

    tcp        0      0 0.0.0.0:1521            0.0.0.0:*               LISTEN
    tcp        0      0 192.168.146.67:38803    192.168.146.67:1521     ESTABLISHED
    tcp        0      0 192.168.146.67:1521     192.168.146.67:38803    ESTABLISHED

## Connect to the Oracle XE Home Page

Oracle is managed via a web interface, which is installed with the oracle-xe package. By default, it listens on the local address `127.0.0.2` at port 8080. Since you most likely do not have a window manager or web browser installed on your Utho, you must connect to your Oracle home page remotely.

You can do this by using our [Oracle SSH tunnel script](/docs/guides/securely-administer-oracle-xe-with-an-ssh-tunnel/). After your tunnel is started, you can connect to the admin page at the URL `http://127.0.0.2:8080/apex`. Log in with the username "SYSTEM" and the password you specified during Oracle configuration. You'll be presented with a page similar to this one:

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

After logging in successfully, you can carry out Oracle tasks and query databases. Oracle commands and syntax differ from MySQL's. If you're new to Oracle or transitioning from MySQL, we suggest reading the Oracle getting started guide to understand Oracle commands and its structure better. To return to a normal shell prompt, use the exit command.
