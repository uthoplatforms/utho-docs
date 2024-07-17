---
slug: Use couchdb for document based data storage on fedora 14
deprecated: true
description: 'An introduction and getting started guide for CouchDB on Fedora 14 systems.'
keywords: ["couchdb", "nosql", "json", "centos"]
modified: 2024-07-09
modified_by:
  name: Utho
published: 2024-07-09
title: Use CouchDB for Document Based Data Storage on Fedora 14
relations:
    platform:
        key: couchdb-document-data-storage
        keywords:
            - distribution: Fedora 14
tags: ["fedora","database","nosql"]
authors: ["Utho"]
---

CouchDB is a non-relational, document-based database. Like other NoSQL databases, CouchDB offers a flexible data storage system suitable for custom application development. It is written in the Erlang programming language, which supports an innovative concurrency model. Instead of using an SQL interface, CouchDB uses an HTTP interface and JSON format, making it easy to integrate into application development.

## Installing CouchDB

Before installing CouchDB, ensure your package repositories and installed programs are up to date by issuing the following command:

    yum update

To install CouchDB and all its dependencies, use the following command:

    yum install couchdb

To start CouchDB, utilize the init script located at `/etc/init.d/couchdb`. Issue the following commands to start CouchDB:

    /etc/init.d/couchdb start

If you need to restart or stop CouchDB later, you can use these commands:

    /etc/init.d/couchdb restart
    /etc/init.d/couchdb stop

To ensure CouchDB starts automatically after a system reboot, issue the following command:

    chkconfig couchdb on

Congratulations! In most cases, you will not need to modify CouchDB's configuration file. However, if needed, you can adjust settings in the `/etc/couchdb/local.ini` file.

## Using CouchDB

Most of your interaction with CouchDB will occur through its HTTP and JSON interface. CouchDB includes a web-based administrative interface called "Futon." By default, CouchDB is only accessible over the local interface, so you will want to create a secure SSH tunnel to access CouchDB or Futon from your local machine to avoid sending data in the clear.

Once the SSH tunnel is set up or you have configured your Utho, you can access the CouchDB HTTP interface by making a request to `http://localhost:5985`. For a simple command-line HTTP client, consider installing curl. You can test your CouchDB instance by issuing the following command:

    curl http://localhost:5985

In response, CouchDB will return the following:

    {"couchdb":"Welcome","version":"1.0.1"}


With the SSH tunnel active, you can access the Futon interface by visiting `http://localhost:5985/_utils/` in a web browser on your local system.

Additionally, CouchDB provides an embedded JavaScript interpreter for direct interaction. You can access this interpreter using the couchjs command in your terminal by issuing a command in the following form:

    couchjs duck-team-check.js

Where `duck-team-check.js` is a file containing JavaScript code for the CouchDB interpreter.

