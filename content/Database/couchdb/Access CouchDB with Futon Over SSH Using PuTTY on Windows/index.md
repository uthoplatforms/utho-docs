---
slug: "Access CouchDB with Futon Over SSH Using PuTTY on Windows"
description: 'This tutorial will teach you how to access your CouchDB database remotely by creating an SSH tunnel with PuTTY.'
keywords: ["futon", " couchdb", " apache", " ssh", " putty", " windows", " os x", " osx"]
modified: 2024-07-08
modified_by:
  name: Utho
published: 2024-07-08
title: Access Futon Over SSH to Administer CouchDB
tags: ["database"]
authors: ["Utho"]
---

Futon is a web-based administrative interface for Apache CouchDB. By using SSH, you can securely connect to your Utho's CouchDB server and access Futon through your web browser. This guide assumes that CouchDB is already running on your Utho.

## Establish an SSH connection

**SSH with Windows Using PuTTY**

If you need help setting up PuTTY, refer to our guide on using it and verifying your Utho's SSH key fingerprint.

To set up the SSH tunnel:

- In PuTTY's configuration window, go to the **Connection** category.
- Go to **SSH**, then **Tunnels**.
- Enter **5985** in the Source Port field and **127.0.0.1:5984** in the Destination field.
- Click **Add**, then click **Open** to log in.

**SSH with Mac OS X or Linux**

Enter the following command into the terminal on your local computer:

    ssh -L5984:127.0.0.1:5985 user@your_Utho's_IP


## Access Futon from a Web Browser

Once the SSH connection is established, open a web browser on your local computer and navigate to `http://localhost:5985/_utils/`. You will see the Futon overview page. Although it uses an HTTP URL, the connection to your server is securely established through an SSH tunnel.

{{< note >}}
You can also access CouchDB directly via its HTTP interface at `http://localhost:5985` without the need to connect to the server using a public IP address.
{{< /note >}}
