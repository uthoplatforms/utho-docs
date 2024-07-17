---
slug: Use couchdb for document based data storage on ubuntu 9.10
deprecated: true
description: 'An introduction and getting started guide for CouchDB on Ubuntu 9.10 (Karmic) systems.'
keywords: ["couchdb", "nosql", "json", "ubuntu"]
modified: 2024-07-09
modified_by:
  name: Utho
published: 2024-07-09
title: 'Use CouchDB for Document Based Data Storage on Ubuntu 9.10 (Karmic)'
relations:
    platform:
        key: couchdb-document-data-storage
        keywords:
            - distribution: Ubuntu 9.10
tags: ["ubuntu","database","nosql"]
authors: ["Utho"]
---

CouchDB is a non-relational, document-based database designed to offer a more flexible data storage solution for custom application development. Written in the Erlang programming language, CouchDB leverages an innovative concurrency model. Instead of an SQL interface, it utilizes an HTTP interface and JSON for easy integration in application development.

Before installing CouchDB, ensure you have followed our Setting Up and Securing a Compute Instance guide. If you're new to Linux server administration, you might find our Introduction to Linux Concepts, Beginner's Guide, and Linux System Administration Basics guides helpful.

## Installing CouchDB

### Enable Universe Repositories

Edit your `/etc/apt/sources.list` file to enable the "universe" repositories by removing the hash symbol (#) in front of the universe lines. The file should resemble the following example:

{{< file "/etc/apt/sources.list" >}}
## main & restricted repositories
deb http://us.archive.ubuntu.com/ubuntu/ karmic main restricted
deb-src http://us.archive.ubuntu.com/ubuntu/ karmic main restricted

deb http://security.ubuntu.com/ubuntu karmic-security main restricted
deb-src http://security.ubuntu.com/ubuntu karmic-security main restricted

## universe repositories
deb http://us.archive.ubuntu.com/ubuntu/ karmic universe
deb-src http://us.archive.ubuntu.com/ubuntu/ karmic universe
deb http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe
deb-src http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe

deb http://security.ubuntu.com/ubuntu karmic-security universe
deb-src http://security.ubuntu.com/ubuntu karmic-security universe

{{< /file >}}

Once you have saved this file, issue the following commands to refresh your system's package database and ensure that you're running the most up-to-date software:

    apt-get update
    apt-get upgrade --show-upgraded

### Install CouchDB Software

To install CouchDB and all of its dependencies, issue the following command:

    apt-get install couchdb

CouchDB will start as soon as the application is fully installed. You can use the "init script" located at `/etc/init.d/couchdb` to control CouchDB. Issue the following commands to start, restart, and stop CouchDB:

    /etc/init.d/couchdb start
    /etc/init.d/couchdb restart
    /etc/init.d/couchdb stop

Congratulations! In most cases, you will not need to modify CouchDB's configuration file. However, should you need to modify any of its settings, a number of options are set in the `/etc/couchdb/local.ini` file.

## Using CouchDB

Most of your interaction with CouchDB will occur via its HTTP and JSON interface. CouchDB includes a web-based administrative interface known as "Futon". By default, CouchDB is accessible only over the local interface, so to securely access CouchDB or Futon from your local machine, you should create a secure SSH tunnel.

Once the SSH tunnel is established or your Utho is configured, you can access the CouchDB HTTP interface by making a request to `http://localhost:5985`. For a simple command-line HTTP client, you can install curl using the following command:

    apt-get install curl

Now issue the following command:

    curl http://localhost:5985

In response, CouchDB will return the following:

    {"couchdb":"Welcome","version":"0.10.0"}

With the SSH tunnel active, you can access the Futon interface by visiting the URL `http://localhost:5985/_utils/` in a web browser on your local system.

Additionally, if you want to interact directly with CouchDB, you can use its embedded JavaScript interpreter. Access this interpreter with the couchjs command in your terminal by issuing a command in the following form:

    couchjs duck-team-check.js

Where `duck-team-check.js` is a file containing JavaScript code for the CouchDB interpreter.



