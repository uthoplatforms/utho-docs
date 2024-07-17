---
slug: "Using CouchDB for Document-Based Data Storage on Fedora 13"
deprecated: true
description: 'An introduction and getting started guide for CouchDB on Fedora 13 systems.'
keywords: ["couchdb", "nosql", "json", "centos"]
modified: 2024-07-08
modified_by:
  name: Utho
published: 2024-07-08
title: Use CouchDB for Document Based Data Storage on Fedora 13
relations:
    platform:
        key: couchdb-document-data-storage
        keywords:
            - distribution: Fedora 13
tags: ["fedora","database","nosql"]
authors: ["Utho"]
---

CouchDB is a non-relational, document-based database that belongs to the "NoSQL" category. It aims to offer a more flexible data storage system for custom application development. CouchDB is developed in the Erlang programming language, known for its robust concurrency model. Unlike traditional SQL databases, CouchDB uses an HTTP interface and JSON as its data format, which simplifies integration into application development workflows.

Before proceeding with the installation of CouchDB, ensure you have followed our Setting Up and Securing a Compute Instance guide. If you're new to Linux server administration, consider reviewing our introduction to Linux concepts guide, beginner's guide, and administration basics guide.

## Installing CouchDB

Before proceeding with the installation of CouchDB, ensure your package repositories and installed programs are up to date by running the following command:

    yum update

To install CouchDB and all its dependencies, execute the following command:

    yum install couchdb

To start CouchDB, use the "init script" located at `/etc/init.d/couchdb`. Issue the following commands to start CouchDB:

    /etc/init.d/couchdb start

Later, if you need to restart or stop CouchDB you can use the following commands to accomplish these functions:

    /etc/init.d/couchdb restart
    /etc/init.d/couchdb stop

To ensure CouchDB starts automatically after a system reboot, issue the following command:

    chkconfig couchdb on

Congratulations! In most scenarios, you won't need to adjust CouchDB's configuration file. However, if you need to modify any settings, you can find the options in the `/etc/couchdb/local.ini` file.

## Using CouchDB

Most of your interaction with CouchDB will happen through its HTTP and JSON interface. CouchDB includes a web-based administrative interface called "Futon". Since CouchDB is initially accessible only over the local interface, it's advisable to set up a secure SSH tunnel to access CouchDB or Futon from your local machine, ensuring data is encrypted.

Once the SSH tunnel is established or your server configured, you can access the CouchDB HTTP interface by visiting http://localhost:5984. For simple command-line HTTP interactions, consider installing curl. You can test your CouchDB instance with the following command:

    curl http://localhost:5984

In response, CouchDB will return the following:

    {"couchdb":"Welcome","version":"0.10.2"}

With the SSH tunnel active, you can access the Futon interface by opening `http://localhost:5984/_utils/` in a web browser on your local system.

Additionally, CouchDB provides an embedded JavaScript interpreter for direct interaction. You can access this interpreter using the couchjs command in your terminal with the following syntax:

    couchjs duck-team-check.js

Where `duck-team-check.js` is a file containing JavaScript code intended for use with the CouchDB interpreter.


