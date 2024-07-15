---
slug: "Setup mariadb clusters galera debian-ubuntu"
description: 'A guide to configuring MariaDB database replication with Galera on Debian and Ubuntu distributions.'
keywords: ["mariadb", "mysql", "galera", "high availability", "HA", "cluster", "debian", "ubuntu"]
modified: 2024-07-10
modified_by:
  name: Utho
published: 2024-07-10
title: Set Up MariaDB Clusters with Galera Debian and Ubuntu
tags: ["ubuntu","debian","mariadb","database"]
authors: ["Pawan Kumar"]
tags: ["ecommerce"]
---

MariaDB replication using Galera provides redundancy for a site's database by creating a clustered environment. In this setup, multiple servers function as a database cluster, which is beneficial for ensuring high availability in website configurations. This guide demonstrates configuring database replication on three distinct Uthos, each equipped with private IPv4 addresses, running Debian and Ubuntu.

{{< note >}}
Communication between nodes is unencrypted. This guide assumes that each of your Uthos is configured with a Private IP Address and situated within the same data center.
{{< /note >}}

Additionally:

 - Since Galera uses synchronous replication, performance is as fast as the slowest node.
 - MariaDB 10.0 has an end of life on March 2019. Installation of MariaDB 10.1 and above is recommended.

## Install Required Packages

1.  To install the required packages, first add the keys for the Galera repository. Note that the key may change depending on distribution and MariaDB version. This guide will use MariaDB 10.1 on Ubuntu 16.04 as an example. Failure to use the correct key and repository list combination has been known to install MariaDB 10.0 by default.

        sudo apt install software-properties-common
        sudo apt-key adv --recv-keys --keyserver hkp://keyserver.ubuntu.com:80 0xF1656F24C74CD1D8

    {{< note respectIndent=false >}}
On Debian 9 and later, run `sudo apt install dirmngr` before importing the key.
{{< /note >}}

2.  Add the repository substituting the version and distribution for your environment:

        sudo add-apt-repository 'deb [arch=amd64,i386,ppc64el] http://mirror.nodesdirect.com/mariadb/repo/10.1/ubuntu xenial main'

    | Distribution |         Key        | Version | Repository List
    |--------------|--------------------|---------|----------------
    | Debian 11    | 0xF1656F24C74CD1D8 |   10.5  | deb [arch=amd64,i386,ppc64el] http://mirror.nodesdirect.com/mariadb/repo/10.5/debian bullseye main
    | Debian 10    | 0xF1656F24C74CD1D8 |   10.4  | deb [arch=amd64,i386,ppc64el] http://mirror.nodesdirect.com/mariadb/repo/10.4/debian buster main
    | Debian 9     | 0xF1656F24C74CD1D8 |   10.1  | deb [arch=amd64,i386,ppc64el] http://mirror.nodesdirect.com/mariadb/repo/10.1/debian stretch main
    | Debian 8     | 0xcbcb082a1bb943db |   10.0  | deb [arch=amd64,i386,ppc64el] http://mirror.nodesdirect.com/mariadb/repo/10.0/debian jessie main
    | Ubuntu 16.04 | 0xF1656F24C74CD1D8 |   10.1  | deb [arch=amd64,i386,ppc64el] http://mirror.nodesdirect.com/mariadb/repo/10.1/ubuntu xenial main
    | Ubuntu 16.04 | 0xF1656F24C74CD1D8 |   10.0  | deb [arch=amd64,i386,ppc64el] http://mirror.nodesdirect.com/mariadb/repo/10.1/ubuntu xenial main

    Not every distribution may have a released version available. For instance, Debian 8 offers versions 10.0 and 10.1, while Debian 9 has only version 10.1. For a complete list of available distributions, visit the MariaDB repository's download page.

3.  Install MariaDB, Galera, and Rsync:

    - **Recommended** - MariaDB 10.1 and above:

            sudo apt update && sudo apt install -y rsync mariadb-server

    - MariaDB 10.0:

            sudo apt update && sudo apt install -y rsync galera mariadb-galera-server

## Configuring Galera

