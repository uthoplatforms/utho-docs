---
slug: Using couchdb for document based data storage on ubuntu 12.04
deprecated: true
description: 'CouchDB is a non-relational document based database, also referred to as a NoSQL database. This guide instructs you on installing it on Ubuntu 12.04 "Precise Pangolin".'
keywords: ["couchdb", "nosql", "json", "ubuntu", "futon"]
modified: 2024-07-09
modified_by:
  name: Utho
published: 2024-07-09
title: 'Use CouchDB for Document-Based Data Storage on Ubuntu 12.04'
relations:
    platform:
        key: couchdb-document-data-storage
        keywords:
            - distribution: Ubuntu 12.04
tags: ["ubuntu","database","nosql"]
authors: ["Utho"]
---

CouchDB is a non-relational, document-based database system. As part of the 'NoSQL' movement, CouchDB aims to offer a more flexible approach to storing data for custom application development. It is implemented in the Erlang programming language, known for its robust concurrency model. Unlike traditional SQL databases, CouchDB utilizes an HTTP interface and JSON as its data format, making it highly compatible and easy to integrate into various application environments.

Before installing CouchDB, it is assumed that you have already completed our guide on 'Setting Up and Securing a Compute Instance'. If you are new to Linux server administration, you may find our introductory guides on Linux concepts, beginners' tips, and administration basics helpful."

## Install CouchDB

"To ensure your system is running the latest software, refresh its package database with the following commands:

    apt-get update
    apt-get upgrade --show-upgraded

To install CouchDB and its dependencies, use the command:

    apt-get install couchdb

Once installed, CouchDB will automatically start. Manage CouchDB using the init script located at `/etc/init.d/couchdb`. Use these commands to start, restart, or stop CouchDB:

    /etc/init.d/couchdb start
    /etc/init.d/couchdb restart
    /etc/init.d/couchdb stop

Congratulations! For most scenarios, you won't need to adjust CouchDB's configuration. However, if necessary, modify settings in the `/etc/couchdb/local.ini` file."

## Use CouchDB

"Most of your interaction with CouchDB will be through its HTTP and JSON interface. CouchDB includes a web-based administrative tool called "Futon". By default, CouchDB is accessible only locally. To securely access CouchDB or Futon from your local machine, you should create a secure SSH tunnel.

Once the SSH tunnel is established or your Utho is configured, access the CouchDB HTTP interface by navigating to `http://localhost:5985`. For simple command-line HTTP requests, you can install curl with the following command:

    apt-get install curl

Now, issue the following command:

    curl http://localhost:5985

In response, CouchDB will return the following:

    {"couchdb":"Welcome","version":"0.10.0"}

With the SSH tunnel established, you can access the Futon interface by opening the URL `http://localhost:5985/_utils/` in a web browser on your local system.

Moreover, CouchDB includes an embedded JavaScript interpreter for direct interaction. Use the couchjs command in your terminal with the following syntax:

    couchjs duck-team-check.js

Here, `duck-team-check.js` is the filename that contains JavaScript code for the CouchDB interpreter.






