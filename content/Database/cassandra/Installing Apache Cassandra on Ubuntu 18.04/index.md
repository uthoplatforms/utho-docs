---
slug: Installing Apache Cassandra on Ubuntu 18.04
description: 'This guide presents instructions to deploy a scalable and development-driven NoSQL database with Apache Cassandra for Ubuntu 18.04.'
keywords: ["cassandra", " apache cassandra", " centos 7", " ubuntu 18.04", " database", " nosql"]
published: 2024-07-08
modified: 2024-07-08
modified_by:
  name: Utho
title: "Installing Apache Cassandra on Ubuntu 18.04"
title_meta: "How to Install Apache Cassandra on Ubuntu 18.04"
relations:
    platform:
        key: install-apache-cassandra
        keywords:
            - distribution: Ubuntu 18.04
tags: ["ubuntu","database","nosql"]
authors: ["Utho"]
---

After following this tutorial, you'll have a single-node, production-ready setup of Apache Cassandra installed on your Utho running Ubuntu 18.04. This guide covers essential configuration steps and includes measures to enhance database security.

{{< note >}}
To successfully execute the commands in this guide, ensure you have root privileges by logging in as the root user or using an account with `sudo` privileges. Prefix each command with sudo as necessary.
{{< /note >}}

## Before You Begin

Follow the Getting Started guide to set up your new Utho.

It's recommended to complete the entire Securing Your Server guide. At a minimum, create a limited user account.

## Install OpenJDK (Java)

Check if Java is installed and verify that it is a compatible version for Apache Cassandra, which requires either OpenJDK 8 or OpenJDK 11 (both LTS releases).
        
        java -version

If Java is not installed, you can install OpenJDK. The command below installs OpenJDK 11, though you can replace the package with `openjdk-8-jdk` if you prefer to use OpenJDK 8.

        apt update
        apt install openjdk-11-jdk

## Install Cassandra

To add the Apache Cassandra repository, follow these steps:

        echo "deb http://www.apache.org/dist/cassandra/debian 40x main" |  sudo tee /etc/apt/sources.list.d/cassandra.list

    {{< note respectIndent=false >}}
The command above installs the latest version within the Apache Cassandra 4.0 release. To explore other available versions, you can visit the repository and modify the command accordingly.
{{< /note >}}

Add the keys required to access the repository:

        curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -

Get the latest package information from the newly added source and install Apache Cassandra:

        sudo apt update
        sudo apt install cassandra

## Activate Cassandra

Enable Cassandra to start on system boot and verify that it is running:

        sudo systemctl enable cassandra
        sudo systemctl start cassandra
        sudo systemctl -l status cassandra

Check the status of the Cassandra cluster:

        nodetool status

If `UN` is displayed in the output, the cluster is working. Your output should resemble the following:

    {{< output >}}
Status=Up/Down
|/ State=Normal/Leaving/Joining/Moving
--  Address    Load       Tokens       Owns (effective)  Host ID                               Rack
UN  127.0.0.1  103.51 KiB  256          100.0%            c43a2db6-8e5f-4b5e-8a83-d9b6764d923d  rack1
    {{< /output >}}

If you encounter connection errors, refer to the Troubleshooting Connection Errors section for assistance.

## Configure Cassandra

### Enable Security Features

In this section, we will enable user login authentication and configure additional security settings as per your project requirements.

Begin by making a backup of the Cassandra configuration file `cassandra.yaml`.

        sudo cp /etc/cassandra/cassandra.yaml /etc/cassandra/cassandra.yaml.backup


Open cassandra.yaml in your preferred text editor. 

- Note that the location of the cassandra.yaml file may vary depending on your Linux distribution.

        sudo vim /etc/cassandra/cassandra.yaml

Match the following variables in the file to the values shown in the example file. If any values are commented out, uncomment them. Adjust the remaining properties in the `cassandra.yaml` file according to your project's specific requirements and how you intend to use Cassandra. The default configuration typically suits development environments well.

    {{< file "Ubuntu /etc/cassandra/cassandra.yaml" yaml >}}
. . .

authenticator: org.apache.cassandra.auth.PasswordAuthenticator
authorizer: org.apache.cassandra.auth.CassandraAuthorizer
role_manager: CassandraRoleManager
roles_validity_in_ms: 0
permissions_validity_in_ms: 0

. . .
    {{< /file >}}

After editing the configuration file, restart Cassandra to apply the changes. For more detailed information about this file, refer to the Cassandra Configuration File guide in Apache's official documentation.

        sudo systemctl restart cassandra

### Add An Administration Superuser

Open the Cassandra command terminal by typing `cqlsh`. Log in with the credentials shown below for the default user cassandra:

        cqlsh -u cassandra -p cassandra

