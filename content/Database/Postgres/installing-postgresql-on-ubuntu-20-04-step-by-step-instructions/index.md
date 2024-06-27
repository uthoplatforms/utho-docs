---
title: "Installing PostgreSQL on Ubuntu 20.04: Step-by-Step Instructions"
date: "2022-09-16"
title_meta: "Guide on Installation of PostgreSQL on Ubuntu 20.04"
description: "Follow step-by-step instructions for installing PostgreSQL on Ubuntu 20.04 in this comprehensive guide. Learn how to set up PostgreSQL, a powerful relational database system, on your Ubuntu 20.04 server with detailed explanations and best practices.
"
keywords: ["install PostgreSQL Ubuntu 20.04", "PostgreSQL installation guide Ubuntu 20.04", "setup PostgreSQL Ubuntu 20.04", "PostgreSQL setup tutorial Ubuntu 20.04", "Ubuntu 20.04 PostgreSQL installation steps", "PostgreSQL server setup Ubuntu 20.04", "Ubuntu 20.04 PostgreSQL install guide", "configure PostgreSQL Ubuntu 20.04"]

tags: ["PostgreSQL", "ubuntu"]
icon: "postgres"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Database/Postgres/installing-postgresql-on-ubuntu-20-04-step-by-step-instructions/']
tab: true
---

![](images/How-To-Install-PostgreSQL-on-Ubuntu-20.04-2-1024x576.png)

**Introduction**

PostgreSQL implements the SQL querying language. Standards-compliant, it has reliable transactions and concurrency without read locks.

This guide shows how to install PostgreSQL and create a new user and database on an Ubuntu 20.04 server. See How to Install and Use PostgreSQL on Ubuntu 20.04 for a more detailed instruction.

**Prerequisites**

You will need one Ubuntu 20.04 server that has been configured using our Initial Server Setup for Ubuntu 20.04 guide if you want to follow along with this tutorial. Your server ought to have a basic firewall and a non-root user with sudo capabilities after finishing this preparatory instruction.

## **Step 1 — Installing PostgreSQL**

In order to install PostgreSQL, you must first refresh the local package index on your server.

```
#sudo apt update
```

Install Postgres and -contrib, which adds utilities and functionality:

```
#sudo apt install postgresql postgresql-contrib
```

Check to see that the service has been initiated:

```
#sudo systemctl start postgresql.service
```

## **Step.2 Utilizing PostgreSQL Roles and Databases  
**  
When it comes to handling authentication and authorisation, Postgres's default settings make use of a concept known as "roles." These are comparable to the standard users and groups used by the Unix operating system in several respects.

During the installation process, Postgres is configured to make use of the ident authentication method. This means that it links each of the Postgres roles to a corresponding Unix or Linux system account. If there is a role within Postgres with the same name as a Unix or Linux username, the user will have the ability to sign in as that role.

During the course of the installation process, a user account with the name postgres was established. This account is linked to the default Postgres role. This account can be used in a number different ways in order to gain access to Postgres. Switching to the postgres account on your server can be done in a number of ways, one of which is to execute the following command:

```
#sudo -i -u postgres
```

After that, you can launch the Postgres prompt by typing:

```
#psql
```

With the PostgreSQL prompt now open, you can immediately start interacting with the database management system.

Run these commands to leave the PostgreSQL prompt:

```
#q
```

After doing so, you will be brought back to the command prompt for postgres on Linux. Execute the exit command to return to your regular user account on the system:

```
#exit
```

You may also access to the Postgres prompt by executing the psql command with the sudo prefix while logged in as the postgres account:

```
#sudo -u postgres psql
```

By using this, you can log into Postgres without using a bash shell as a middleman.

Once more, you can end the interactive Postgres session by executing the commands below:

```
#q
```
