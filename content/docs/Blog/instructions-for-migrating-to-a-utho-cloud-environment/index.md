---
title: "Instructions for Migrating to a Utho Cloud Environment"
date: "2022-11-15"
---

<figure>

![](images/Instructions-for-Migrating-to-a-Utho-Cloud-Environment.jpg)

<figcaption>

Instructions for Migrating to a Utho's Cloud Environment

</figcaption>

</figure>

This article will guide you through the recommended instructions for migrating to a Utho Cloud Environment from another host. Depending on the type of software you're employing, you'll need to do a variety of distinct tasks. Despite this, the outline of the high-level plan remains the same regardless of the type of service you offer.

The most typical method for moving websites from one hosting provider to another is to:

Even while reinstalling your services might be a time-consuming process, any difficulties that arise when you are configuring your apps are often far simpler to debug than problems with low-level setup. When it comes to migration, this is the preferred technique.

Create an instance of your services on Utho Cloud, and then transfer over just the configuration and data that is relevant to those instances. As a consequence, this produces a Linux environment that has a 100% chance of starting up properly on the Utho Cloud platform.

## Decision-Making in Regards to a Migration Plan

When transitioning from one hosting provider to another, there are two basic options:

### 1\. Recommended technique- Installing each service independently .

Establish a Utho Cloud instance, deploy a Utho-supplied Linux image to the instance, and then copy only the configuration and data relevant to your services. This builds a Linux environment that has a one hundred percent chance of starting up normally on a Utho platform.

Reinstalling all of your services could take some time. Problems that arise when installing your programs, on the other hand, are typically easier to resolve than low-level configuration issues. When it comes to migration, this is the recommended method.

### 2\. Full Duplication (Not Suggested)

After establishing a Utho Cloud Instance, you must clone your existing discs by transferring them from your primary host to the Utho. This action will result in the creation of an exact copy of your discs on the Utho platform. Due to the fact that low-level system configuration files can vary between hosting providers, it is not recommended that you employ this method.

Due to these differences, your Utho was unable to boot normally. It is possible to modify these parameters sufficiently for your Utho to function normally. However, obtaining the exact values for these configurations can be difficult, as can debugging when the numbers have been entered incorrectly.

## Steps to follow

## 1\. Deploy a new Utho Cloud instance

When building a new Utho Cloud, there are two factors to think about: the first is in which data centre the instnace should be located, and the second is the hardware resource plan the instance should operate under.

1. Location of Data Center:  
    When determining the bandwidth available between your location and the data centre, it is helpful to compare the speeds of download. These comparisons will indicate the latency between where you are located and the data centre; ideally, the latency should be as low as possible.  
    

3. Configuration of cloud instance:  
    Choose a plan that has a minimum amount of storage space that is sufficient for the data that is currently being stored on the current VM instance that you are using.

Find out the Linux distribution the instance you currently have running on your current server is using, and install it on your new Utho cloud. If your existing deployment utilises an earlier version of a Linux distribution, you should deploy the most recent version of that distribution that is available for your new cloud. This will guarantee that you have access to the most recent security upgrades and applications.

