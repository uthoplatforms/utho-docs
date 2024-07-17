---
slug: Install configure run spark on hadoop yarn cluster
description: 'Restic is a backup utility written in Go. This guide shows how to configure Restic to backup your MariaDB (or MySQL) databases onto Utho Object Storage.'
keywords: ['mariadb','mysql','backup','backups','restic','off-site backups','Object Storage']
tags: ['mariadb', 'mysql', 'automation']
published: 2024-07-10
modified_by:
  name: Utho
title: "Backup MariaDB or MySQL Databases to Utho Object Storage with Restic"
title_meta: "Backup MariaDB Databases to Utho Object Storage with Restic"
authors: ["Pawan Kumar"]
tags: ["saas"]
---

## Introduction

"Maintaining backups of your databases is crucial for restoring data in case of server faults, user errors, or worst-case scenarios like hacking or website defacement.

Successful backups should be automatic, reliable, and secure. This guide demonstrates how to configure Restic on your Utho to back up MariaDB (or MySQL) databases to Utho Object Storage. This ensures you can recover data even if your Utho becomes inaccessible.

Restic is a backup utility written in Go, compatible with most Linux distributions running kernel versions newer than 2.6.23. Backups are stored as snapshots in a repository, which can reside on various cloud storage providers or a separate directory on your Utho (though not recommended). This guide focuses on using Utho Object Storage for your backup repository.

{{< note >}}
MariaDB is a fork of MySQL. Where you see a reference to *MariaDB* in this guide, it should apply to MySQL also.
{{< /note >}}

{{< note >}}
The steps in this guide require root privileges, and commands are run with `sudo` unless otherwise noted. For more information on privileges, see our [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< /note >}}

## Before You Begin

If you haven't already, create a Utho account and Compute Instance. Refer to our Getting Started with Utho and Creating a Compute Instance guides.

Follow our Setting Up and Securing a Compute Instance guide to update your system. You may also want to set the timezone, configure your hostname, create a limited user account, and enhance SSH access security.

Install MariaDB on your Utho by following the appropriate guide from How to Install MariaDB based on your Utho's distribution.

Create an Object Storage bucket to store your backup repository. If you don't have one already, follow the Create a Bucket guide.

    {{< content "object-storage-cancellation-shortguide" >}}

1.  [Generate Object Storage access keys](/docs/products/storage/object-storage/guides/access-keys/).

1. Ensure your Utho has the `wget` and `bzip2` utilities installed. Install them with the following commands:

   **CentOS / Fedora**

       yum install wget bzip2

   **Ubuntu / Debian**

       apt install wget bzip

## Install Restic

{{< content "install-restic-shortguide" >}}

## Create the Restic Repository

{{< content "create-restic-repository-shortguide" >}}

## Backup All Databases

{{< note >}}
In this section's commands, remember to replace `your-bucket-name` and `us-east-1.Uthoobjects.com` with the name of your Object Storage bucket and its cluster hostname.
{{< /note >}}

The mysqldump utility is employed to extract the contents of a database into a file stored on your Utho. The example script in this section iterates through all databases on your server, dumping each one into its individual SQL file.

1. Create a file in your `/usr/local/bin` directory:

        sudo nano /usr/local/bin/backup_mariadb

1. Copy the following contents into the file:

    {{< file "/usr/local/bin/backup_mariadb" >}}
#!/bin/bash
PATH="/usr/local/bin:$PATH"
source /root/restic_params
mysql --defaults-extra-file=/root/mysql_cnf -N -e 'show databases' | while read dbname; do /usr/bin/mysqldump --defaults-extra-file=/root/mysql_cnf --complete-insert "$dbname" > "/var/backups/mariadb/$dbname".sql; done
restic -r s3:us-east-1.Uthoobjects.com/your-bucket-name -p /root/restic_pw backup /var/backups/mariadb
{{< /file >}}

