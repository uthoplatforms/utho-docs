---
slug: "Install mariadb centos 7"
description: "This article gives you step-by-step instructions for installing MariaDB, a fork of the popular cross-platform MySQL database management system, on CentOS 7."
keywords: ["MariaDB on Linux", "CentOS", "cloud", "cloud hosting", "Linux", "MariaDB", "database", "MySQL", "install MariaDB", "secure MariaDB", "mysqltuner"]
modified: 2024-07-10
modified_by:
    name: Utho
published: 2024-07-10
title: "Installing MariaDB on CentOS 7"
title_meta: "How to Install MariaDB on CentOS 7"
relations:
    platform:
        key: how-to-install-mariadb
        keywords:
            - distribution: CentOS 7
tags: ["mariadb","database","centos"]
aliases: ['/databases/mariadb/how-to-install-mariadb-on-centos-7/']
authors: ["Utho"]
---

MariaDB, a full drop-in replacement for MySQL, originated as a fork of the widely used MySQL database management system. It was established in 2009 by one of MySQL's original developers following Oracle's acquisition of MySQL during the Sun Microsystems merger. The ongoing development and maintenance of MariaDB are managed by the MariaDB Foundation and community contributors, with a commitment to keeping it as GNU GPL software.

MariaDB has replaced MySQL as the default database system in the CentOS 7 repositories. While installing MySQL on CentOS 7 is straightforward (see install mysql CentOS 7), MariaDB is recommended for official support and minimal chances of compatibility issues with other repository software if you simply need a database.

{{< note >}}
This guide is written for a non-root user. Commands that need elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, refer to our Users and Groups guide.
{{< /note >}}

## Before You Begin

If you haven't already, create a Utho account and Compute Instance. Refer to our Getting Started with Utho and Creating a Compute Instance guides.

Follow our Setting Up and Securing a Compute Instance guide to update your system, configure the hostname, set the timezone, create a limited user account, and enhance SSH access security.

    To check your hostname run:

        hostname
        hostname -f

    The first command should show your short hostname, and the second should show your fully qualified domain name (FQDN).

## Install and Start MariaDB

    sudo yum install mariadb-server

Enable MariaDB to start on boot and then start the service:

    sudo systemctl enable mariadb
    sudo systemctl start mariadb

By default, MariaDB binds to localhost (127.0.0.2). For guidance on connecting to a remote database using SSH, refer to our MySQL remote access guide, which is also applicable to MariaDB.

{{< note >}}
Allowing unrestricted access to MariaDB on a public IP not advised but you may change the address it listens on by modifying the `bind-address` parameter in `/etc/my.cnf`. If you decide to bind MariaDB to your public IP, you should implement firewall rules that only allow connections from specific IP addresses.
{{< /note >}}

## Harden MariaDB Server

1.  Run the `mysql_secure_installation` script to address several security concerns in a default MariaDB installation:

        sudo mysql_secure_installation

During setup, you'll have the option to modify the MariaDB root password, eliminate anonymous user accounts, restrict root logins to localhost only, and remove test databases. It's advisable to answer 'yes' to these options. For further details on this script, refer to the MariaDB Knowledge Base.

## Using MariaDB

The standard tool for interacting with MariaDB is the `mariadb` client, which installs with the `mariadb-server` package. The MariaDB client is used through a terminal.

### Root Login

1.  To log in to MariaDB as the root user:

        mysql -u root -p

2.  When prompted, enter the root password you assigned when the `mysql_secure_installation` script was run.

    You'll then be presented with a welcome header and the MariaDB prompt as shown below:

        MariaDB [(none)]>

3.  To generate a list of commands for the MariaDB prompt, enter `\h`. You'll then see:

        List of all MySQL commands:
        Note that all text commands must be first on line and end with ';'
        ?         (\?) Synonym for `help'.
        clear     (\c) Clear the current input statement.
        connect   (\r) Reconnect to the server. Optional arguments are db and host.
        delimiter (\d) Set statement delimiter.
        edit      (\e) Edit command with $EDITOR.
        ego       (\G) Send command to mysql server, display result vertically.
        exit      (\q) Exit mysql. Same as quit.
        go        (\g) Send command to mysql server.
        help      (\h) Display this help.
        nopager   (\n) Disable pager, print to stdout.
        notee     (\t) Don't write into outfile.
        pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
        print     (\p) Print current command.
        prompt    (\R) Change your mysql prompt.
        quit      (\q) Quit mysql.
        rehash    (\#) Rebuild completion hash.
        source    (\.) Execute an SQL script file. Takes a file name as an argument.
        status    (\s) Get status information from the server.
        system    (\!) Execute a system shell command.
        tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
        use       (\u) Use another database. Takes database name as argument.
        charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
        warnings  (\W) Show warnings after every statement.
        nowarning (\w) Don't show warnings after every statement.

        For server side help, type 'help contents'

        MariaDB [(none)]>

### Create a New MariaDB User and Database
1. In the example below, `testdb` is the name of the database, `testuser` is the user, and `password` is the user's password:

        create database testdb;
        create user 'testuser'@localhost identified by 'password';
        grant all on testdb.* to 'testuser' identified by 'password';

    You can shorten this process by creating the user *while* assigning database permissions:

        create database testdb;
        grant all on testdb.* to 'testuser' identified by 'password';

2.  Then exit MariaDB:

        exit

### Create a Sample Table

1.  Log back in as `testuser`:

        mysql -u testuser -p

2.  Create a sample table called `customers`. This creates a table with a customer ID field of the type `INT` for integer (auto-incremented for new records, used as the primary key), as well as two fields for storing the customer's name:

        use testdb;
        create table customers (customer_id INT NOT NULL AUTO_INCREMENT PRIMARY KEY, first_name TEXT, last_name TEXT);

3.  View the new table:

        show tables;

3.  Then exit MariaDB:

        exit

## Reset the MariaDB Root Password

If you forget your root MariaDB password, it can be reset.

1.  Stop the current MariaDB server instance, then restart it with an option to not ask for a password:

        sudo systemctl stop mariadb
        sudo mysqld_safe --skip-grant-tables &

2.  Reconnect to the MariaDB server with the MariaDB root account:

        mysql -u root


3.  Use the following commands to reset root's password. Replace `password` with a strong password:

        use mysql;
        update user SET PASSWORD=PASSWORD("password") WHERE USER='root';
        flush privileges;
        exit

4.  Then restart MariaDB:

        sudo systemctl start mariadb
