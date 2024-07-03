---
title: "How to Use ‘at’ Command to Schedule a Task in Linux"
date: "2022-10-08"
title_meta: "How to Use ‘at’ Command to Schedule a Task in Linux"
description: "How to Use ‘at’ Command to Schedule a Task in Linux"
keywords:  ['at command', 'linux']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/miscellaneous/how-to-use-at-command-to-schedule-a-task-in-linux']
---

![How to Use ‘at’ Command to Schedule a Task in Linux](images/How-to-Use-‘at-Command-to-Schedule-a-Task-in-Linux-1024x576.png)

The at command is a substitute for the [cron job scheduler](https://utho.com/docs/tutorial/how-to-schedule-your-task-using-crontab/) that enables you to schedule a task to execute only once at a certain time without changing any configuration files.

## Prerequesties:

- yum or apt repository configured on your server so that your can install the utility
- Super user such as root or any other normal user with SUDO privileges.

## Installation and uses of 'at' command

For this tutorial, We are using CentOS 7.7 cloud from [MIcrohost Cloud](http://Microhost.com). But we will show you how to install the 'at' utility on both debian and fedora flavor Linux machines.

```
# yum install atd [on CentOS based systems]  
```
# sudo apt-get install atd [on Debian and derivatives]
```

```

Next, start and enable the at service at the boot time.

```
# systemctl start at  
```
# systemctl enable at 
```

```

You may schedule any command or job as follows after atd is operational. To ping [microhost.com](http://microhost.com) domain four times at the very next minute. you will use the bellow command.

```
# echo "ping microhost.com" | at now + 1 minute 
```

Nothing will be written on standard output when this command is run. However, you might decide to send the results to a file instead by redirecting the output.

```
# echo "ping microhost.com" | at now + 1 minute >> pingfile 
```

Please also be aware that 'at' supports custom 2-digit (indicating hours) and 4-digit times in addition to the following preset times: now, noon, and midnight (hours and minutes).

For Example:

If the current date is later than today at 11 p.m., execute updatedb tomorrow:

```
# echo "updatedb" | at 23:00
```

To restart the server at **23:45** today (same criteria as in the previous example applies):

```
# echo "restart -r now" | at 23:45 
```

The + sign and the required time specification, like in the first example, may also be used to postpone the execution by a specified number of minutes, hours, days, weeks, months, or years.

For example: To start the httpd services at after 10 minutes

```
# echo "systemctl restart httpd" | at now + 10minute > restartHTTPDreport 
```

The above command will restart the httpd service after 10 minute of executing the command and then will save the output in restartHTTPDreport file.