1. Make the script executable and create the folder to store the backup files (if it doesn't already exist):

        sudo chmod u+x /usr/local/bin/backup_mariadb
        sudo mkdir -p /var/backups/mariadb/

1. Line 3 of the script refers to a MySQL configuration file named `msql_cnf`, which is used to authenticate with your database. Create this file under your `/root` directory and add the username and password for your database:

        sudo nano /root/mysql_cnf

    Copy and past the contents of the example file and replace the values of `your-database-username` and `your-database-password` with your own.

    {{< file "/root/mysql_cnf" >}}
[client]
user="your-database-username"
password="your-database-password"
{{< /file >}}

1. Run your first backup using the script you created:

        sudo backup_mariadb

    You should see a similar output:

    {{< output >}}
mysqldump: Got error: 1044: "Access denied for user 'root'@'localhost' to database 'information_schema'" when using LOCK TABLES
mysqldump: Got error: 1142: "SELECT, LOCK TABLES command denied to user 'root'@'localhost' for table 'accounts'" when using LOCK TABLES
repository 1689c602 opened successfully, password is correct

Files:           4 new,     0 changed,     0 unmodified
Dirs:            2 new,     0 changed,     0 unmodified
Added to the repo: 470.844 KiB

processed 4 files, 469.825 KiB in 0:01
snapshot 81072f28 saved
{{< /output >}}

1. Verify that your backups have been created. You should see one backup file per database:

        ls -al /var/backups/mariadb

    The output displays all backup files stored in the backups directory you created:

    {{< output >}}
total 492
drwxr-xr-x 2 root root   4096 Jul 21 19:47 .
drwxr-xr-x 3 root root   4096 Jul 21 19:46 ..
-rw-r--r-- 1 root root    830 Jul 21 19:47 information_schema.sql
-rw-r--r-- 1 root root 479441 Jul 21 19:47 mysql.sql
-rw-r--r-- 1 root root    830 Jul 21 19:47 performance_schema.sql
-rw-r--r-- 1 root root   1292 Jul 21 19:47 wordpress.sql
{{< /output >}}

1. Executing the script also creates a snapshot in your Restic repository. Use Restic's `snapshot` command to view it:

        sudo /bin/bash -c "source /root/restic_params; restic -r s3:us-east-1.Uthoobjects.com/your-bucket-name -p /root/restic_pw snapshots"

    Restic returns a similar output:

    {{< output >}}
repository 1689c602 opened successfully, password is correct
ID        Time                 Host        Tags        Paths
---------------------------------------------------------------------------
81072f28  2020-07-21 19:47:19  li1356-54               /var/backups/mariadb
---------------------------------------------------------------------------
1 snapshots
{{< /output >}}

## Set Up Automated Database Backups

There are several methods in Linux for scheduling jobs on a defined schedule. This section covers several common approaches that you can use to set up the backup script to run at regular intervals. Review these methods and choose the one that best fits your requirements.

{{< note >}}
When choosing how often to run your script, consider your databases' usage, how much data you could potentially lose, and the storage space required.
{{< /note >}}

### Cron

System Cron jobs exist as entries in the `/etc/crontab` file. Open your systems `crontab` file for editing with the following command:

    sudo crontab -e

Include a line in your cron configuration file that points to your backup script. This example schedules the backup to run every hour, precisely on the hour. For more scheduling options, refer to the Schedule tasks with Cron article.

    0 * * * * /usr/local/bin/backup_mariadb > /tmp/mariadb-backup-log.txt 2>&1

### Systemd

Systemd can execute commands (referred to as units) periodically using timers. You can utilize systemd commands to monitor the last execution times of these timers and the command outputs.

To schedule a command, you require two configuration files: the service file, containing the commands to execute, and a timer file, specifying when to execute the service.

Create the service configuration file and paste the example contents:

    sudo nano /etc/systemd/system/backup-mariadb.service

{{< file "/etc/systemd/system/backup-mariadb.service" >}}[Unit]
Description=Backup MariaDB databases
[Service]
ExecStart=/usr/local/bin/backup_mariadb
Environment=USER=root HOME=/root
{{< /file >}}

Create the timer configuration file and paste the example contents. The OnCalendar directive tells systemd when to execute the commands defined in the service file. In this example, the commands in the service file run every hour, precisely on the hour.

    sudo nano /etc/systemd/system/backup-mariadb.timer

{{< file "/etc/systemd/system/backup-mariadb.timer" >}}[Unit]
Description=Backup MariaDB databases
[Timer]
OnCalendar=*-*-* *:00:00
[Install]
WantedBy=timers.target
{{< /file >}}

When you are satisfied with your timer's configurations, enable the timer:

    sudo systemctl enable --now backup-mariadb.timer

You can monitor all your system's timers with the following command:

    sudo systemctl list-timers

You should see a similar output:

{{< output >}}
NEXT                        LEFT          LAST                        PASSED        UNIT                         ACTIVATES
Mon 2020-07-20 16:00:00 BST 35min left    Mon 2020-07-20 15:00:03 BST 24min ago     backup-mariadb.timer         backup-mariadb.service
{{< /output >}}

The `NEXT` and `LEFT` column tells you the exact time and how long until the timer executes the service file next. The `LAST` and `PASSED` columns display information on when the timer last executed the service file.

## Finishing Up

"Login to your Utho Cloud Manager account and navigate to the Object Storage bucket where you've stored your Restic backups. You should observe a collection of files similar to those shown in the screenshot below. These files together constitute the Restic repository; individual database backup files won't be visible.

To explore the backups and files within the Restic repository, use the restic command on the machine where Restic was installed, as described in the Before You Begin section."

### Create an Alias

It can get tedious typing out the arguments to the Restic command. To make life easier for maintenance and daily management, create an alias for the command with the arguments you need.

{{< note >}}
Because the credentials that Restic uses were created under the root user's home folder, the example alias in this section only works for the root user.
{{< /note >}}

Add the following lines to your root user's .profile file. On Ubuntu systems, this file is typically located at /root/.profile. For detailed instructions on creating reusable aliases, refer to the How to Add the Linux alias Command in the .bashrc File guide.

    source /root/restic_params
    alias myrestic='restic -r s3:us-east-1.Uthoobjects.com/your-bucket-name -p /root/restic_pw'

After logging out of your system and back in again, you can run restic using your aliased command:

    myrestic snapshots

### Restore a Backup

Backups are not useful if you cannot restore them. It's a good idea to test out your backups once in a while. To restore the latest usable backup from Restic, run the `restore latest` command:

    restic -r s3:us-east-1.Uthoobjects.com/your-bucket-name -p /root/restic_pw restore latest -t /root

{{< note >}}
The `-t` option tells Restic where to restore your backup. Restic restores your backup's files and recreates the full directory structure that existed at the time the backup was taken.

For example, consider the backup file `/var/backups/mariadb/wordpress.sql`. Restoring a backup containing this file to a target of `/home/myuser` results in the file being restored as:

    /home/myuser/var/backups/mariadb/wordpress.sql
{{< /note >}}

To restore a backup from a particular point-in-time, issue the example command to find the snapshot ID for the specific backup.

    sudo /bin/bash -c "source /root/restic_params; restic -r s3:us-east-1.Uthoobjects.com/your-bucket-name -p /root/restic_pw snapshots"

The output resembles the example, where the first column displays the snapshot ID:

{{< output >}}
repository 1689c602 opened successfully, password is correct
ID        Time                 Host        Tags        Paths
---------------------------------------------------------------------------
81072f28  2020-07-21 19:47:19  li1356-54               /var/backups/mariadb
---------------------------------------------------------------------------
1 snapshots
{{< /output >}}

Pass the selected ID to the restore command instead of `latest`. Replace `81072f28` in the example with your own snapshot ID:

    sudo /bin/bash -c "source /root/restic_params; restic -r s3:us-east-1.Uthoobjects.com/your-bucket-name -p /root/restic_pw restore 81072f28 -t /root"

The above commands restore all databases taken in the backup. If you only want a selected backup, pass the filename using the `-i` option, along with either `latest` or the snapshot ID:

    sudo /bin/bash -c "source /root/restic_params; restic -r s3:us-east-1.Uthoobjects.com/your-bucket-name -p /root/restic_pw restore 81072f28 -i wordpress.sql -t /root"

### Maintain your Repository

Your backup repository can rapidly expand in size, especially if you're backing up large databases hourly.

Restic offers the ability to automatically manage your backup snapshots based on a flexible policy using snapshot policies.

Consider scheduling the forget command to run automatically on a regular basis (e.g., daily) to control the size of your backup repository. For detailed information, refer to the snapshot policies article.

{{< note >}}
Don't forget to pass the `--prune` option to the `forget` command or the space won't actually be freed from your repository.

Pruning a repository can take significant time and stops new backups from taking place while it is being run, so it is best to run it often and non-interactively.
{{< /note >}}