Create a new superuser. Replace the brackets with the applicable information:

        CREATE ROLE [new_superuser] WITH PASSWORD = '[secure_password]' AND SUPERUSER = true AND LOGIN = true;

Log out by typing `exit`.

Log back in with the new superuser account. Replace [username] and [password] with your new credentials:

        cqlsh -u new-super-user -p my-scecure-password

Remove the elevated permissions from the Cassandra account:

        ALTER ROLE cassandra WITH PASSWORD = 'cassandra' AND SUPERUSER = false AND LOGIN = false;
        REVOKE ALL PERMISSIONS ON ALL KEYSPACES FROM cassandra;

Grant all permissions to the new superuser account. Replace [username] with your superuser account username:

        GRANT ALL PERMISSIONS ON ALL KEYSPACES TO [superuser];

Log out by typing `exit`.

### Edit The Console Configuration File


Here's the rewritten section:

The cqlshrc file contains configuration settings that affect user preferences and Cassandra's behavior during operations.

Note: Ensure you perform these steps using your limited user account. If necessary, ensure it has sudo privileges.

To secure your Cassandra username and password, the cqlshrc file should only be accessible to your administrative user account and inaccessible to other system accounts.

Caution: Proceed with caution and consider the security implications for your Cassandra cluster before implementing the [authentication] section.

Create the cqlshrc file using your preferred text editor. If the ~/.cassandra directory does not exist, create it:

        sudo mkdir ~/.cassandra
        sudo vim ~/.cassandra/cqlshrc

Copy any sections below that you want to add to your configuration, replacing [superuser] and [password] with your actual values. For more details on this file, refer to the Configuring cqlsh From a File guide on the DataStax site.

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


Save and close the file.

Update the permissions for the `cqlshrc` file and directory as follows:

        sudo chmod 440 ~/.cassandra/cqlshrc
        sudo chmod 700 ~/.cassandra

Log in by typing the following command. You will be prompted to enter your password. The `cqlsh` command terminal should open, displaying your superuser name in the command line.

        cqlsh -u superuser

    {{< note respectIndent=false >}}
You can also login by providing your username and password:

    cqlsh -u superuser -p password
    {{< /note >}}

## Rename the Cluster

In this section, you will change the default cluster name from "Test Cluster" to your preferred name.

Log into the `cqlsh` control terminal if you are not already logged in.

        cqlsh -u superuser

Replace [new_name] with your desired cluster name:

        UPDATE system.local SET cluster_name = '[new_name]' WHERE KEY = 'local';

Type `exit` to return to the Linux command line.

Edit the `cassandra.yaml` file and replace the value in the `cluster_name` variable with the new cluster name you just set.

        sudo vim /etc/cassandra/cassandra.yaml

Save and close.

From the Linux terminal (not cqlsh), clear the system cache using the following command. This operation will not affect your node's data.

        nodetool flush system

Restart Cassandra:

        sudo systemctl restart cassandra

Log in to cqlsh and verify that the new cluster name is displayed.

        cqlsh -u superuser

    {{< output >}}
Connected to my-cluster-name at 127.0.0.1:9042.
[cqlsh 5.0.1 | Cassandra 4.0 | CQL spec 3.4.5 | Native protocol v4]
Use HELP for help.
superuser@cqlsh>
    {{</ output >}}

## Troubleshooting Connection Errors

If you encounter connection errors while executing `nodetool status`, you might need to manually input networking details.

Open the `cassandra-env.sh` file using a text editor.

        sudo vim /etc/cassandra/cassandra-env.sh

Look for `-Djava.rmi.server.hostname=` within the file. Uncomment this line and specify your loopback address or public IP address by replacing <public name> at the end of the line:

    {{< file "/etc/cassandra/cassandra-env.sh" bash >}}
. . .
JVM_OPTS="$JVM_OPTS -Djava.rmi.server.hostname=<public name>"
. . .
{{< /file >}}

After completing the changes to `cassandra-env.sh`, restart Cassandra:

        sudo systemctl restart cassandra

Verify the node status again after the service restarts:

        nodetool status

    {{< note respectIndent=false >}}
It may take a few seconds for Cassandra to apply the updated configuration. If you encounter another connection error, wait for 15 seconds before rechecking the node status.
{{< /note >}}

## Where To Go From Here

Be sure to explore the links provided in the More Information section, which offer further guidance on configuring Cassandra to suit your requirements and enhancing your understanding and proficiency with Cassandra.

To maximize Cassandra's capabilities in a production environment, consider adding additional nodes to your cluster. For detailed instructions, refer to the companion guide Adding Nodes to an Existing Cluster.