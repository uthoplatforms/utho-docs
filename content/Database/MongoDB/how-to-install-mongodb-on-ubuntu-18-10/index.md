---
title: "How to install mongodb on Ubuntu 18.10"
date: "2022-02-28"
title_meta: "mongodb installtion guide on Ubuntu"
description: "Learn how to install MongoDB on Ubuntu 18.10 with this comprehensive guide. Follow these step-by-step instructions to set up MongoDB, a popular NoSQL database, on your Ubuntu 18.10 system for efficient data storage and management.
"
keywords: ["install MongoDB Ubuntu 18.10", "MongoDB setup Ubuntu 18.10", "Ubuntu 18.10 MongoDB installation guide", "NoSQL database Ubuntu", "Ubuntu MongoDB tutorial", "MongoDB installation steps Ubuntu 18.10", "database management Ubuntu", "MongoDB Ubuntu 18.10 instructions"]

tags: ["mongodb", "ubuntu"]
icon: "mongodb"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Database/MongoDB/how-to-install-mongodb-on-ubuntu-18-10/']
tab: true
---

![](images/How-to-install-mongodb-on-Ubuntu-18.10_utho.jpg)

MongoDB is a document database built on a scale-out architecture that has become popular with developers of all kinds who are building scalable applications using agile methodologies. The document data model is a powerful way to store and retrieve data that allows developers to move fast.  
  it provides high performance and great scalability features, it is being used for building modern applications that require powerful, mission-critical and high-availability databases.

1\. Login the server with the root credential on the putty. 

![](images/image-26-2.png)

After successfully login you will get looking like bellow mentioned screenshot.

After login first update the system software package cache to have the most latest version of the repository listings. Command given below  
```
# sudo apt update 
```

Next, install MongoDB package that includes several other packages such as mongo-tools, mongodb-clients, mongodb-server and mongodb-server-core.

```
# sudo apt install mongodb 
```

To start a MongoDB service, type the following command.

```
# sudo systemctl stop mongodb
```
To enable again MongoDB service, type the following command.

```
# sudo systemctl enable mongodb
```
Once you have successfully installed it, the MongoDB service will start automatically via systemd and the process listens on port **27017**. You can verify its status using the systemctl command as shown.

```
# sudo systemctl status mongodb
```
![](images/Screenshot_3-12.png)

By default MongoDB comes with user authentication disabled, its therefore started without access control. To launch the **mongo shell**, run the following command.

```
# mongo 
```

Once you have connected to the mongo shell, you can list all available databases with the following command.

```
# show dbs 
```

switch to the admin database, then create the **root user** using following commands.

Now exit the mongo shell to enable authentication as explained next.

 The mongodb instance was started without the `--auth` command line option. You need to enable authentication of users by editing /lib/systemd/system/mongod.service file, first open the file for editing like so.  
```
sudo vim /lib/systemd/system/mongodb.service 
```

Under the `[Service]` config section, find the parameter ExecStart.

Change it to the following.

![](images/Screenshot_7-1-2.png)

Save the file and exit it.

After making changes to configuration file, run ‘systemctl daemon-reload‘ to reload units and restart the MongoDB service and check its status as follows.

```
# systemctl daemon-reload 
```

```
# sudo systemctl restart mongodbb 
```

```
# sudo systemctl status mongodbb 
```

![](images/Screenshot_8-3-1.png)

Now when you try to connect to mongodb, you must authenticate yourself as a MongoDB user. For example:

That’s all! MongoDB is an open-source, modern No-SQL database management system that provides high performance, high availability, and automatic scaling.

Thank you :)
