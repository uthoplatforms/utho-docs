---
title: "How To Move a PostgreSQL Data Directory to a New Location on Ubuntu 22.04"
date: "2022-09-16"
title_meta: "Move a PostgreSQL Data Directory to a New Location on Ubuntu 22"
description: "Learn how to move a PostgreSQL data directory to a new location on Ubuntu 22.04 with this detailed guide. Follow step-by-step instructions to safely relocate the PostgreSQL data directory on your Ubuntu 22.04 server, ensuring database continuity and stability.
"
keywords: ["move PostgreSQL data directory Ubuntu 22.04", "PostgreSQL data directory relocation Ubuntu 22.04", "change PostgreSQL data directory location Ubuntu 22.04", "PostgreSQL data directory move guide Ubuntu 22.04", "Ubuntu 22.04 PostgreSQL data directory migration", "move PostgreSQL database directory Ubuntu 22.04", "PostgreSQL data directory Ubuntu 22.04 tutorial", "PostgreSQL Ubuntu 22.04 data directory move steps"]

tags: ["PostgreSQL Data Directory", "ubuntu"]
icon: "postgres"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Database/Postgres/how-to-move-a-postgresql-data-directory-to-a-new-location-on-ubuntu-22-04/']
tab: true
---

![](images/How-To-Move-a-PostgreSQL-Data-Directory-to-a-New-Location-on-Ubuntu-22.04_utho.jpg)

How To Move a PostgreSQL Data Directory to a New Location on Ubuntu 22.04

**Introduction**

Databases expand over time, sometimes outgrowing the space available on their original file system. When they're on the same partition as the rest of the operating system, this can cause I/O contention.

RAID, network block storage, and other devices can provide redundancy and scalability, among other benefits. This tutorial will walk you through relocating PostgreSQL's data directory, whether you're looking to add more space, optimise performance, or take advantage of other storage features.

**Prerequisites**

You will need the following items to finish this guide:

A non-root user with sudo privileges on an Ubuntu 22.04 server. More information on creating a user with these privileges can be found in our Initial Server Setup with Ubuntu 22.04 guide.

PostgreSQL is already installed on your server. If you haven't already done so, the guide How to Install and Use PostgreSQL on Ubuntu 22.04 can help.

Throughout this tutorial, the data will be moved to a block storage device mounted at /mnt/volume nyc1 01. Before continuing with this tutorial, if you're using Block Storage on DigitalOcean, read our documentation on How to Create and Set Up Volumes for Use with Droplets for help mounting your volume.

Regardless of the underlying storage, the steps below can assist you in moving the data directory to a new location.

Step 1- Transferring the PostgreSQL Data Directory to a New Location

Before we begin moving PostgreSQL's data directory, let's check the current location with an interactive PostgreSQL session. psql is the command used to enter the interactive monitor, and -u postgres instructs sudo to run psql as the system's postgres user:

```
#sudo -u postgres psql
```

To display the current data directory, open the PostgreSQL prompt and type the following command:

```
postgres=#SHOW data_directory;
```

```
Output  
data_directory  
----------------------------- 
/var/lib/postgresql/14/main  
(1 row)
```

This output confirms that PostgreSQL is configured to use the default data directory, /var/lib/postgresql/14/main, so that's the directory you need to move. Once you've confirmed the directory on your system, use the q meta-command to exit the psql prompt:

```
postgres=#q
```

Stop PostgreSQL before making changes to the data directory to ensure the integrity of the data:

```
#sudo systemctl stop postgresql
```

The output of all service management commands is not displayed by systemctl. Use the following command to confirm that you have successfully stopped the service:

```
#sudo systemctl status postgresql
```

The output should indicate that PostgreSQL is inactive (dead), indicating that it has been terminated:

```
Output  
○ postgresql.service - PostgreSQL RDBMS  
Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor>  
Active: inactive (dead) since Thu 2022-06-30 18:46:35 UTC; 27s ago  
Process: 4588 ExecStart=/bin/true (code=exited, status=0/SUCCESS)  
Main PID: 4588 (code=exited, status=0/SUCCESS)  
CPU: 1ms
```

Now that the PostgreSQL server is no longer running, use rsync to move the existing database directory to the new location. The -a flag preserves permissions and other directory properties, while -v produces verbose output to assist you in following the progress. You'll run rsync from the postgresql directory to replicate the original directory structure in the new location. You can avoid permissions issues for future upgrades by creating that postgresql directory within the mount-point directory and retaining ownership by the PostgreSQL user.

**Note:** Be sure there is no trailing slash on the directory, which may be added if you use TAB completion. If you do include a trailing slash, rsync will dump the contents of the directory into the mount point instead of copying over the directory itself.

Following the project convention won't harm, especially if PostgreSQL needs to run different versions in the future. The version directory, 14, isn't technically necessary since you've specified the location explicitly in the postgresql.conf file:

```
#sudo rsync -av /var/lib/postgresql /mnt/volume_nyc1_01
```

Once the copy is finished, give the current folder a.bak extension and leave it there until you're sure the transfer worked. Having directories with similar names in both the new and old locations will help to prevent confusion:

```
#sudo mv /var/lib/postgresql/14/main /var/lib/postgresql/14/main.bak
```

You may now prepare PostgreSQL to access the data directory in its new location by configuring it to use the new path.

**Step 2 -Using the New Data Location as the Target  
**

The default setting for the data directory configuration directive in the /etc/postgresql/14/main/postgresql.conf file is /var/lib/postgresql/main. This file should be modified to reflect the new data directory:

```
#sudo nano /etc/postgresql/14/main/postgresql.conf
```

```
/etc/postgresql/14/main/postgresql.conf  
. . .  
data_directory = '/mnt/volume_nyc1_01/postgresql/14/main'  
. . .
```

By hitting CTRL + X, Y, then ENTER, you can save and close the document. To set up PostgreSQL to use the new data directory location, all you have to do is this. The PostgreSQL service must now be restarted, and it must be verified that it is pointing to the appropriate data directory.

**Step 3 — Restarting PostgreSQL**

Start the PostgreSQL server using systemctl after updating the data-directory directive in the postgresql.conf file:

```
#sudo systemctl start postgresql
```

Check the status of the PostgreSQL server once again using systemctl to ensure that it began successfully:

```
#sudo systemctl status postgresql
```

If the service started up right, the Active line of the command's output will say "active (exited)"

```
Output  
● postgresql.service - PostgreSQL RDBMS  
Loaded: loaded (/lib/systemd/system/postgresql.service; enabled; vendor>  
Active: active (exited) since Thu 2022-06-30 18:50:18 UTC; 3s ago  
Process: 4852 ExecStart=/bin/true (code=exited, status=0/SUCCESS)  
Main PID: 4852 (code=exited, status=0/SUCCESS)  
CPU: 1ms
```

Lastly, enter the PostgreSQL command prompt to confirm that the new data directory is truly active:

```
#sudo -u postgres psql
```

Again, make sure the value for the data directory is correct:

```
postgres=#SHOW data_directory;
```

```
Output  
data_directory  
---------------------------------------- 
/mnt/volume_nyc1_01/postgresql/14/main  
(1 row)
```

This demonstrates that PostgreSQL is utilising the new location for the data directory. After that, take a moment to make sure you can access your database and interact with the data inside. You can delete the backup data directory once you've ensured the accuracy of any current data:

```
#sudo rm -Rf /var/lib/postgresql/14/main.bak
```

With that, you've successfully changed the location of your PostgreSQL data directory.
