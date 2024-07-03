---
title: "How to schedule your task using crontab"
date: "2022-09-03"
title_meta: "How to schedule your task using crontab"
description: "How to schedule your task using crontab"
keywords:  ['crontab', 'linux']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/miscellaneous/how-to-schedule-your-task-using-crontab']
---

<figure>

![How to schedule your task using crontab](images/How-to-schedule-your-task-with-crontab-1.png)

<figcaption>

How to schedule your task using crontab

</figcaption>

</figure>

## Description:

In this article, we will learn how to schedule your task using crontab. The crontab programme is used to add, remove, or list the tables that tell the cron daemon what to do.

A crontab file tells the cron daemon to do something, like "run this command at this time on this date." [Each user](https://utho.com/docs/tutorial/multiple-user-account-creation-in-linux/) can have their own crontab. These files are in the /var directory, but you shouldn't edit them directly.

A cron job is any task that you schedule using crons. Cron jobs help us automate tasks that we do every hour, every day, every month, or every year.

### Important Files:

1. To allow user to use crontab, make entry in- /etc/cron.allow
2. To deny user to use crontab, make entry in- /etc/cron.deny

<figure>

![](images/image-38.png)

<figcaption>

Add username in /etc/cron.deny

</figcaption>

</figure>

By default, every user is allowed to use crontab to [schedule their tasks](https://en.wikipedia.org/wiki/Scheduling_(computing)). But if we want to deny any particular user, make an entry in above file number 2 using vim or any other way you like.

## How to use crontab

Before using crontab, we need to understand the syntax to schedule any task using crontab.

Below is the syntax summary to use crontab

```
*    *    *   *    *  Command_to_execute    Argument_for_command
|    |    |    |   |       
|    |    |    |    Day of the Week ( 0 - 6 ) ( Sunday = 0 )
|    |    |    |
|    |    |    Month ( 1 - 12 )
|    |    |
|    |    Day of Month ( 1 - 31 )
|    |
|    Hour ( 0 - 23 )
|
Min ( 0 - 59 )
```

In the above summary, every asterisk( \* ) represent a different time period of different values.

<table><tbody><tr><td class="has-text-align-center" data-align="center"><strong>Asterisk name( from left to right)</strong></td><td><strong>Value</strong></td><td class="has-text-align-center" data-align="center"><strong>Description</strong></td></tr><tr><td class="has-text-align-center" data-align="center">Minute of the hour</td><td>0-59</td><td class="has-text-align-center" data-align="center">Command would be executed at the specific minute.</td></tr><tr><td class="has-text-align-center" data-align="center">Hours of the day</td><td>0-23</td><td class="has-text-align-center" data-align="center">Command would be executed at the specific hour.</td></tr><tr><td class="has-text-align-center" data-align="center">Day of the Month</td><td>1-31</td><td class="has-text-align-center" data-align="center">Commands would be executed in these days of the months.</td></tr><tr><td class="has-text-align-center" data-align="center">Month of the year</td><td>1-12</td><td class="has-text-align-center" data-align="center">The month in which tasks need to be executed.</td></tr><tr><td class="has-text-align-center" data-align="center">Weekdays</td><td>0-6</td><td class="has-text-align-center" data-align="center">Days of the week where commands would run. Here, 0 is Sunday.</td></tr></tbody></table>

## **Crontab Options**

- To add/ delete/ edit a cron job

```
crontab -e
```
- To list the cronjobs of the current user

```
crontab -l
```
- To remove/ deinstall the cronjobs

```
crontab -r
```
- To list another user's cronjobs

```
crontab -u username -l
```
- To edit another user's cronjobs.

```
crontab -u username -e
```
## Examples:

**To test the crontab, first create a script called test`.sh` which prints the system date and time and appends it to a file.**

```
# vim test.sh 
```

```
#!/bin/sh

date >> testfile
```

**To make sure that this script executes, we need to give it executable permission**

```
# chmod +x test.sh 
```

**To run /usr/bin/test.sh at every minute**

```
* * * * * /usr/bin/sh test.sh
```

**To run test.sh everyday at 10pm (22:00)**

```
0 22 * * * /usr/bin/sh test.sh
```

**To restart the httpd servic**e **every Saturday at 1am (01:00)**

```
0 1 * * 7 /usr/bin/systemctl restart httpd
```

**To run test.sh at 07:30, 09:30 13:30 and 15:30**

```
30 7,9,13,15 * * * /usr/bin/sh test.sh
```

> Thanks you!!!
