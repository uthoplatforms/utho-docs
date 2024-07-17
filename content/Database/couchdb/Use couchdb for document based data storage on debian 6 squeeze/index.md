---
slug: Use CouchDB for Document-Based Data Storage on Debian 6 (Squeeze)
deprecated: true
description: 'An introduction and getting started guide for CouchDB on Debian 6 (Squeeze) systems'
keywords: ["couchdb", "nosql", "json", "debian"]
modified: 2024-07-08
modified_by:
  name: Utho
published: 2024-07-08
title: 'Use CouchDB for Document Based Data Storage on Debian 6'
relations:
    platform:
        key: couchdb-document-data-storage
        keywords:
            - distribution: Debian 6
tags: ["debian","database","nosql"]
authors: ["Utho"]
---

CouchDB is a non-relational, document-based database that aims to offer a flexible data storage solution for custom application development, aligning with other innovations in the NoSQL field. Built using the Erlang programming language, CouchDB leverages an advanced concurrency model. It eschews an SQL interface in favor of an HTTP interface and JSON data format, facilitating straightforward integration into application development workflows.

## Installing CouchDB

Before proceeding with the installation of CouchDB, ensure your package repositories and installed programs are up to date by issuing the following commands:

    apt-get update
    apt-get upgrade --show-upgraded

To install CouchDB and all of its dependencies, run the following command:

    apt-get install couchdb

CouchDB will start automatically once the installation is complete. You can manage CouchDB using the "init script" located at /etc/init.d/couchdb. Use the following commands to start, restart, and stop CouchDB:

    /etc/init.d/couchdb start
    /etc/init.d/couchdb restart
    /etc/init.d/couchdb stop

Congratulations! In most cases, you won't need to modify CouchDB's configuration file. However, if necessary, you can adjust settings in the `/etc/couchdb/local.ini` file.

## Using CouchDB

Most of your interaction with CouchDB will happen through its HTTP and JSON interface. CouchDB includes a web-based administrative tool called "Futon". Since CouchDB is typically only accessible locally by default, it's recommended to create a secure SSH tunnel to access CouchDB or Futon from your local machine to ensure data security.

Once the SSH tunnel is established or your Utho is configured, you can access the CouchDB HTTP interface by entering the following command:

    curl http://localhost:5985

In response, CouchDB will return the following:

    {"couchdb":"Welcome","version":"0.11.0"}

With the SSH tunnel established, you can access the Futon interface by opening `http://localhost:5985/_utils/` in a web browser on your local system.

Additionally, if you wish to interact directly with CouchDB, you can use the embedded JavaScript interpreter provided by CouchDB. Access it using the couchjs command in your terminal with the following format:

    couchjs duck-team-check.js

Where `duck-team-check.js` represents a file containing JavaScript code intended for use with the CouchDB interpreter.






