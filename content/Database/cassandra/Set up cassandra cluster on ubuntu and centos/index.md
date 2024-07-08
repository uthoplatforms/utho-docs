---
slug: "Set up cassandra cluster on ubuntu and centos"
description: "This guide walks you through the steps to deploy a production-ready Apache Cassandra node cluster"
keywords: ["cassandra", " apache-cassandra", " centos 7", " ubuntu 16.04", " database", " nosql"]
published: 2024-07-08
modified: 2024-07-08
modified_by:
  name: Utho
title: "Setting Up a Cassandra Node Cluster "
title_meta: "Set Up a Cassandra Node Cluster"
authors: ["Pawan Kumar"]
---

## What is Apache Cassandra

Apache Cassandra is an open-source database managed through a simple command line interface using CQL, or Cassandra Query Language. CQL is similar to SQL, making it easy for those familiar with SQL to learn.

Cassandra NoSQL databases are perfect for scenarios that demand high data redundancy, maximum uptime, easy horizontal scaling across multiple servers, and flexibility for evolving project needs—areas where traditional relational databases may fall short.

To follow this guide, you need at least two Cassandra nodes set up on two separate servers. This guide will teach you how to connect your Cassandra nodes to form a true cluster.

Additionally, you will learn how to secure communication between your nodes and fortify your cluster against common failure points. By the end, your cluster will be production-ready and optimized for maximum uptime.

## Before You Begin

Ensure you have at least two Cassandra nodes set up and configured. These nodes should have equal or similar hardware specifications to avoid bottlenecks. For installation instructions, refer to the Installing Apache Cassandra guide and choose your distribution.

A working firewall is essential for security. This guide provides specific instructions for UFW, FirewallD, and IPtables. For UFW setup, see How to Configure a Firewall with UFW. For FirewallD, refer to Introduction to FirewallD on CentOS.

Many commands in this guide require root privileges. You can follow the guide as-is if you are using the root account. Alternatively, you can use an elevated user account with sudo privileges by prefixing each command with `sudo`.

## Prepare the Cassandra Nodes for Clustering

The following instructions must be executed on each Cassandra node in the cluster. Ensure that the same configuration is applied to each node unless otherwise specified.

