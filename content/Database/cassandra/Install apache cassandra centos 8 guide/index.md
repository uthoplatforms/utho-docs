---
slug: Install apache cassandra centos 8 guide
description: 'Deploying Apache Cassandra on CentOS 8: A Guide for Scalable, Development-Driven NoSQL Database Deployment'
keywords: ["cassandra", " apache cassandra", " centos 7", "CentOS8", " database", " nosql"]
published: 2024-07-08
modified: 2024-07-08
modified_by:
  name: Utho
title: "Install Apache Cassandra on CentOS 8"
title_meta: "How to Install Apache Cassandra on CentOS 8"
relations:
    platform:
        key: install-apache-cassandra
        keywords:
            - distribution: CentOS 8
tags: ["centos","database","nosql"]
authors: ["Utho"]
---

After following this guide, you'll successfully set up a production-ready instance of Apache Cassandra on your Utho server running CentOS 8. This tutorial includes essential configuration steps and methods to enhance the security and resilience of your database setup.

{{< note >}}
To execute the commands in this guide successfully, ensure you either run them as the root user or use an account with root privileges. Prefix each command with `sudo` for proper execution.
{{< /note >}}

## Before You Begin

Follow the Getting Started guide to set up your new Utho.

It's strongly recommended to complete the entire Securing Your Server guide. At the very least, ensure you create a limited user account.

### Add Repositories and GPG Keys

1.  Install the "yum-utils" package:

        sudo yum install yum-utils -y

Add the Datastax repository to install the necessary Cassandra software later:

        sudo yum-config-manager --add-repo http://rpm.datastax.com/community

Add the Datastax repository's public key. First, create a directory to store the downloaded key:

        mkdir ~/.keys

Navigate to the `.keys` directory and download the public key using `curl`:

        curl -o repo_key http://rpm.datastax.com/rpm/repo_key

Once downloaded, the key will be in a file named "repo_key". Install this key using your package manager:

        sudo rpm --import repo_key

## Install Cassandra and Supporting Applications

In this section, you'll update your Linux system software, install necessary package dependencies, Java, and Cassandra.

Install Cassandra, Java, and NTP:

        sudo yum update && sudo yum upgrade
        sudo yum install java dsc30 cassandra30-tools

Install Python. The Cassandra `cqlsh` interpreter relies on Python for its execution, which you will need for subsequent sections of this guide.

        sudo dnf install python2

## Activate Cassandra

Enable Cassandra to start automatically on system boot and verify its status:

        sudo systemctl enable cassandra
        sudo systemctl start cassandra
        sudo systemctl -l status cassandra

Check the status of the Cassandra cluster:

        nodetool status

If the output shows `UN`, the cluster is operational. Your output should look similar to this:

    {{< output >}}
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address    Load       Tokens       Owns (effective)  Host ID                               Rack
UN  127.0.0.1  103.51 KiB  256          100.0%            c43a2db6-8e5f-4b5e-8a83-d9b6764d923d  rack1
    {{< /output >}}

If you encounter connection errors, refer to Troubleshooting Connection Errors.

## Configure Cassandra

### Enable Security Features

In this section, you'll enable user login authentication and configure additional security settings as per your project requirements.

Create a backup of the Cassandra configuration file cassandra.yaml.

        sudo cp /etc/cassandra/conf/cassandra.yaml /etc/cassandra/conf/cassandra.yaml.backup

Open `cassandra.yaml` in your preferred text editor.

    {{< note respectIndent=false >}}
The location of the cassandra.yaml file might vary slightly across different Linux distributions.
    {{< /note >}}

        sudo vim /etc/cassandra/conf/cassandra.yaml

Ensure the following variables in the file match the values shown in the example file. Uncomment any commented-out values. Customize the remaining properties in the cassandra.yaml file according to your project's specific needs and intended use of Cassandra. The default configuration is generally suitable for development purposes.

    {{< file "CentOS /etc/cassandra/conf/cassandra.yaml" yaml >}}
. . .

authenticator: org.apache.cassandra.auth.PasswordAuthenticator
authorizer: org.apache.cassandra.auth.CassandraAuthorizer
role_manager: CassandraRoleManager
roles_validity_in_ms: 0
permissions_validity_in_ms: 0

. . .
    {{< /file >}}

For more details about this file, refer to the Cassandra Configuration File guide in Apache's official documentation.

After editing the configuration file, restart Cassandra.

        sudo systemctl restart cassandra

### Add An Administration Superuser

Open the Cassandra command terminal by typing `cqlsh`. Log in with the following credentials for the default user cassandra:

        cqlsh -u cassandra -p cassandra

Create a new superuser. Replace the placeholders with your specific information:

        CREATE ROLE [new_superuser] WITH PASSWORD = '[secure_password]' AND SUPERUSER = true AND LOGIN = true;

