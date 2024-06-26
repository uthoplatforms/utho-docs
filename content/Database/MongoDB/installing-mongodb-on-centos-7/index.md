---
title: "Installing MongoDB on CentOS 7"
date: "2020-06-29"
title_meta: " GUIDE TO Install MongoDB on CentOS 7,"
description: 'This guide details the steps to install and configure MongoDB, a document-oriented NoSQL database, on your CentOS 7 system. MongoDB offers scalability and flexibility, making it a suitable choice for various modern web applications.'

keywords:  ['   CentOS 7, MongoDB, NoSQL database, installation.']
tags: ["MongoDB on CentOS 7"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/installing-mongodb-on-centos-7/']
tab: true
---
---

![](images/Installing-MongoDB-on-CentOS-7-2.png)

MongoDB is a database engine that provides access to non-relational, document-oriented databases. It is known as a NoSQL database, because it is not based on a conventional table-oriented relational database framework.

Unlike relational databases, before you add data to a database, MongoDB doesn't require a predefined schema. You can alter the schema at any time and as often as is necessary without having to setup a new database with an updated schema.

## Add MongoDB Yum Repository

Create a new file, vi /etc/yum.repos.d/mongodb-org-4.2.repo so that you can install the latest release using yum. Add the following contents to the file:

```
vi /etc/yum.repos.d/mongodb-org-4.2.repo
```

```file {title="vi /etc/yum.repos.d/mongodb-org-4.2.repo" lang="aconf"}
[mongodb-org-4.2] name=MongoDB Repository baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.2/x86_64/ gpgcheck=enabled=gpgkey=https://www.mongodb.org/static/pgp/server-4.2.asc
```

## Install MongoDB

```
sudo yum install mongodb-org
```

## Configure MongoDB

The configuration file for MongoDB is located at `/etc/mongod.conf`, and is written in YAML format.

```file {title="/etc/mongod.conf" lang="aconf"}
security: authorization: enabled
```

## Start and Stop MongoDB

Start the MongoDB service with the systemctl utility:

```
sudo systemctl start mongod
```

To restart the mongod process

```
sudo systemctl restart mongod
```

The stop command halts all running mongod processes.

```
sudo systemctl stop mongod
```

MongoDB can also be started on boot:

```
sudo systemctl enable mongod
```

## Create Database Users

1\. Open the mongo shell

```
mongo
```

2\. By default, MongoDB connects to a database called `test`. Before adding any users, create a database to store user data for authentication:

```
use admin
```

3\. Use this command to create an administrative user with the ability to create other users on any database. For better security, change the values `mongo-admin` and `password`:

```
db.createUser({user: "mongo-admin", pwd: "password", roles:[{role: "userAdminAnyDatabase", db: "admin"}]})
```

Hold these credentials for future reference in a secure spot. All the information will be displayed to the database except the password.

4\. Exit the mongo shell:

```
quit()
```

## Backup and restore MongoDB Database

We use these tools:

- mongodump
- mongorestore

Command to mongodb backup

The `mongodump` command will create a backup / dump of the MongoDB database with the name specified by the `--db [DB NAME]` argument.

The ``--out /var/backups/`date +"%Y%m%d"`` argument specifies the output directory as `/var/backups/[TODAY'S DATE]` e.g. `/var/backups/20190903`

```
sudo mongodump --db [DB NAME] --out /var/backups/date +"%Y%m%d"
```

It assumes that you have a running mongodb instance on port 27017.

### Mongodb backup when database is on remote server or port is different on localhost

```
mongodump --host <hostname>: --db <source database name> --authenticationDatabase <source database name> -u <username> -p <password>
```

### Commands to restore mongodb database

```
sudo mongorestore --db [DB NAME] /var/backups/[BACKUP FOLDER NAME]
```

Thankyou.
