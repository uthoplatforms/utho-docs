---
weight: 40
title: 'Migrating an AWS Cloud Instance to UTHO Platform (Ubuntu 22.04)'
title_meta: "API Documentation for PostgreSQL on the Utho Platform"
description: "Discover how Utho's API simplifies PostgreSQL deployment and management, allowing you to integrate seamlessly with your cloud infrastructure."
keywords: ["MIGRATION", "AWS", "gcp", "AZURE"]
tags: ["MIGRATION", "AWS", "gcp", "AZURE"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Products/Migration/uthos-migration-tool']
icon: "api"
tab: true
---

# Migrating an AWS Cloud Instance to UTHO Platform (Ubuntu 22.04)

## Step 1: Accessing Your AWS EC2 Instance

Before starting the migration, ensure you have an active AWS EC2 instance. In this guide, we will refer to an instance named ubuntu22. When you click on your server, you will find important details such as the Public IP and Instance ID.

### Logging into the EC2 Instance via SSH

Use the following command to connect to your instance via SSH using a private key:

```
ssh -i /path/to/your-key.pem default-instance-name@instance-ip
```

**Example:**
```
ssh -i /home/ritss/Downloads/migration.pem ubuntu@35.154.216.10
```

**Breakdown of the Command:**
* `ssh` → Command to establish a secure connection to a remote server.
* `-i /home/ritss/Downloads/migration.pem` → Specifies the private key file (PEM format) used for authentication.
* `ubuntu@35.154.216.10` → ubuntu is the default username, and 35.154.216.10 is the public IP address of the remote server.

## Step 2: Setting Permissions for the Private Key

Before using the private key, set the appropriate permissions to ensure security:

```
chmod 400 /home/ritss/Downloads/migration.pem
```

**Breakdown of the Command:**
* `chmod` → Command to change file permissions.
* `400` → Grants read-only access to the owner, and denies access to the group and others.
* `/home/ritss/Downloads/migration.pem` → Path to the private key file.

## Step 3: Enabling Password Authentication for SSH

To allow SSH access using a password, modify the SSH configuration file.

### Editing the SSH Configuration File

Open the SSH configuration file using a text editor:

```
sudo nano /etc/ssh/sshd_config
```

Locate and modify the following settings:
```
PasswordAuthentication yes
PermitRootLogin yes
UsePAM yes
```

### Restart the SSH Service

After making these changes, restart the SSH service:

```
sudo service ssh restart
```

## Step 4: Changing the Password for the Default User

To set a new password for the default user (e.g., ubuntu), use the following command:

```
passwd ubuntu
```

You will be prompted to enter and confirm a new password.

## Step 5: Logging into the Instance Using a Password

Once password authentication is enabled, you can log into the instance without using the private key:

```
ssh default-instance-name@instance-ip
```

## Step 6: Migrating to the UTHO Platform

To proceed with the migration, initiate the migration process on the UTHO platform. Follow the platform-specific instructions to complete the AWS cloud migration successfully.

### Migration Process on UTHO Platform

1. Go to the UTHO console and click on Cloud Migration.
2. Click on New Migration → New Cloud.

    ![alt text](<images/Screenshot from 2025-03-18 18-14-13.png>)





3. Choose a data center as per your choice.
![alt text](<images/Screenshot from 2025-03-18 18-12-45.png>)


4. Set the storage type to General.
![alt text](<images/Screenshot from 2025-03-18 18-15-55.png>)


5. Choose resources as per your requirement.

   ![alt text](<images/Screenshot from 2025-03-18 18-16-39.png>)

6.  Choose  VPC and Subnet.
![alt text](<images/Screenshot from 2025-03-18 18-17-29.png>)


7. After creating the migration, select AWS as the provider.

8. Click on Get Migration Command.
![alt text](<images/Screenshot from 2025-03-19 10-09-04.png>)

![alt text](<images/Screenshot from 2025-03-19 10-17-45.png>)

9. Run the generated command on your AWS server.
![alt text](<images/Screenshot from 2025-03-19 10-36-33.png>)

10. After running command you get this to press 1.
![alt text](<images/Screenshot from 2025-03-19 10-48-02.png>)

This process will migrate all data, configurations, and settings from AWS to the UTHO platform successfully.

Finally, you can see your server has been migrated on UTHO.

![alt text](<images/Screenshot from 2025-03-19 10-49-13.png>)


