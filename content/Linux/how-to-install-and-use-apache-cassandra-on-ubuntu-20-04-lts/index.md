---
title: "How to Install and Use Apache Cassandra on Ubuntu 20.04 LTS"
date: "2022-12-09"
---

Description

I'll walk you through installing and configuring [Apache](https://utho.com/docs/tutorial/how-to-protect-your-web-sites-by-using-username-and-password-in-apache-on-ubuntu/) Cassandra on Ubuntu 20.04 LTS (Focal Fossa). [Cassandra](https://cassandra.apache.org/_/index.html) is the best NoSQL database to utilise if you need a database that is both efficient and speedy. Cassandra is a NoSQL database management system that is free and open-source, distributed, wide-column store, decentralised, elastically scalable, and highly available. It is designed to handle large amounts of data across many commodity servers while providing high availability with no single point of failure. It is widely utilised to power cloud applications in a variety of sectors.

## Prerequisites

a) You must have an Ubuntu 20.04 LTS (Focal Fossa) server running.

b) To run privileged commands, you must have sudo or root access.

c) The apt, apt-get, curl, and tee utilities should be available on your server.

## Update Your Server

```
apt update
```
## Install Java

If Java is not already installed, the next step is to install it. You may verify this by executing the java version command. If it is not installed, you will get results similar to the one below, prompting you to use any of the following instructions to install the Java platform. We'll install OpenJDK from the Ubuntu Repository because we'll need JDK.

```
java --version
```
Use the apt install openjdk-8-jdk command to install OpenJDK

```
apt install openjdk-8-jdk
```
If you check the Java version again, it should now appear like this. This validates that Java is now correctly installed.

```
java --version
```
## Install Apache Cassandra

We can now proceed with the installation of Cassandra DB; however, because Apache Cassandra is not available from the default repository, we will need to add the repository first, from which we can directly download and install the packages.

**Install apt-transport-https**

But first, we must install the apt-transport-https package using the apt install apt-transport-https command.

```
apt install apt-transport-https
```
## GPG Key Import

You may receive the steps to add the GPG key from the Cassandra Download Page. This is necessary to ensure the package's integrity.

```
wget -q -O - https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
```
## Add Repo

```
echo "deb http://www.apache.org/dist/cassandra/debian 40x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
```
## Update Package

```
apt update
```
## Install Cassandra DB

Now we can use apt install Cassandra to install the Cassandra package and all of its dependencies.

```
apt install cassandra
```
## Cassandra service check

After a successful installation, use the systemctl status cassandra command to check the service's status.

```
systemctl status cassandra
```
## Nodetool Status: Check

If you are operating a single node cluster, you should additionally verify the node state by running nodetool status.

```
nodetool status
```
## Login to Cassandra

Once Cassandra is up and running, use the cqlsh command to connect to the database.

```
cqlsh
```
## Configure Cassandra

You may have observed that the Cassandra cluster name is set to Test Cluster by default. If you want to modify this name, first perform the update query below and then exit the prompt by typing exit.

```
update system.local SET cluster_name = 'CyberITHub Cluster' WHERE KEY = 'local';
```
```
exit
```
Then, using an editor such as vi, change the cluster name from Test to CyberITHub in the /etc/cassandra/cassandra.yaml configuration file, When you're finished, use Ctrl+X to save and close the file.

```
vi /etc/cassandra/cassandra.yaml
```
After modifying the configuration, use the systemctl restart cassandra command to restart the service. Use the systemctl status cassandra command to check the status.

```
systemctl restart cassandra
```
```
systemctl status cassandra
```
Wait a few minutes for the services to start up before testing the cluster name with cqlsh in the terminal again. The cluster name has been changed to CyberITHub.

**NOTE:** Please keep in mind that if you get the Connection Problem: Unable to connect to any servers error after running the cqlsh command, you should verify the node status with the nodetool status command. Only use the cqlsh command to login to Cassandra once the node is up and running.

Conclusion: I'll walk you through installing and configuring Apache Cassandra on Ubuntu 20.04 LTS (Focal Fossa). Cassandra is the best NoSQL database to utilise if you need a database that is both efficient and speedy. Cassandra is a NoSQL database management system that is free and open-source, distributed, wide-column store, decentralised, elastically scalable, and highly available. It is designed to handle large amounts of data across many commodity servers while providing high availability with no single point of failure. It is widely utilised to power cloud applications in a variety of sectors.
