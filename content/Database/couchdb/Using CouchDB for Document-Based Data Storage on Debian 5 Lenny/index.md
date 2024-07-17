---
slug: "Using CouchDB for Document-Based Data Storage on Debian 5 Lenny"
deprecated: true
description: 'An introduction and getting started guide for CouchDB on Debian 5 (Lenny) systems.'
keywords: ["couchdb", "nosql", "json", "debian"]
modified: 2024-07-08
modified_by:
  name: Utho
published: 2024-07-08
title: 'Use CouchDB for Document Based Data Storage on Debian 5 (Lenny)'
relations:
    platform:
        key: couchdb-document-data-storage
        keywords:
            - distribution: Debian 5
tags: ["debian","database","nosql"]
authors: ["Utho"]
---

CouchDB is a non-relational, document-based database that falls within the "NoSQL" category. It aims to offer a flexible data storage solution for custom application development. Written in the Erlang programming language, CouchDB leverages its innovative concurrency model. Unlike traditional SQL databases, CouchDB uses an HTTP interface and JSON as its data format, ensuring seamless integration into application development workflows.

Before proceeding with CouchDB installation, it is assumed that you have already completed our Setting Up and Securing a Compute Instance guide. If you are new to Linux server administration, we recommend reviewing our guides on Linux concepts, beginner's guide, and administration basics.

## Installing CouchDB

Before proceeding with the installation of CouchDB, ensure your package repositories and installed programs are up to date by issuing the following commands:

    apt-get update
    apt-get upgrade --show-upgraded

To install CouchDB and all its dependencies, use the following command:

    apt-get install couchdb

Once CouchDB is fully installed, it will start automatically. You can manage CouchDB using the init script located at `/etc/init.d/couchdb`. Use the following commands to start, restart, and stop CouchDB:

    /etc/init.d/couchdb start
    /etc/init.d/couchdb restart
    /etc/init.d/couchdb stop

Congratulations! In most scenarios, you won't need to modify CouchDB's configuration file. However, if you need to adjust any settings, you can find various options configured in the /etc/couchdb/local.ini file.

## Using CouchDB

Most of your interaction with CouchDB will happen through its HTTP and JSON interface. CouchDB includes a web-based administrative interface called "Futon". By default, CouchDB is only accessible over the local interface. To securely access CouchDB or Futon from your local machine, it's advisable to create a secure SSH tunnel.

Once the SSH tunnel is established or if you have configured your environment accordingly, you can access the CouchDB HTTP interface by making a request to `http://localhost:5984`. For those preferring a simple command-line HTTP client, consider installing curl with the following command:

    apt-get install curl

Now issue the following command:

    curl http://localhost:5984

In response, CouchDB will return the following:

    {"couchdb":"Welcome","version":"0.8.0-incubating"}

With the SSH tunnel established, you can access the Futon interface by visiting `http://localhost:5984/_utils/` in a web browser on your local system.

Additionally, CouchDB includes an embedded JavaScript interpreter. You can interact directly with CouchDB using the couchjs command in your terminal, as shown in the following command format:

    couchjs duck-team-check.js

Where `duck-team-check.js` represents a JavaScript file containing code for the CouchDB interpreter.



