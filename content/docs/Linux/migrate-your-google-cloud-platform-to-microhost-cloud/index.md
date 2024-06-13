---
title: "Migrate your Google Cloud Platform to Microhost Cloud"
date: "2022-11-08"
---

![Migrate your Google Cloud Platform to Microhost](images/Migrate-your-Google-Cloud-Platform-to-Microhost-1024x576.png)

This tutorial will walk you through the suggested process for Migrate your Google Cloud Platform to Microhost Cloud.

The most typical method for moving websites from one hosting provider to another is to:

Create an instance of your services on Microhost Cloud, and then transfer over just the configuration and data that is relevant to those instances. As a consequence, this produces a Linux environment that has a 100% chance of starting up properly on the Microhost Cloud platform.

Even while reinstalling your services might be a time-consuming process, any difficulties that arise when you are configuring your apps are often far simpler to debug than problems with low-level setup. When it comes to migration, this is the preferred technique.

## Steps to follow

## 1\. Deploy a new Microhost Cloud instance

When building a new Microhost Cloud, there are two factors to think about: the first is in which data centre the instnace should be located, and the second is the hardware resource plan the instance should operate under.

1. Location of Data Center  
    When determining the bandwidth available between your location and the data centre, it is helpful to compare the speeds of download. These comparisons will indicate the latency between where you are located and the data centre; ideally, the latency should be as low as possible.
2. Configuration of cloud instance  
    Choose a plan that has a minimum amount of storage space that is sufficient for the data that is currently being stored on the [GCP](https://en.wikipedia.org/wiki/Google_Cloud_Platform) VM instance that you are using.

Find out the Linux distribution the instance you currently have running on GCP is using, and install it on your new Microhost cloud. If your existing deployment utilises an earlier version of a Linux distribution, you should deploy the most recent version of that distribution that is available for your new cloud. This will guarantee that you have access to the most recent security upgrades and applications.

Please refer to guide of [how to create Microhost Cloud](https://utho.com/docs/tutorial/microhost-product-details/) server for further information on the deployment of your new Linux image. 

## 2- Install required software on your Cloud server

Install the exact same software stack onto your new Microhost cloud server that is already running on your existing GCP instance. You may get a list of all installed packages by using the package manager that is associated with your GCP instance. For instance, if you are operating with Debian or Ubuntu, you may type in the following command:

```
# apt list --installed 
```

And if you are operating with Redhat or CentOS machine:

```
# yum list --installed 
```

* * *

You may refer to our Documents and Tutorials to learn how to set up your system's software on your new cloud instance once you have determined which software you would want to move to your new Microhost Cloud server and after you have decided which software you would like to migrate.

## 3- Identify your configuration files and database files

Which of the software configuration options have to be kept (e.g. web server, virtual host information, database connection settings, and which files contain these settings, etc.).

The location on the disc where your data is stored (e.g. as files in a directory, in a database process, etc.).

It is quite probable that you will want a database dump in order to retrieve your data if it is stored in a database. When you do this, a file will be created on the disc that wraps the data from your database and can be moved over the network just like any other file:

## 4- Transfer you data from GCP to your Microhost Cloud

You may use a network transfer programme such as [rsync to move your data](https://utho.com/docs/tutorial/how-to-use-rsync-to-sync-local-and-remote-directories/) to the cloud instance you have created with Microhost. 

Execute the following command from inside the GCP instance you are currently using. Replace instance-user with the Linux user that is logged into your Microhost Cloud instance, and replace cloud-ip with the IP address that is assigned to your Microhost Cloud instance.

```
# rsync -avh /path/to/data_directory insatance-user@cloud-ip:/path/to/destination_directory 
```

Uploading files from the current host's /path/to/data\_directory to the new Microhost Cloud instance's /path/to/destination\_direcotory will occur when the command shown above is executed.

If you uploaded a database dump file to your new Instance, you must also [restore the dump file](https://utho.com/docs/tutorial/using-mysqldump-to-backup-mysql-databases/) in order for your database programme to utilise the data regularly.

## 5- Test your new Environment

When you have done configuring your programme and recovering your data, test the installation to ensure that it functions appropriately.

Use this opportunity to load test your new service. If you notice that the hardware resource plan you initially selected is insufficient for completing these load tests, resize your plan and continue testing.  
When you've completed testing, go to the next stage in the migration process: upgrading your DNS records.

So, in conclusion, we can say that the process for Migrate your Google Cloud Platform to Microhost Cloud is quite easy and simple
