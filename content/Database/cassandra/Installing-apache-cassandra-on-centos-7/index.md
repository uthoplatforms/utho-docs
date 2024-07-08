---
slug: Installing-apache-cassandra-on-centos-7
description: 'This guide will show you how to deploy a scalable and development-driven NoSQL database with Apache Cassandra on a Utho running CentOS 7.'
keywords: ["cassandra", " apache cassandra", " centos 7", " ubuntu 18.04", " database", " nosql"]
published: 2024-06-04
modified: 2024-06-04
modified_by:
  name: Utho
title: "Installing Apache Cassandra on CentOS 7"
title_meta: "How to Install Apache Cassandra on CentOS 7"
relations:
    platform:
        key: install-apache-cassandra
        keywords:
            - distribution: CentOS 7
tags: ["centos","database","nosql"]
authors: ["Pawan Kumar"]
---

## Introduction to Apache Cassandra

The Cassandra NoSQL database is perfect for scenarios requiring high data redundancy and uptime, easy horizontal scaling across multiple servers, and the flexibility to adapt to rapidly changing project needs during development. Apache Cassandra is an open-source database managed through a simple command-line interface using the Cassandra Query Language (CQL), which is similar to SQL, making it accessible for those with SQL experience.

By following this guide, you'll set up a single-node, production-ready installation of Apache Cassandra on your Utho. This tutorial will cover basic configuration options and security hardening for the database. To execute the commands in this guide, you will need to run them as the "root" user or use an account with root privileges, prefixing each command with sudo.

## Install Cassandra

### Before You Begin

- Complete the Getting Started guide to set up a new Utho.

- Although it is recommended to follow the entire Securing Your Server guide, you will at least need a limited user account.

### Add Repositories and GPG Keys

Install the "yum-utils" package:

        yum install yum-utils -y

Add the Datastax repository:

        yum-config-manager --add-repo http://rpm.datastax.com/community

Add the public key for the Datastax repository. First, create a directory for the downloaded key:

        mkdir ~/.keys

Navigate to the ".keys" directory you just created and download the public key:

        curl -o repo_key http://rpm.datastax.com/rpm/repo_key

The key should now be in a file named "repo_key". Install the key using the package manager:

        rpm --import repo_key

## Install Cassandra and Supporting Applications

Update the system and install Java along with Cassandra. Installing NTP will ensure the Cassandra node stays synchronized with the correct time.

1.  Install Cassandra, Java, and NTP:

        yum update && yum upgrade
        yum install java dsc30 cassandra30-tools ntp

## Activate Cassandra

Enable Cassandra to start on system boot and verify that it is running:

        systemctl enable cassandra
        systemctl start cassandra
        systemctl -l status cassandra

3.  Check the status of the Cassandra cluster:

        nodetool status

