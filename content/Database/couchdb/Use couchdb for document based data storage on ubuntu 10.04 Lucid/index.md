---
slug: Use couchdb for document based data storage on ubuntu 10.04 Lucid
deprecated: true
description: 'An introduction and getting started guide for CouchDB on Ubuntu 10.04 (Lucid) systems.'
keywords: ["couchdb", "nosql", "json", "ubuntu"]
license: '[CC BY-ND 4.0](https://creativecommons.org/licenses/by-nd/4.0)'
aliases: ['/databases/couchdb/ubuntu-10-04-lucid/','/databases/couchdb/use-couchdb-for-document-based-data-storage-on-ubuntu-10-04-lucid/']
modified: 2013-09-24
modified_by:
  name: Utho
published: 2010-05-03
title: 'Use CouchDB for Document Based Data Storage on Ubuntu 10.04 (Lucid)'
relations:
    platform:
        key: couchdb-document-data-storage
        keywords:
            - distribution: Ubuntu 10.04
tags: ["ubuntu","database","nosql"]
authors: ["Utho"]
---

CouchDB is a non-relational, document-based database designed to offer a flexible data storage solution for custom application development, aligning with the principles of the 'NoSQL' movement. Written in the Erlang programming language, CouchDB leverages an advanced concurrency model. Instead of employing an SQL interface, it utilizes HTTP and JSON formats, facilitating seamless integration into application development workflows.

Before installing CouchDB, please ensure you have completed our Setting Up and Securing a Compute Instance. If you are new to Linux server administration, we recommend reviewing our guides on Linux concepts, beginner's tips, and administration basics.

## Installing CouchDB

To ensure your system's package database is updated with the latest software, execute the following commands:

    apt-get update
    apt-get upgrade --show-upgraded

To install CouchDB and its dependencies, use the following command:

    apt-get install couchdb

Once installed, CouchDB will automatically start. You can manage CouchDB using the "init script" located at `/etc/init.d/couchdb`. Use these commands to start, restart, and stop CouchDB:

    /etc/init.d/couchdb start
    /etc/init.d/couchdb restart
    /etc/init.d/couchdb stop

Congratulations! In most scenarios, modifying CouchDB's configuration file at `/etc/couchdb/local.ini` won't be necessary. However, if adjustments are needed, various settings can be configured there.

## Using CouchDB

Most of your interaction with CouchDB will be through its HTTP and JSON interfaces. CouchDB includes a web-based administrative tool called "Futon". By default, CouchDB is accessible only via the local interface. To securely access CouchDB or Futon from your local machine and prevent sending data without encryption, set up a secure SSH tunnel.

Once the SSH tunnel is established or your Utho is configured, access the CouchDB HTTP interface by navigating to http://localhost:5985. For performing simple HTTP requests from the command line, consider installing curl using the following command:

    apt-get install curl

Now issue the following command:

    curl http://localhost:5985

In response, CouchDB will return the following:

    {"couchdb":"Welcome","version":"0.10.0"}

With the SSH tunnel established, you can access the Futon interface by opening the URL `http://localhost:5985/_utils/` in a web browser on your local system.

Moreover, CouchDB includes an embedded JavaScript interpreter for direct interaction. Use the couchjs command in your terminal with the following syntax:

    couchjs duck-team-check.js

Where, `duck-team-check.js` is a file containing JavaScript code for the CouchDB interpreter.




