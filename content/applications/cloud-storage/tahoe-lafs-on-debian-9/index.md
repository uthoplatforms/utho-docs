---
slug: "Unleash Tahoe-LAFS: Your Guide to Setting Up on Debian 9"
description: "Explore the Ultimate Data Guardian: Tahoe-LAFS ensures encrypted, tamper-proof data storage with redundant copies across servers"
og_description: "Discover the Tahoe Least Authority File Store (Tahoe-LAFS), a decentralized system prioritizing confidentiality, data integrity, and redundancy for enhanced file security and accessibility. Follow our guide to effortlessly create, manage, and access your own Tahoe-LAFS grid."
keywords: ["confidential", "encrypted", "integrity", "redundant", "private", "filesystem", "storage"]
tags: ["debian"]
published: 2024-03-26
modified: 2024-03-26
modified_by:
  name: Utho
title: "Secure Your Cloud Data: Unveiling Tahoe-LAFS"
authors: ["Pawan Kumar"]
---

## Unlocking Tahoe-LAFS: Your Essential Guide

Tahoe-LAFS stands out from other decentralized or distributed file systems like Gluster or Ceph due to its unique solutions to common challenges. The Least Authority File Store (LAFS) is meticulously designed with these key principles:

1.  **Confidentiality**: Protecting your data privacy, even when stored on external servers. Storing sensitive data in the cloud comes with inherent risks such as potential hacking or unauthorized access. Tahoe-LAFS mitigates these risks by encrypting data before it reaches storage servers.

2.  **Data integrity**: Detecting any unauthorized alterations to encrypted data and, in some cases, restoring it to its original state.

3.  **Redundancy**:  Distributing data redundantly across storage nodes for resilience. By default, Tahoe-LAFS uses a 3-of-10 configuration, where uploaded files are split into ten shares and distributed randomly among available storage nodes. To recover the file, you only need to retrieve three of these shares. This setup ensures data availability even if some servers fail. Additionally, you can adjust the configuration to increase resistance to failure or attacks.

Having more storage nodes and changing the default 3-of-10 to something else means you can make the setup more resistant to failure or attacks. 3-of-20 would give you a more uniform distribution. 1-of-10 would increase failure resistance but would keep ten copies of your data. So one gigabyte of data would require ten gigabytes of storage. This mechanism of shares makes it possible to destroy compromised or failed servers, create new ones, add them to the pool and redistribute shares if required.

