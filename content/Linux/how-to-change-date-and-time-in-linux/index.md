---
title: "How to change date and time in Linux"
date: "2023-03-25"
title_meta: "How to change date and time in Linux"
description: "Follow the below steps to learn How to change date and time in Linux."
keywords:  ['change date time', 'Linux']
tags: ["Linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/how-to-change-date-and-time-in-linux']
---

<figure>

![](images/How-to-change-date-and-time-in-Linux-1-1024x576.png)

<figcaption>

How to change date and time in Linux

</figcaption>

</figure>

**Description**

In this post, we will explore How to change date and time in [Linux](https://en.wikipedia.org/wiki/Linux) and the various options available to us in Linux for modifying the current date and time. On occasion, it is of the utmost need to ensure that the date and time on your [Linux](https://utho.com/docs/tutorial/linux-port-test-commandsredhat-7-centos-7-and-ubuntu-18-04/) server are accurate.

Follow the below steps to learn How to change date and time in Linux.

## Step.1  check current time and date

By using the timedatectl command that is provided below, you may get the current date and time.

```
timedatectl
```
![timdatectl command in linux](images/image-891.png)

## Step.2 Check available Time Zones

Using the timedatectl list-timezones command, which is demonstrated below, it is possible to examine all of the time zones that are currently accessible.

```
timedatectl list-timezones
```
![chang time and date](images/image-892.png)

## Step.3 Find only Asia Time Zone

As will be demonstrated in the following section, using the timedatectl list-timezones command, you can select only the time zones that you are interested in checking.

```
timedatectl list-timezones | grep -i Asia
```
![list off time zones](images/image-893.png)

Step.4 change timezone to Asia/Kolkata

You are able to alter the time zone at this point by utilising the timedatectl set-timezone command and specifying the appropriate time zone based on the output of the timedatectl list-timezones command, which can be found above.

```
timedatectl set-timezone Asia/Kolkata
```
![timezones](images/image-894.png)

## Step.5 change the time to 15:22:02

You can alter the time without affecting the date by using the timedatectl set-time command. This function is available in most modern operating systems.

```
timedatectl set-time '15:22:02'
```
![change the timezone](images/image-895.png)

## Step.6 Change the date to 6oct ,2022 and time to 15:22:02

You can adjust both the date and time by using the timedatectl set-time command, as described below.

```
timedatectl set-time '2022-10-06 15:22:02'
```
![timedatectl](images/image-896.png)

**Important**:- When you try to change the date and time manually, you may see the error "Failed to set time: Automatic time synchronisation is enabled." In that situation, you must disable the NTP before attempting to alter the date and time again.

```
timedatectl set-ntp off
```
![change date and time](images/image-897.png)

I sincerely hope that each and every one of these things was clear to you. How to change date and time in Linux.

Must read :-
