---
title: "How to install MongoDB on AlmaLinux 8"
date: "2023-06-12"
title_meta: "GUIDE How to install MongoDB on Almalinux"
description: "Learn how to install MongoDB on AlmaLinux 8. Follow this guide to set up MongoDB, a popular NoSQL database, on your AlmaLinux 8 system for scalable and flexible data storage."

keywords: ['MongoDB', 'AlmaLinux 8', 'install', 'NoSQL database', 'scalable data storage']

tags: ["MongoDB"]
icon: "Alma Linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/Alma Linux/how-to-install-mongodb-on-almalinux-8/']
tab: true
---

![How to install MongoDB on AlmaLinux 8](images/How-to-install-MongoDB-on-AlmaLinux-8-1024x576.jpg)

## Introduction

In this article, you will learn how to install MongoDB on AlmaLinux 8.

MongoDB is a non-relational document database that provides support for JSON-like storage. The [MongoDB](https://en.wikipedia.org/wiki/MongoDB) database has a flexible data model that enables you to store unstructured data, and it provides full indexing support, and replication with rich and intuitive APIs.

##### Step 1: Update the system

```
# dnf update -y

```

##### Step 2: Install MongoDB

**For MongoDB to work, we need to make one source file. We'll use vi editor to make a repo file.**

```
# vi /etc/yum.repos.d/mongodb-org-4.4.repo

```

**Add following lines:**

```
[mongodb-org-4.4]name=MongoDB Repositorybaseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/gpgcheck=1enabled=1gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
```

**baseurl: This points to the URL of a directory where the repository's repodata directory, which holds the repository's information, can be found.**

**gpgcheck: Setting this directive to 1 tells the DNF package manager to allow GPG signature checking on this repository. This requires it to check whether any packages you want to install from this repository have been corrupted or tampered with.**

**gpgkey: This option gives the URL of the GPG key that should be imported to check the signatures of packages from this repository.**

**Save and exit.**

**Use the following command to install the most stable version of MongoDB:**

```
# dnf install -y mongodb-org

```

![How to install MongoDB on AlmaLinux 8](images/image-1164.png)

##### Step 3: Start and enable the services

```
# systemctl start mongod

```

```
# systemctl enable mongod

```

##### Step 4: Use MongoDB

```
# mongo

```

![install MongoDB on AlmaLinux 8](images/image-1165.png)

## Conclusion

Hopefully, now you have learned how to install MongoDB on AlmaLinux 8.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