1.  Create the file `/etc/mysql/conf.d/galera.cnf` on each of the Uthos with the following content. Replace the IP addresses in the `wsrep_cluster_address` section with the private IP addresses of each of the Uthos:

    {{< file "/etc/mysql/conf.d/galera.cnf" ini >}}
[mysqld]
#mysql settings
binlog_format=ROW
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
query_cache_size=0
query_cache_type=0
bind-address=0.0.0.0

#galera settings
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so
wsrep_cluster_name="my_wsrep_cluster"
wsrep_cluster_address="gcomm://192.168.1.3,192.168.1.4,192.168.1.5"
wsrep_node_address="192.168.1.2"
wsrep_node_name="node_1"
wsrep_sst_method=rsync
{{< /file >}}

2.  Reboot both of your non-primary servers in the cluster to enable the new `galera.cnf` file settings.

3.  Stop the MariaDB service on each Utho after installation:

        sudo service mysql stop

4.  Restart the MariaDB service on the primary Utho, with the `--wsrep-new-cluster` flag:

        sudo mysqld --wsrep-new-cluster

5.  Confirm that the cluster has started by opening the MariaDB monitor:

        $ mysql -u root -p
        Enter password:
        Welcome to the MariaDB monitor.  Commands end with ; or \g.
        Your MariaDB connection id is 6
        Server version: 10.1.29-MariaDB-1~xenial mariadb.org binary distribution

        Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

        Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

        MariaDB [(none)]> SHOW STATUS LIKE 'wsrep_cluster_%';

    Alternatively, this can be run inline as a statement:

        $ mysql -u root -p -e "SHOW STATUS LIKE 'wsrep_cluster_%'"
        Enter password:
        +--------------------------+--------------------------------------+
        | Variable_name            | Value                                |
        +--------------------------+--------------------------------------+
        | wsrep_cluster_conf_id    | 1                                    |
        | wsrep_cluster_size       | 1                                    |
        | wsrep_cluster_state_uuid | 8534672c-d39a-11e7-814b-aba9f331bda4 |
        | wsrep_cluster_status     | Primary                              |
        +--------------------------+--------------------------------------+

6.  Start the MariaDB service on the other two Uthos.

        sudo service mysql start

    Re-run the command from step 5 to ensure that each system has joined the cluster:

        +--------------------------+--------------------------------------+
        | Variable_name            | Value                                |
        +--------------------------+--------------------------------------+
        | wsrep_cluster_conf_id    | 3                                    |
        | wsrep_cluster_size       | 3                                    |
        | wsrep_cluster_state_uuid | 8534672c-d39a-11e7-814b-aba9f331bda4 |
        | wsrep_cluster_status     | Primary                              |
        +--------------------------+--------------------------------------+


7.  To prevent repeated errors on startup, copy the `/etc/mysql/debian.cnf` file from your primary Utho in the cluster to each of your other Uthos, overwriting the existing copies.

8.  Reboot both of your secondary Uthos to apply the new `debian.cnf` settings.

## Firewall Settings
Communication between nodes are unencrypted. Even if using a private IP, this information is potentially open within the data center.

1.  Enable UFW.

        sudo ufw enable

2.  There are four ports to secure. Repeat for each connection as necessary.

        sudo ufw allow from 192.168.1.2 to any port 3306,4567,4568,4444 proto tcp
        sudo ufw allow from 192.168.1.2 to any port 3306,4567,4568,4444 proto udp

    Purpose of each port:

- 3306: State Snapshot Transfer using mysqldump
- 4567: Cluster replication communication
- 4568: Incremental State Transfer
- 4444: Other types of State Snapshot Transfer

## Test Database Replication

1.  Log in to MariaDB on each of the Uthos:

        mysql -u root -p

1.  Create a test database and insert a row on your primary Utho:

        create database test;
        create table test.flowers (`id` varchar(10));

2.  From each of the other servers, list the tables in your test database:

        show tables in test;

    You should receive an output of the database and row that you created in the previous step:

        MariaDB [(none)]> show tables in test;
        +----------------+
        | Tables_in_test |
        +----------------+
        | flowers        |
        +----------------+
        1 row in set (0.00 sec)

Congratulations, you have now configured a MariaDB cluster with Galera.