Log out by typing exit.

Log back in using the new superuser account. Replace the placeholders with your new credentials:

        cqlsh -u new-super-user -p my-scecure-password

Revoke elevated permissions from the default Cassandra account:

        ALTER ROLE cassandra WITH PASSWORD = 'cassandra' AND SUPERUSER = false AND LOGIN = false;
        REVOKE ALL PERMISSIONS ON ALL KEYSPACES FROM cassandra;

Grant all permissions to the new superuser account. Replace the placeholders with your superuser account username:

        GRANT ALL PERMISSIONS ON ALL KEYSPACES TO '[superuser]';

Log out by typing exit.

### Edit The Console Configuration File

The `cqlshrc` file contains configuration settings that affect user preferences and Cassandra's operational behavior.

{{< note >}}
Make sure to follow the steps in this section using your limited user account. This account must have sudo privileges if it hasn't already been granted.
{{< /note >}}

To safeguard your Cassandra username and password, ensure that the cqlshrc file is accessible only to your administrative user account. It should be configured to be inaccessible to other accounts on your Linux system.

{{< note type="alert" >}}
Avoid completing this section as the root user. Before proceeding, carefully assess the security implications and potential impact on your node cluster before adding the [authentication] section.
{{< /note >}}

Using your preferred text editor, create the cqlshrc file. If the ~/.cassandra directory isn't already present, create it:

        sudo mkdir ~/.cassandra
        sudo vim ~/.cassandra/cqlshrc

Copy any sections below that you want to add to your configuration. Replace the placeholders [superuser] and [password] with your actual values. For more information about this file, refer to the Configuring cqlsh From a File guide on the DataStax site.


    {{< note respectIndent=false >}}
You can refer to the example file located at `/etc/cassandra/conf/cqlshrc.sample` for a complete list of configuration options.
    {{< /note >}}

    {{< file "~/.cassandra/cqlshrc" aconf >}}

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

{{< /file >}}

Save and close the file.

Update the `cqlshrc` file and directory with the following permissions:

        sudo chmod 440 ~/.cassandra/cqlshrc
        sudo chmod 700 ~/.cassandra

Log in by entering the command below. You will be prompted to enter your password. The cqlsh command terminal will open, displaying your superuser name in the command line.

        cqlsh -u superuser

    {{< note respectIndent=false >}}
You can also login by providing your username and password:

    cqlsh -u superuser -p password
    {{< /note >}}

## Rename the Cluster

In this section, you'll change the default cluster name from "Test Cluster" to your preferred name.

If you're not already logged in, access the `cqlsh` control terminal.

        cqlsh -u superuser

Replace [new_name] with your desired cluster name.

        UPDATE system.local SET cluster_name = '[new_name]' WHERE KEY = 'local';

Type exit to exit the cqlsh control terminal and return to the Linux command line.

Edit the cassandra.yaml file and update the cluster_name variable with the new cluster name you've just set.

        sudo vim /etc/cassandra/conf/cassandra.yaml

Save and close.

From the Linux terminal (not `cqlsh`), clear the system cache. This command will not affect your node's data.

        nodetool flush system

Restart Cassandra:

        sudo systemctl restart cassandra

Log in to `cqlsh` and confirm that the new cluster name is displayed.

        cqlsh -u superuser

    {{< output >}}
Connected to my-cluster-name at 127.0.0.1:9042.
[cqlsh 5.0.1 | Cassandra 4.0 | CQL spec 3.4.5 | Native protocol v4]
Use HELP for help.
superuser@cqlsh>
    {{</ output >}}

## Troubleshooting Connection Errors

If you encounter connection errors while running `nodetool status`, you might need to manually specify networking information.

Open the `cassandra-env.sh` file in a text editor.

        sudo vim /etc/cassandra/conf/cassandra-env.sh

Look for `-Djava.rmi.server.hostname=` within the file. Uncomment this line and replace <public name> at the end with your loopback address or public IP address:

    {{< file "/etc/cassandra/conf/cassandra-env.sh" bash >}}
. . .
JVM_OPTS="$JVM_OPTS -Djava.rmi.server.hostname=<public name>"
. . .
{{< /file >}}

After updating the cassandra-env.sh file, restart Cassandra:

        sudo systemctl restart cassandra

Once the service restarts, verify the node status again:

        nodetool status

    {{< note respectIndent=false >}}
It may take a few moments for Cassandra to apply the new configuration. If you encounter another connection error, wait approximately 15 seconds before checking the node status again.
{{< /note >}}

## Where To Go From Here

Explore the links in the More Information section for further customization of Cassandra to suit your requirements and to enhance your understanding and proficiency with Cassandra.

For leveraging Cassandra's capabilities in a production environment, consider adding additional nodes to your cluster. Refer to the companion guide Adding Nodes to an Existing Cluster for detailed instructions.