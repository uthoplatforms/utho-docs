---
slug: "How to Install CouchDB 2.0 on Ubuntu"
description: 'This guide will show you how to install the noteworthy NoSQL database utility known for its scalability and fault tolerance, CouchDB 2.0 on Ubuntu 20.04.'
keywords: ['couchdb','nosql','database','deploy on ubuntu 20.04']
tags: ['couchdb','ubuntu','nosql','apache']
published: 2024-07-08
modified_by:
  name: Pawan Kumar
title: "Installing CouchDB 2.0 on Ubuntu 20.04"
title_meta: "How to Install CouchDB 2.0 on Ubuntu 20.04"
author: ["Pawan Kumar"]
---

*CouchDB* is a NoSQL database designed for scalability and ease of use. It is programmed in Erlang, which provides a highly scalable concurrency model and fault tolerance. These features ensure that CouchDB can handle varying request volumes and maintain performance with minimal interruptions.

CouchDB utilizes HTTP APIs and JSON documents, making it intuitive and easy to integrate with web and mobile applications. Its flexibility allows it to meet a wide range of requirements.

This guide will show you how to install CouchDB on Ubuntu 20.04. At the end of this guide, you will find a link to a follow-up guide on getting started with CouchDB and understanding its core concepts.

## Before You Begin

If you haven't already, create a Utho account and set up a Compute Instance. Refer to our guides on Getting Started with Utho and Creating a Compute Instance.

Follow our guide on Setting Up and Securing a Compute Instance to update your system. Additionally, you may want to set the timezone, configure your hostname, create a limited user account, and harden SSH access.

{{< note >}}
This guide is intended for a non-root user. Commands requiring elevated privileges are prefixed with sudo. If you're not familiar with the sudo command, refer to the Linux Users and Groups guide.
{{< /note >}}

## Set Up the Apache CouchDB Repository

Install the prerequisites for adding the Apache CouchDB repository by executing the following commands:

        sudo apt update && sudo apt install -y curl apt-transport-https gnupg

        curl https://couchdb.apache.org/repo/keys.asc | gpg --dearmor | sudo tee /usr/share/keyrings/couchdb-archive-keyring.gpg >/dev/null 2>&1

        source /etc/os-release

Add the CouchDB repository to the apt repository list.

        echo "deb [signed-by=/usr/share/keyrings/couchdb-archive-keyring.gpg] https://apache.jfrog.io/artifactory/couchdb-deb/ ${VERSION_CODENAME} main" | sudo tee /etc/apt/sources.list.d/couchdb.list >/dev/null

Install the CouchDB repository key.

        sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 8756C4F765C9AC3CB6B85D62379CE192D401AB61

## Install CouchDB

{{< note >}}
The following steps outline the installation of a standalone CouchDB server. If you plan to use CouchDB in a cluster, select clustered instead of standalone and specify 0.0.0.0 as the interface bind-address in the subsequent steps.

Refer to CouchDB's Cluster Set Up guide for additional steps required to set up a CouchDB cluster after completing the installation.
{{< /note >}}

1. Update the package manager.

        sudo apt update

1. Install CouchDB.

        sudo apt install -y couchdb

Select standalone when prompted to choose a configuration type.

Enter 127.0.0.1 as the interface bind address, using the default value.

Starting from CouchDB 3.0.0, CouchDB requires configuring an administrator user during installation. When prompted, create an administrator user by entering a password and confirming it on the subsequent screen.

1. You can verify that CouchDB is installed by running the following command.
   - Replace `admin` and `password` with the username and password, respectively, for a valid CouchDB user.
   - To use the administrator user you created during the installation process, enter the username, which is `admin` by default, and the password you set up.

         curl admin:password@127.0.0.1:5984

## Get Started with CouchDB

You have now successfully installed CouchDB! To get started, head over to the guide for [Using CouchDB on Ubuntu 20.04](/docs/guides/use-couchdb-2-0-on-ubuntu-20-04/).
