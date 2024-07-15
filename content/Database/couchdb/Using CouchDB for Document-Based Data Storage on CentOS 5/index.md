---
slug: Using CouchDB for Document-Based Data Storage on CentOS 5
deprecated: true
description: 'An introduction and getting started guide for CouchDB on CentOS 5 systems.'
keywords: ["couchdb", "nosql", "json", "centos"]
modified: 2024-07-08
modified_by:
  name: Utho
published: 2024-07-08
title: Use CouchDB for Document Based Data Storage on CentOS 5
relations:
    platform:
        key: couchdb-document-data-storage
        keywords:
            - distribution: CentOS 5
tags: ["centos","database","nosql"]
aUthors: ["Utho"]
---

CouchDB is a non-relational, document-based database designed to offer flexible data storage for custom application development within the "NoSQL" landscape. Written in the Erlang programming language, CouchDB utilizes an HTTP interface and JSON as its data format, facilitating seamless integration into application development.

Before proceeding with CouchDB installation, ensure you have completed our Setting Up and Securing a Compute Instance guide. If you are new to Linux server administration, you may find our guides on Linux concepts, beginner's guide, and administration basics helpful.

## Installing CouchDB

To install CouchDB and its dependencies on CentOS, you need to enable the EPEL (Extra Packages for Enterprise Linux) repository, as these packages are not available in the standard CentOS repositories. EPEL is maintained by the Fedora Project and offers up-to-date software packages not typically found in CentOS repositories. Use the following command to enable EPEL:

    rpm -Uvh http://download.fedora.redhat.com/pub/epel/5/i386/epel-release-5-4.noarch.rpm

Before installing CouchDB, ensure your package repositories and installed programs are up to date by running the following command:

    yum update

To install CouchDB and its dependencies, run the following command:

    yum install couchdb

During the installation process, you will be prompted to accept the repository key for EPEL. Once confirmed, the installation will proceed. To start CouchDB, use the init script located at `/etc/init.d/couchdb`. Issue the following commands to start CouchDB:

    /etc/init.d/couchdb start

To restart or stop CouchDB, you can use the following commands:

    /etc/init.d/couchdb restart
    /etc/init.d/couchdb stop

To ensure CouchDB starts automatically after the next system reboot, issue the following command:

    chkconfig couchdb on

Congratulations! CouchDB has been successfully installed. In most cases, you won't need to modify CouchDB's configuration file. However, various configuration options are set in the /etc/couchdb/local.ini file if you need to make adjustments in the future.

## Using CouchDB

CouchDB includes a web-based administrative interface called "Futon". By default, CouchDB is accessible only over the local interface. To securely access CouchDB or Futon from your local machine, it's recommended to create a secure SSH tunnel.

Once the SSH tunnel is established or if you have configured your Utho accordingly, you can access the CouchDB HTTP interface by making a request to `http://localhost:5984`. If you prefer to install a simple command-line HTTP client, you may use curl to test your CouchDB instance with the following command:

    curl http://localhost:5984

In response, CouchDB will return the following:

{{< output >}}
{"couchdb":"Welcome","version":"0.10.2"}
{{< /output >}}

Once the SSH tunnel is established, you can access the Futon interface by visiting http://localhost:5984/_utils/ in a web browser on your local system.

Additionally, CouchDB includes an embedded JavaScript interpreter. You can interact with CouchDB directly using the couchjs command in your terminal. Use it with a command similar to the following format:

    couchjs duck-team-check.js

Where `duck-team-check.js` is a JavaScript file containing code for the CouchDB interpreter.