Clear the default data from the Cassandra system table to import the new values set in the `cassandra.yaml` configuration file:

        systemctl stop cassandra
        rm -rf /var/lib/cassandra/data/system/*

Edit the cassandra.yaml file and set the appropriate values for each variable indicated below:

    | Property  | Explanation |
    | -- | -- |
    | `cluster_name` | Choose your cluster name here. |
    | `seed_provider` | This contains a comma-delimited list of each public IP address of each node to be clustered. Input the list in the line that reads `- seeds: "127.0.0.1"`.  |
    | `listen_address` | Other nodes in the cluster will use the IP address listed here to find each other. Change from `localhost` to the specific node's public IP address. |
    | `rpc_address` | The listen address for client communication. Change from "localhost" to the public IP address or loopback address of the node. |
    | `endpoint_snitch` | Snitches determine how Cassandra replicates data. Change this to "GossipingPropertyFileSnitch," as this is more suitable to a multi-data center configuration. |
    | `auto_bootstrap` | Add this property anywhere in the file. If you have yet to add data to your nodes - that is, you would start with a fresh cluster - set this to "false." If your node(s) already contains data, **do not** add this property. |
    | `num_tokens` | This property defines the proportion of data stored on each node. For nodes with equal hardware capabilities, this number should be set equally between them so the data is more likely to be evenly distributed. The default value of 256 is likely to ensure equal data distribution. For more information on this topic, see the "How data is distributed across a cluster" link in the "External Resources" section. |

    {{< file "/etc/cassandra/conf/cassandra.yaml" >}}
cluster_name: '[Your Cluster Name]'
listen_address: [public_ip_address]
rpc_address: [public_ip_address]
num_tokens: 256
seed_provider:
  - class_name: org.apache.cassandra.locator.SimpleSeedProvider
parameters:
  - seeds: "[node1_ip_address],[node2_ip_address]"
endpoint_snitch: GossipingPropertyFileSnitch
auto_bootstrap: false
{{< /file >}}

Edit the `cassandra-rackdc.properties` file, assigning the same data center and rack name to each node:

    {{< file "/etc/cassandra/conf/cassandra-rackdc.properties" >}}
# These properties are used with GossipingPropertyFileSnitch and will
# indicate the rack and dc for this node
dc=DC1
rack=RACK1
{{< /file >}}

## Edit Firewall Settings

### Open Cassandra Communication Ports

Ports `7000` and `9042` must be available for external nodes to connect. For security, restrict connections to these ports to only the IP addresses of other nodes in the cluster. Depending on your preference, you can use UFW, FirewallD, or iptables for this configuration.

**UFW**

    ufw allow proto tcp from [external_node_ip_address] to any port 7000,9042 comment "Cassandra TCP"

**FirewallD**

    firewall-cmd --permanent --zone=public --add-rich-rule='
        rule family="ipv4"
        source address="[external_node_ip_address]"
        port protocol="tcp" port="7000" accept'

    firewall-cmd --permanent --zone=public --add-rich-rule='
        rule family="ipv4"
        source address="[external_node_ip_address]"
        port protocol="tcp" port="9042" accept'

    firewall-cmd --reload

**iptables**

    -A INPUT -p tcp -s [external_node_ip_address] -m multiport --dports 7000,9042 -m state --state NEW,ESTABLISHED -j ACCEPT

## Test the Cluster Setup

### Boot Cassandra

Start Cassandra on each node one at a time using `systemctl start cassandra`. After starting each node, run nodetool status to verify that each node in your cluster is listed in the output.

## Enable Node-to-Node Encryption

Setting up encryption between nodes provides additional security and protects the data transferred between Cassandra nodes. The commands in this section only need to be executed on one node in your cluster; then, distribute the appropriate files across the rest of the nodes.

### Generate SSL Files

Create a new directory named `.keystore` in the Cassandra config directory. Navigate to this directory:

        mkdir /etc/cassandra/conf/.keystore
        cd /etc/cassandra/conf/.keystore

Generate a configuration file for OpenSSL to automate the certificate creation process. Copy the following content into a new file named `rootCAcert.conf`. Replace the placeholder values (`examplePassword`, `US`, `WA`, `Seattle`) with your specific information:

    {{< file "~/.keystore/rootCAcert.conf" aconf >}}
[ req ]
distinguished_name     = req_distinguished_name
prompt                 = no
output_password        = examplePassword
default_bits           = 4096

[ req_distinguished_name ]
C                      = US
ST                     = WA
L                      = Seattle
OU                     = Cluster_Name
CN                     = Cluster_Name_MasterCA

{{< /file >}}

Create the public and private key files.

        openssl req -config rootCAcert.conf -new -x509 -nodes -keyout ca-cert.key -out ca-cert.cert -days 365

Generate a keystore for each node in your cluster. The following commands demonstrate the sequence for a two-node cluster.

        keytool -genkeypair -keyalg RSA -alias node1 -keystore node1-keystore.jks -storepass cassandra -keypass cassandra -validity 365 -keysize 4096 -dname "CN=node1, OU=[cluster_name]"
        keytool -genkeypair -keyalg RSA -alias node2 -keystore node2-keystore.jks -storepass cassandra -keypass cassandra -validity 365 -keysize 4096 -dname "CN=node2, OU=[cluster_name]"

Verify the keys. Successful verification will display the certificate fingerprint. Repeat this command for each certificate file.

        keytool -list -keystore node1-keystore.jks -storepass [password]

Generate a signing-request file for each node in your cluster, using each .jks file for the -keystore option. The commands below illustrate this for a two-node cluster.

        keytool -certreq -keystore node1-keystore.jks -alias node1 -file node1-cert.csr -keypass cassandra -storepass cassandra -dname "CN=node1, OU=[cluster_name]"
        keytool -certreq -keystore node2-keystore.jks -alias node2 -file node2-cert.csr -keypass cassandra -storepass cassandra -dname "CN=node2, OU=[cluster_name]"

Sign each node's certificate. Execute the following command for each node in your cluster, using the respective .csr files created earlier. For best practices, set the certificate expiration to 365 days. The commands below illustrate this for a two-node cluster.

        openssl x509 -req -CA ca-cert.cert -CAkey ca-cert.key -in node1-cert.csr -out node1-signed.cert -days 365 -CAcreateserial -passin pass:cassandra
        openssl x509 -req -CA ca-cert.cert -CAkey ca-cert.key -in node2-cert.csr -out node2-signed.cert -days 365 -CAcreateserial -passin pass:cassandra

Verify the certificates generated for each node.

        openssl verify -CAfile ca-cert.cert node1-signed.cert

Import the original certificate into the keystore for each node. The commands below illustrate this for a two-node cluster.

        keytool -importcert -keystore node1-keystore.jks -alias ca-cert -file ca-cert.cert -noprompt -keypass cassandra -storepass cassandra
        keytool -importcert -keystore node2-keystore.jks -alias ca-cert -file ca-cert.cert -noprompt -keypass cassandra -storepass cassandra

Now, import the signed certificate into the keystore for each node. The commands below illustrate this for a two-node cluster.

        keytool -importcert -keystore node1-keystore.jks -alias node1 -file node1-signed.cert -noprompt -keypass cassandra -storepass cassandra
        keytool -importcert -keystore node2-keystore.jks -alias node2 -file node2-signed.cert -noprompt -keypass cassandra -storepass cassandra

Create a Cassandra server truststore file. This file acts as a certificate authority, enabling communication for all nodes whose client certificates were signed here.

        keytool -importcert -keystore cassandra-truststore.jks -alias truststore -file ca-cert.cert -noprompt -keypass [password] -storepass [password]

## Copy Files to Each Node in The Cluster

Copy the truststore file and keystore files into Cassandra's `conf` directory on each node. Depending on your installation, the conf directory may be located at `/etc/cassandra/conf` or `/etc/cassandra`.

Note: If you encounter a "Permission denied" error when executing the following command, it indicates that the destination server user lacks permissions to access Cassandra's configuration directory.

    scp ~/.keystore/cassandra-truststore.jks username@<dest_server_public_ip>:/cassandra/config/directory/cassandra-truststore.jks
    scp ~/.keystore/[Cluster_Name].jks username@<dest_server_public_ip>:/cassandra/config/directory/[Cluster_Name]-keystore.jks

Use the `-i` option if your destination server requires a certificate for authentication.

    scp -i /local_path/to/private_key_file ~/.keystore/cassandra-truststore.jks username@<dest_server_public_ip>:/cassandra/config/directory/cassandra-truststore.jks

## Configure Encryption Settings

Edit the `cassandra.yaml` file on each node to match the following configuration. Replace text in [brackets] with the specified information.

{{< file "/etc/cassandra/conf/cassandra.yaml" yaml >}}
server_encryption_options:
    internode_encryption: all
    keystore: /etc/cassandra/conf/[keystore_file.jks]
    keystore_password: cassandra
    truststore: /etc/cassandra/conf/[truststore_file.jks]
    truststore_password: cassandra
    # More advanced defaults below:
    protocol: TLS
    algorithm: SunX509
    store_type: JKS
    cipher_suites: [TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA]
    require_client_auth: true

{{< /file >}}


Consider configuring the `internode_encryption` setting to better suit your specific environment. Below are the available values explained:

|  Property  | Property description |
|:----------:|:-------------:|
| all | All traffic between nodes is encrypted. |
| none | No traffic is encrypted. |
| dc | Only traffic between data centers is encrypted. |
| rack | Only traffic between server racks is encrypted. |

## Verify SSL Setup

Run the following commands on each server node:

Reboot Cassandra

        systemctl restart cassandra

Verify that the nodes are online and communicating.

        nodetool status

Check the log file to confirm SSL encryption status.

        grep SSL /var/log/cassandra/system.log 2>&1 | tail -1

If successful, your console output should resemble the following:

    INFO  [main] 2017-07-19 14:35:14,212 MessagingService.java:521 - Starting Encrypted Messaging Service on SSL port 7001

### Automate SSL Certificate Generation

If you have numerous Cassandra nodes requiring SSL certificates, the manual process described above can be cumbersome. Once you grasp the SSL certificate generation for Cassandra, you can streamline it using a bash script. Automate the process with this script. The remainder of this guide demonstrates how to utilize the SSL certificate generator for automating SSL certificate creation.

Navigate to a directory of your choice and fetch the script from GitHub:

        git pull https://github.com/Darkstar90/cassandra-keygen.git

Execute the script using the `-h` or `--help` option:

        bash cassandra-keygen.sh --help

The command output will display the script's capabilities and usage instructions. The script operates in 3 modes:

        a. Generate Node Certificates
        b. Import Node Certificates
        c. Generate Truststore

Here's an example of how each mode can be used in a 6-node cluster:

**Generate Node Certificates**

    bash cassandra-keygen.sh  --nodes 6 --directory /etc/cassandra/conf/.keystore --cluster Cassandra_Cluster --password cassandra --sslconfig /etc/cassandra/conf/rootCAcert.conf --keysize 4096

**Import Node Certificates**

    bash cassandra-keygen.sh -n 6 -d /etc/cassandra/conf/.keystore -p cassandra

**Generate Truststore**

    bash cassandra-keygen.sh --directory /etc/cassandra/conf/.keystore --truststore cassandra

**Do All Actions**

    bash cassandra-keygen.sh  --nodes 6 --directory /etc/cassandra/conf/.keystore --cluster Cassandra_Cluster --password cassandra --truststore cassandra --sslconfig /etc/cassandra/conf/rootCAcert.conf --keysize 4096

### Where to Go From Here

Now that your Cassandra cluster is operational with node-to-node SSL encryption, you're ready to deploy production-ready databases. You can also enable encryption for `cqlsh` when logging into each node in the cluster. For details on setting up Client-to-node encryption, refer to the external resources section.
