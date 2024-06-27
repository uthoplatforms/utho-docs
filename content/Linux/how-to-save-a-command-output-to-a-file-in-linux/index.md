---
title: "How to Save a Command Output to a File in Linux"
date: "2022-10-07"
title_meta: "How to Save a Command Output to a File in Linux"
description: "How to Save a Command Output to a File in Linux"
keywords:  ['save command', 'linux']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/how-to-save-a-command-output-to-a-file-in-linux']
---

![](images/How-to-Save-a-Command-Output-to-a-File-in-Linux_utho.jpg)

##### **Description**

In Linux, you can do a lot with the results of a command. You can put the results of a command into a variable, send them through a pipe to another command or programme to be processed, or send them to a file for further study.

Under this brief post, I will demonstrate a simple command-line technique that may be very helpful. Specifically, I will show you how to observe the result of a command on the screen while concurrently writing to a file in Linux.

**In addition to seeing output on the screen, you may also write to a file.**

df is a command that can be used on a Linux operating system to detect the kind of file system that is present on a partition in addition to providing a comprehensive overview of the amount of disc space that is both available and being utilised by a file system.

```
#df
```

![](images/image-221.png)

When you use the -h option, the statistics about the file system disc space will be shown in a manner that is "human readable" (displays statistics details in bytes, mega bytes and gigabyte).

```
#df -h
```

![](images/image-222.png)

Execute the command that is provided below in order to show the aforementioned information on the screen, as well as save it to a file, for example so that it may be analysed at a later time or sent to a system administrator.

```
#df -h | tee df.txt
```

```
#cat df.txt
```

![](images/image-223.png)

The tee command does the magic in this case; it reads from standard input and writes to files as well as standard output.

Using the -a or —append option, you may add a file that already exists.

```
#df -h | tee -a df.txt
```

##### \***If you want additional knowledge, you can get it by reading the df and tee man pages.**

```
#man df
```

```
#man tee
```

You should now be able to observe the results of a command on the screen as well as write to a file under Linux as a result of reading this brief tutorial.

##### **Thank You**