If the output displays UN, the cluster is operational. Your output should look similar to this:

    {{< output >}}
    Status=Up/Down
    |/ State=Normal/Leaving/Joining/Moving
    --  Address    Load       Tokens       Owns (effective)  Host ID                               Rack
    UN  127.0.0.1  103.51 KiB  256          100.0%            c43a2db6-8e5f-4b5e-8a83-d9b6764d923d  rack1
{{< /output >}}

    If you receive connection errors, see [Troubleshooting Connection Errors](#troubleshooting-connection-errors).

## Configure Cassandra

### Enable Security Features

Enable user login authentication. First, create a backup of the Cassandra configuration file "cassandra.yaml."

    {{< note respectIndent=false >}}
The CentOS 7 installation already includes a backup file located at `/etc/cassandra/conf/cassandra.yaml.orig`.
{{< /note >}}

        cp /etc/cassandra/cassandra.yaml /etc/cassandra/cassandra.yaml.backup

Open "cassandra.yaml" in your preferred text editor:

        vim /etc/cassandra/conf/cassandra.yaml

Ensure the following variables in the file match the values shown below. If any values are commented out, uncomment them. Customize the remaining properties in the cassandra.yaml configuration file according to your project's specific needs and how you intend to utilize Cassandra. The default configuration is generally sufficient for development purposes.

For further details on this file, refer to the Cassandra .yaml Configuration File Overview link in the "External Resources" section.

{{< file "/etc/cassandra/conf/cassandra.yaml" yaml >}}
. . .
authenticator: org.apache.cassandra.auth.PasswordAuthenticator
authorizer: org.apache.cassandra.auth.CassandraAuthorizer
role_manager: CassandraRoleManager
roles_validity_in_ms: 0
permissions_validity_in_ms: 0
. . .
{{< /file >}}

After editing the file restart Cassandra.

## Add An Administration Superuser

Open the Cassandra command terminal by typing cqlsh. Log in with the following credentials for the default user cassandra:

        cqlsh -u cassandra -p cassandra

Create a new superuser. Replace the placeholders with the appropriate information:

        cassandra@cqlsh> CREATE ROLE [new_superuser] WITH PASSWORD = '[secure_password]' AND SUPERUSER = true AND LOGIN = true;

1. Log out by typing `exit`.

Log in again using the new superuser account with the updated credentials, and then remove the elevated permissions from the Cassandra account:

        superuser@cqlsh> ALTER ROLE cassandra WITH PASSWORD = 'cassandra' AND SUPERUSER = false AND LOGIN = false;
        superuser@cqlsh> REVOKE ALL PERMISSIONS ON ALL KEYSPACES FROM cassandra;

Grant all permissions to the new superuser account. Replace the placeholders with your superuser account username:

        superuser@cqlsh> GRANT ALL PERMISSIONS ON ALL KEYSPACES TO [superuser];

6.  Log out by typing `exit`.

## Edit The Console Configuration File

The cqlshrc file contains configuration settings that affect how Cassandra performs certain tasks based on user preferences. Before proceeding, switch from the "root" user to your administrative Linux user account (you need sudo privileges for this).

Because your Cassandra username and password can be stored here in plaintext, this file should only be accessible to your administrative user account and should not be accessible to other accounts on your Linux system. Exercise caution before proceeding with the [authentication] section.

Create the cqlshrc file using your preferred text editor. If the ~/.cassandra directory does not exist, create it:

        sudo mkdir ~/.cassandra
        sudo vim ~/.cassandra/cqlshrc

Copy any sections below that you want to include in your configuration. For more details on this file, refer to the "Cassandra cqlshrc File Configuration Overview" link in the "External Resources" section.

Note: CentOS 7 users can access a sample file containing all configuration options at /etc/cassandra/conf/cqlshrc.sample.

    {{< file "~/.cassandra/cqlshrc" aconf >}}
. . .
;; Options that are common to both COPY TO and COPY FROM

[copy]
;; The string placeholder for null values
nullval=null
;; For COPY TO, controls whether the first line in the CSV output file will
;; contain the column names.  For COPY FROM, specifies whether the first
;; line in the CSV file contains column names.
header=true
;; The string literal format for boolean values
boolstyle = True,False
;; Input login credentials here to automatically login to the Cassandra command line without entering them each time. When this
;; is enabled, just type "cqlsh" to start Cassandra.
[authentication]
username=[superuser]
password=[password]

;; Uncomment to automatically use a certain keyspace on login
;; keyspace=[keyspace]

[ui]
color=on
datetimeformat=%Y-%m-%d %H:%M:%S%z
completekey=tab
;; The number of digits displayed after the decimal point
;; (note that increasing this to large numbers can result in unusual values)
float_precision = 5
;; The encoding used for characters
encoding = utf8
. . .
{{< /file >}}

Save and close the file. Update the permissions for both the file and directory accordingly.

        sudo chmod 440 ~/.cassandra/cqlshrc
        sudo chmod 700 ~/.cassandra

If you have enabled the auto-login feature, simply type cqlsh to log in. The command terminal should open with your superuser name visible in the command line.

## Rename the Cluster

Update your default cluster name from "Test Cluster" to your desired name.

Log in to the control terminal using cqlsh. Replace [new_name] with your new cluster name:

        UPDATE system.local SET cluster_name = '[new_name]' WHERE KEY = 'local';

Edit the cassandra.yaml file and replace the value in the cluster_name variable with the new cluster name you've just set.

        vim /etc/cassandra/conf/cassandra.yaml

3.  Save and close.

From the Linux terminal (not cqlsh), execute nodetool flush system. This command clears the system cache while preserving all data on the node.

Restart Cassandra. After logging in with cqlsh, verify that the new cluster name is visible.

## Troubleshooting Connection Errors

If you encounter connection errors while running nodetool status, you may need to manually enter networking information.

Open the cassandra-env.sh file in a text editor.

        sudo vim /etc/cassandra/conf/cassandra-env.sh

Search for -Djava.rmi.server.hostname= in the file. Uncomment this line and replace <public name> with your loopback address or public IP address at the end of the line:

    {{< file "/etc/cassandra/conf/cassandra-env.sh" bash >}}
. . .
JVM_OPTS="$JVM_OPTS -Djava.rmi.server.hostname=<public name>"
. . .
{{< /file >}}

Restart Cassandra after completing the updates to the cassandra-env.sh file:

        sudo systemctl restart cassandra

Check the node status again once the service has restarted:

        nodetool status

Note: It may take a few seconds for Cassandra to refresh the configuration. If you encounter another connection error, wait for 15 seconds before rechecking the node status.

## Where To Go From Here

Explore the links in the "External Resources" section for further Cassandra configuration options and resources to enhance your understanding and utilization of Cassandra. To maximize Cassandra's capabilities in a production environment, consider adding additional nodes to your cluster. Refer to the companion guide, "Deploy Additional Nodes to the Cassandra Cluster," to begin this process.