Please refer to guide of [how to create Utho Cloud](https://utho.com/docs/tutorial/microhost-product-details/) server for further information on the deployment of your new Linux image. 

## 2- Install required software on your Cloud server

Install the exact same software stack onto your new Utho cloud server that is already running on your existing cloud instance. You may get a list of all installed packages by using the package manager that is associated with your cloud instance. For instance, if you are operating with Debian or Ubuntu, you may type in the following command:

```
# apt list --installed 
```

And if you are operating with Redhat or CentOS machine:

```
# yum list --installed 
```

* * *

You may refer to our Documents and Tutorials to learn how to set up your system's software on your new cloud instance once you have determined which software you would want to move to your new Utho Cloud server and after you have decided which software you would like to migrate.

## 3- Identify your configuration files and database files

Which of the software configuration options have to be kept (e.g. web server, virtual host information, database connection settings, and which files contain these settings, etc.).

The location on the disc where your data is stored (e.g. as files in a directory, in a database process, etc.).

It is quite probable that you will want a database dump in order to retrieve your data if it is stored in a database. When you do this, a file will be created on the disc that wraps the data from your database and can be moved over the network just like any other file:

## 4- Transfer you data from Cloud instance to your Utho Cloud

You may use a network transfer programme such as [rsync to move your data](https://utho.com/docs/tutorial/how-to-use-rsync-to-sync-local-and-remote-directories/) to the cloud instance you have created with Utho. 

Execute the following command from inside the cloud VPS you are currently using. Replace instance-user with the Linux user that is logged into your Utho Cloud instance, and replace cloud-ip with the IP address that is assigned to your Utho Cloud instance.

```
# rsync -avh /path/to/data_directory insatance-user@cloud-ip:/path/to/destination_directory 
```

Uploading files from the current host's /path/to/data\_directory to the new Utho Cloud instance's /path/to/destination\_direcotory will occur when the command shown above is executed.

If you uploaded a database dump file to your new Utho Cloud instance, you must also [restore the dump file](https://utho.com/docs/tutorial/using-mysqldump-to-backup-mysql-databases/) in order for your database programme to utilise the data regularly.

## 5- Test your new Environment

After installing your programme and recovering your data, you should test the installation to check that everything is working properly. Even if you have not yet updated the DNS records to point to your Utho Instance deployment, you can still preview your services without DNS.

Utilize this opportunity to perform load testing on the newly constructed service. ApacheBench is one of the most commonly used benchmarking tools for web services. After completing all of these load tests, if you discover that your hardware resource plan is insufficient for the workload, you should resize it. After that, testing should proceed.

After completing the testing portion of the migration, proceed to the final step, which is updating your DNS records.

### 6- Modifying DNS Records

By associating your domain with the IP address of your newly formed instance, you may direct your website visitors to your Utho Cloud Instance. You have two possibilities when it comes to transferring DNS records:

Use Utho's speedy and dependable DNS hosting at no extra cost so long as your Utho account includes at least one active cloud instance.

Continue to use the nameserver authority you currently possess, and update your DNS records with the IP address of the newly formed cloud instance. You must inquire with your current internet service provider about any costs involved with utilising their DNS services. If you use the nameservers given by your domain name registrar, they are often free to use.

## Use Utho's manage DNS service

By associating your domain with the IP address of your newly formed instance, you may direct your website visitors to your Utho Cloud Instance. You have two possibilities when it comes to transferring DNS records:

Use Utho's speedy and dependable DNS hosting at no extra cost so long as your Utho account includes at least one active cloud instance.

Continue to use the nameserver authority you currently possess, and update your DNS records with the IP address of the newly formed cloud instance. You must inquire with your current internet service provider about any costs involved with utilising their DNS services. If you use the nameservers given by your domain name registrar, they are often free to use.

1. Follow [Utho's instructions](https://utho.com/docs/tutorial/dns-management/) on how to add a domain zone in order to make DNS records for your domain. You should recreate the DNS entries that are posted on the website of your current nameserver authority, but you should change the IP addresses to your Utho Cloud instance IPs wherever it makes sense to do so.  
    

3. Find the company from which you bought your domain name. This company is often called your domain name's registrar. If you don't know who the registrant of your domain name is, you can use a [Whois](http://whois.com/whois/) Search tool to find out.  
      
    Even though it is very probable that the same corporation serves as both your registrant and nameserver authority, this is not always the case. Registrars often provide free DNS services.  
      
    Simply upgrade the authoritative nameservers to Utho's nameservers by logging into the domain registrar's control panel and making the required changes:  
      
    \* ns1.microhost.com  
    \* ns2.microhost.com  
    

5. When you have waited the amount of time you chose for your TTL, the domain will start to spread. If you didn't shorten your TTL, it could take up to 48 hours for this to happen.  
    

7. When you're ready, use a web browser to go to your domain. It should now show your Utho Cloud website instead of the one hosted by your old provider. If you can't tell the difference between the two, use the DIG utility. It should show the IP address of the Utho Cloud instance you are using.  
    

9. When you're ready, use a web browser to go to your domain. It should now show your Utho-hosted website instead of the one on your old host. If you can't tell the difference between the two, use the [DNS utility](https://www.whatsmydns.net/). It should show the IP address that your Utho is using.  
    

11. You need to set up the reverse DNS for your domain. If you run a mail server, this is the most important thing for you to know.  
    

### Use Your Current Nameservers as an Alternative

All of the DNS entries associated with the IP address of your former host must be changed to use the IP address of your new Utho Cloud instance if you wish to continue utilising your current nameservers. To alter your DNS records, speak with the organisation in charge of running your nameserver.

## Conclusion

After completing the aforementioned procedures, your service ought to have been fully moved to Utho Cloud. It is advised that you give your shared hosting service a few more days before terminating it to make sure everything is working properly. Additionally, ensure sure you do not need any additional files to be added from the shared host.