These features make Tahoe-LAFS ideal for securely storing sensitive data on remote servers while minimizing the risk of data loss. Storage capacity can be dynamically expanded by adding more machines to the pool. For further information, refer to the [Tahoe-LAFS documentation](http://tahoe-lafs.readthedocs.io/en/latest/about.html).

## Before You Begin

1. If you haven't already, sign up for a Utho account and set up a *Debian 9* Compute Instance.

2. Refer to our guide to update your system. You might also want to adjust the timezone, configure your hostname, create a restricted user account, and enhance SSH security.

{{< note >}}
The procedures outlined in this guide necessitate root privileges. Ensure you execute the following steps as `root` or with the `sudo` prefix.
{{< /note >}}

## Server Specifications and Recommendations

1. By default, you'll need a minimum of 10 storage nodes for optimal performance. However, for testing purposes, you can start with fewer nodes. Keep in mind that having less than seven storage units might result in most uploads failing entirely. Refer to the documentation on `shares.needed`, `shares.total`, and `shares.happy` to understand more about [how to configure your nodes](http://tahoe-lafs.readthedocs.io/en/latest/configuration.html?#client-configuration)..

2.  Ensure your storage node Uthos have a minimum of 2GB of RAM. The memory and CPU requirements increase with the size of files you intend to upload. With the current version of Tahoe-LAFS available in Debian 9's repositories, you'll need at least 1GB of RAM when uploading mutable files larger than 40MB. If the node exhausts its RAM, it will result in an *out-of-memory kill*. Regularly monitor the Grid Status page in the web user interface to manage your grid efficiently.

3.  For a more dependable and resilient setup, deploy Uthos in different data centers.

## Installing Tahoe-LAFS and Configuring the Introducer

Introducers act as intermediaries, serving as central points that link storage nodes and clients within the grid.

Here's a breakdown of the advantages and disadvantages of using introducers:

Advantages:

Facilitate alerting every node when a new peer joins the grid.
Inform joining machines about currently active peers to which they can connect.
Disadvantages:

Potential single point of failure.
However, without introducers, you'd need to manually edit a configuration file on every node and add a new IP address each time you insert another node into the grid.
To enhance reliability, you can configure multiple introducers, ideally located in different data centers, to mitigate the risk of crashes or other unforeseen events.

Once you're familiar with the initial introducer setup, you can [read about additional introducers](http://tahoe-lafs.readthedocs.io/en/latest/configuration.html#additional-introducer-definitions).

1.  Login as the root user and create a user with restricted privileges:

        adduser --disabled-password --gecos "" tahoe

2.  Install Tahoe-LAFS on your system:

        apt-get install tahoe-lafs

3.  Log in using the tahoe user:

        su - tahoe

4.  Create the introducer configuration file, and replace 203.0.113.1 with your Utho's public IP address:

        tahoe create-introducer --port=tcp:1234 --location=tcp:203.0.113.1:1234 --basedir=introducer

This command creates a directory named `introducer`, which includes various configuration files, logs, and identifiers.

5.  Generate an identifier by initiating the introducer:

        tahoe run --basedir introducer

After initiating the introducer, the last line of output should confirm that the introducer is running. Press **CTRL+C** to halt the program. The identifier required, known as a *FURL*, is now generated.

6.  Display the FURL using the `cat` command:

        cat introducer/private/introducer.furl

    Copy the whole line starting with **pb://** and paste it somewhere you can access it later. The storage nodes and clients will need to be configured with that value.

7.  Log out from the `tahoe` user and return to the root user:

        exit

8.  To automatically start the introducer on boot, create a systemd service file with the following content:

        {{< file "/etc/systemd/system/tahoe-autostart-introducer.service" >}}
        [Unit]
        Description=Tahoe-LAFS autostart introducer
        After=network.target

        [Service]
        Type=simple
        User=tahoe
        WorkingDirectory=/home/tahoe
        ExecStart=/usr/bin/tahoe run introducer --logfile=logs/introducer.log

        [Install]
        WantedBy=multi-user.target

        {{< /file >}}

Although you can add a rule here to restart the process if it crashes, it's advisable to manually inspect the Utho each time a node, client, or introducer crashes before initiating the process again.

9.  Enable the service to start automatically at boot:

        systemctl enable tahoe-autostart-introducer.service

10. Start the introducer service:

        systemctl start tahoe-autostart-introducer.service

You can now close the SSH session to avoid confusion and prevent entering commands on the wrong Utho while configuring the rest.

### Restarting the Introducer:

If the process crashes or encounters an error, you can start or restart the service using these commands.

To start the introducer service:

    systemctl start tahoe-autostart-introducer.service

Restart the service:

    systemctl restart tahoe-autostart-introducer.service

## Adding Storage Nodes to the Tahoe-LAFS Grid:

While the process can be automated for expanding your storage pool, it's beneficial to set up your first node manually to gain a better understanding of how things work and where certain files are located. The initial steps from the [Before You Begin](#before-you-begin) section also apply here.

{{< note >}}

If you require large amounts of disk space, configure block storage before proceeding with the other steps in this section.

When configuring `/etc/fstab`, mount your volume in /home instead of `/mnt/BlockStorage1` as instructed in the tutorial. Use the same location when using the `mount` command. However, this approach has the drawback that you won't be able to automate the creation of storage nodes with the steps provided in the next subsection.
{{< /note >}}

1.  After launching a new Utho and deploying Debian 9, log in as root and create an unprivileged user:

        adduser --disabled-password --gecos "" tahoe

2.  Install Tahoe-LAFS by running the following command:

        apt-get install tahoe-lafs

3.  Log in using the unprivileged user account:

        su - tahoe

4.  Retrieve the introducer FURL copied in Step 6 of [the introducer installation](#install-tahoe-lafs-and-set-up-the-introducer), and paste it after --introducer=. Replace pb://<Introducer FURL> with your own FURL. Replace 203.0.113.1 in --location with the public IP address of the Utho. Choose unique nicknames for each server as you repeat this step on new Uthos.

        tahoe create-node --nickname=node01 --introducer=pb://<Introducer FURL> --port=tcp:1235 --location=tcp:203.0.113.1:1235

Configuration files, shares, logs, and other data are stored in `/home/tahoe/.tahoe`.

5.  Switch back to the root shell:

        exit

6.  Create a systemd service file:

        {{< file "/etc/systemd/system/tahoe-autostart-node.service" >}}
        [Unit]
        Description=Tahoe-LAFS autostart node
        After=network.target

        [Service]
        Type=simple
        User=tahoe
        WorkingDirectory=/home/tahoe
        ExecStart=/usr/bin/tahoe run .tahoe --logfile=logs/node.log

        [Install]
        WantedBy=multi-user.target

        {{< /file >}}

7.  Enable the service to start the storage node automatically at boot:

        systemctl enable tahoe-autostart-node.service

8.  Start the service to launch the node.

        systemctl start tahoe-autostart-node.service

### Automatically Configure Storage Nodes with Utho StackScripts

Automating the configuration of numerous storage nodes is possible with Utho StackScripts, which is helpful for users needing many nodes. Instead of launching all instances at once, it's prudent to verify each setup's success individually. You can temporarily skip to the following sections and use the web interface to confirm. Later, return to this section, launch each Utho, and refresh the page after a few minutes to ensure they appear with a green checkmark.

{{< note >}}
This StackScript depends on *icanhazip.com* to fetch the external IP address of each Utho. Although the site has redundant servers, it may occasionally be unavailable.
{{< /note >}}

1. Get acquainted with StackScripts, then head to the StackScripts page to create a new one.

2.  Choose Debian 9 as the distribution, then copy and paste the following script into the **Script** section:

        #!/bin/bash

        #<UDF name="nickname" Label="Storage Node Nickname" example="node01" />
        #<UDF name="introducer" Label="Introducer FURL" example="pb://wfpe..." />

        apt-get update
        apt-get -y upgrade
        adduser --disabled-password --gecos "" tahoe
        apt-get -y install tahoe-lafs
        su - -c "tahoe create-node --nickname=$NICKNAME --introducer=$INTRODUCER --port=tcp:1235 --location=tcp:`curl -4 -s icanhazip.com`:1235" tahoe

        echo "[Unit]
        Description=Tahoe-LAFS autostart node
        After=network.target

        [Service]
        Type=simple
        User=tahoe
        WorkingDirectory=/home/tahoe
        ExecStart=/usr/bin/tahoe run .tahoe --logfile=logs/node.log

        [Install]
        WantedBy=multi-user.target" >> /etc/systemd/system/tahoe-autostart-node.service

        systemctl enable tahoe-autostart-node.service

        systemctl start tahoe-autostart-node.service

Save your changes.

3. Create a new Utho, deploy the StackScript on a Debian 9 image, and boot your Utho.

4. Repeat this procedure to create as many nodes as necessary for your storage cluster.

## Configuring the Tahoe-LAFS Client on Your Local Machine

To securely upload and download files to and from the grid, you must set up a client node on your local machine.

While you could use port forwarding to access the web user interface from a storage node hosted on Utho, or use the command line interface on a remote server to work with files in the grid, it's not recommended to do so. Going this route exposes you to a few risks like accidentally leaking unencrypted data or *filecaps/dircaps* (think of them as passwords, giving you access to files and directories; more about this later).

Install the Tahoe-LAFS Client for your operating system:

*  [Windows](http://tahoe-lafs.readthedocs.io/en/latest/windows.html)
*  [MacOS](http://tahoe-lafs.readthedocs.io/en/latest/OS-X.html)
*  Linux users can install Tahoe-LAFS using their distribution's package manager, as described in earlier sections.
*  If Tahoe-LAFS is not available in your distribution's repositories, [build Tahoe-LAFS from source](http://tahoe-lafs.readthedocs.io/en/latest/INSTALL.html?highlight=build%20tahoe#preliminaries).

1. Run the command `tahoe create-client` to configure a client node, replacing `pb://<Introducer FURL>` with your own introducer FURL.

        tahoe create-client --nickname=localclient --introducer=pb://<Introducer FURL>

2.  Launch the client to start working with your grid.

        tahoe run

3.  Close the server by pressing **CTRL+C**.

## Managing Your Grid with Tahoe-LAFS's Web Interface

The web interface provides an intuitive way to manage your grid. It offers a comprehensive overview of your grid, displaying active and inactive nodes, connection status, errors, total storage space available, and more.

1.  By default, the web server is configured to listen on the *loopback* interface, specifically on port `3456`. To connect to it, launch the local client and then access the address in your web browser.

        tahoe run --basedir client

2.  You can upload files using three different algorithms:

*  **Immutable**: Ideal for storing files that won't be changed.
*  **SDMF (Small Mutable Distributed Files)**: Originally for small files but can handle larger ones too. It might be slower for big files since it replaces all blocks, even for minor changes.
*  **MDMF (Medium Distributed Mutable Files)**: Allows modification of large files directly, updating only the changed parts. It supports appending data and retrieving specific blocks on demand. Use this for large files that need frequent updates.

3.  Once you upload a file, you receive a *capability* or filecap. For example, an SDMF filecap looks something like this:

        URI:SSK:4a4hv34xtt43a6s7ft76i563oa:7s643ebsf2yujglqhn55xo7c5ohunx2tpoi32dahgr23seob7t5q

Filecaps are the exclusive means to access encrypted data. Ensure to store filecaps securely. Losing a filecap means losing access to your data permanently, with no way to retrieve it.

4.  Keeping track of multiple random strings of characters can be challenging. A more efficient method to organize your data is by using directories. Here are a few advantages they offer:

*  You can bookmark them in your browser for quick access later.
*  You access these directories using cryptographic secrets. If you lose the bookmarks or access codes (writecaps/readcaps), there's no way to retrieve them. However, you can still access the contents of the directory if you have bookmarks for individual items or their access codes saved elsewhere.
*  Keeping track of a single directory capability, which grants access to hundreds of objects, is simpler than managing hundreds of individual access codes.
*  Clicking on **More Info** or **More info on this directory** will provide you with read-only capabilities. These capabilities allow you to share data with others, verify the integrity of data, or repair and redistribute any unhealthy shares.

## Using Tahoe-LAFS's Command Line Interface (CLI)

Although the web user interface is user-friendly, it has certain limitations. An alternative method to manage files and directories is through the command line interface (CLI). Some benefits of using the CLI include the ability to upload files recursively and synchronize (backup) directories.

* Once you've started the local client, open another terminal window or command prompt and create an alias:

        tahoe create-alias testing

This action creates a directory on the grid and assigns it an alias, making it accessible by typing `testing:` instead of a lengthy capability string.

* To transfer a file from your current local working directory to the newly created alias:

        tahoe cp file1 testing:

*  Display the contents of the alias:

        tahoe ls testing:

*  Display the capabilities of files/directories:

        tahoe ls --uri testing:

*  To upload a whole directory:

        tahoe cp --recursive name-of-local-directory testing:

*  Back up a directory:

        tahoe backup name-of-local-directory testing:

This process generates incremental backups stored in directories with timestamps, uploading only changed files when the command is rerun.

* Fix issues and redistribute file shares as needed:

        tahoe deep-check --repair testing:

It's wise to regularly execute this command on crucial directories, especially after losing a few storage nodes.

* Additionally, make sure to save the capabilities stored in your aliases and store them securely (back them up on another machine, preferably encrypted with a strong password). You can view these capabilities using:

        tahoe list-aliases

*  To show a list of available commands:

        tahoe

*  If you require further assistance with a command:

        tahoe name-of-command --help

For instance: `tahoe ls --help`. For detailed information about Tahoe-LAFS, refer to the [official documentation](http://tahoe-lafs.readthedocs.io/en/latest/frontends/CLI.html).

## Exploring Further Actions

Now that your grid is operational, it's crucial to ensure its smooth operation by implementing some enhancements:

1. Helper Nodes for Low Upload Bandwidth: If users with limited upload bandwidth experience delays when sending files to the grid, consider [set up helper nodes](http://tahoe-lafs.readthedocs.io/en/latest/helper.html). These slowdowns may occur because your local Tahoe client also needs to transmit redundant data to multiple nodes. Learn more about setting up helper nodes here.

2. Garbage Collection for Storage Servers: Over time, your storage servers may become filled with data that is no longer necessary. Familiarize yourself with [garbage collection](http://tahoe-lafs.readthedocs.io/en/latest/garbage-collection.html) to learn how to efficiently remove unnecessary files. Explore more about garbage collection here.
