---
slug: Using couchdb for document based data storage on ubuntu 10.10 maverick
deprecated: true
description: 'An introduction and getting started guide for CouchDB on Ubuntu 10.10 (Maverick) systems.'
keywords: ["couchdb", "nosql", "json", "ubuntu"]
modified: 2024-07-09
modified_by:
  name: Utho
published: 2024-07-09
title: 'Use CouchDB for Document Based Data Storage on Ubuntu 10.10 (Maverick)'
relations:
    platform:
        key: couchdb-document-data-storage
        keywords:
            - distribution: Ubuntu 10.10
tags: ["ubuntu","database","nosql"]
authors: ["Utho"]
---

CouchDB is a non-relational, document-based database. It belongs to the 'NoSQL' category, focusing on providing a flexible data storage solution for custom application development. Written in the Erlang programming language, CouchDB utilizes an innovative concurrency model. Instead of an SQL interface, it employs HTTP and JSON formats, making integration into applications straightforward.

This guide assumes you have completed the Setting Up and Securing a Compute Instance before installing CouchDB. For those new to Linux server administration, we recommend reviewing our guides on Linux concepts, beginner's tips, and administration basics.

## Installing CouchDB

After saving this file, execute the following commands to update your system's package database and ensure that you have the latest software installed:

    apt-get update
    apt-get upgrade

### Install CouchDB Software

To ensure your system is up to date and install CouchDB, execute the following command sequence:

    apt-get install couchdb

Once installed, CouchDB will automatically start. Use the "init script" at /etc/init.d/couchdb to control CouchDB with these commands: start, restart, and stop.

    /etc/init.d/couchdb start
    /etc/init.d/couchdb restart
    /etc/init.d/couchdb stop

Congratulations! In most situations, modifying CouchDB's configuration file at `/etc/couchdb/local.ini` won't be necessary. However, if adjustments are needed, various settings can be configured there.

## Using CouchDB

Your primary interaction with CouchDB will be through its HTTP and JSON interfaces. CouchDB includes a web-based administrative tool called "Futon". By default, CouchDB is only accessible locally. To securely access CouchDB or Futon from your local machine and prevent sending data in plain text, you should set up a secure SSH tunnel.

Once the SSH tunnel is in place or you have configured your Utho, you can access the CouchDB HTTP interface by making a request for `http://localhost:5985`. For a simple command-line HTTP client you may use `curl`. Issue the following command:

    curl http://localhost:5985

In response, CouchDB will return the following:

    {"couchdb":"Welcome","version":"1.0.1"}

With the SSH tunnel active, you can access the Futon interface by visiting the URL `http://localhost:5985/_utils/` in a web browser on your local system.

Additionally, CouchDB provides an embedded JavaScript interpreter if you would like to interact with CouchDB directly. Access this interpreter with the `couchjs` command in your terminal by issuing a command in the following form:

    couchjs duck-team-check.js

Where, `duck-team-check.js` is a file containing JavaScript code for the CouchDB interpreter.



