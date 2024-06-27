---
title: "How To Use Rsync to Sync Local and Remote Directories"
date: "2022-09-16"
title_meta: "How To Use Rsync to Sync Local and Remote Directories"
description: "How To Use Rsync to Sync Local and Remote Directories"
keywords:  ['rsync', 'linux']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/how-to-use-rsync-to-sync-local-and-remote-directories']
---

![How To Use Rsync to Sync Local and Remote Directories](images/How-To-Use-Rsync-to-Sync-Local-and-Remote-Directories_utho.jpg)

This article will teach you How to Use Rsync to Synchronize Local and Remote Directories. 

**Introduction**

[Rsync](https://en.wikipedia.org/wiki/Rsync#:~:text=Rsync%20is%20typically%20used%20for,as%20user%20to%20remote%2Dhost%20.), which stands for "remote sync," is a tool for keeping files in sync both remotely and locally. It uses an algorithm to move only the parts of files that have changed, which reduces the amount of data that needs to be copied.

In this tutorial, we'll explain what [Rsync](https://utho.com/docs/tutorial/find-multiple-ways-to-user-account-info-and-login-details-in-linux/) is, go over the syntax for using Rsync, show you how to use Rsync to sync with a remote system, and talk about other options you have.

**Prerequisites**

To practise using rsync to sync files between a local system and a remote system, you'll need two computers, one to act as your local system and the other as your remote system. As long as they are set up the right way, these two machines could be virtual private servers, virtual machines, containers, or even personal computers.

If you want to use servers to follow this guide, it would be smart to give each of them administrative users and set up a firewall. Follow our First Server Setup Guide to set up these servers.

To follow this tutorial, you will need to have made SSH keys on both of the machines you use. This is true no matter what kind of machines you use. Then, follow Step 2 of that guide to copy each server's public key to the authorized keys file of the other server.

This guide was tested on computers running Ubuntu 20.04, but it should work on most Linux-based computers with rsync installed if rsync is installed.

To Use Rsync to Sync Local and Remote Directories please read the below steps ....

## Defination Rsync

Rsync is a very flexible tool for synchronising files over a network. It comes with most Linux distributions by default because it is common on Linux and Unix-like systems and is a popular tool for system scripts.

## Recognizing Rsync Syntax

rsync's syntax is similar to that of other tools like ssh, scp, and cp.

First, run the following command to go to your home directory:

```
#cd 
```

after that create a test directory

```
#mkdir dir1
```

Make a second test directoey:

```
#mkdir dir2
```

Add some test files now:

```
#touch dir1/file{1..100}
```

There is currently a dir1 directory with 100 empty files in it. Verify by listing the files out:

```
#ls dir1
```

```
Output  
file1 file18 file27 file36 file45 file54 file63 file72 file81 file90  
file10 file19 file28 file37 file46 file55 file64 file73 file82 file91  
file100 file2 file29 file38 file47 file56 file65 file74 file83 file92  
file11 file20 file3 file39 file48 file57 file66 file75 file84 file93  
file12 file21 file30 file4 file49 file58 file67 file76 file85 file94  
file13 file22 file31 file40 file5 file59 file68 file77 file86 file95  
file14 file23 file32 file41 file50 file6 file69 file78 file87 file96  
file15 file24 file33 file42 file51 file60 file7 file79 file88 file97  
file16 file25 file34 file43 file52 file61 file70 file8 file89 file98  
file17 file26 file35 file44 file53 file62 file71 file80 file9 file99
```

dir2 is also empty. To sync dir1 to dir2 on the same system, execute rsync with the "recursive" -r flag:

```
#rsync -r dir1/ dir2
```

\-a is a combo flag that means "archive." It syncs recursively and preserves symbolic links, special and device files, modification times, groups, owners, and rights. This flag is preferred over -r. Run the same command with the -a flag:

```
#rsync -a dir1/ dir2
```

Please note that the previous two instructions have a trailing slash (/) after the first argument.

```
#rsync -a dir1/ dir2
```

Trailing slash indicates dir1's contents. Without the trailing slash, dir1 would be inside dir2. The result is a hierarchy:

```
Trailing slash indicates dir1's contents. Without the trailing slash, dir1 would be inside dir2. The result is a hierarchy:
```

Check your rsync command arguments before executing. Using Rsync's -n or —dry-run option does this. The -v flag, which means “verbose”, is also necessary to get the appropriate output. You’ll combine the a, n, and v flags in the following command:

```
#rsync -anv dir1/ dir2
```

```
Output  
sending incremental file list  
./  
file1  
file10  
file100  
file11  
file12  
file13  
file14  
file15  
file16  
file17  
file18
```

Now compare that output to what you get when you remove the trailing slash, like in:

```
#rsync -anv dir1 dir2
```

```
Output  
sending incremental file list  
dir1/  
dir1/file1  
dir1/file10  
dir1/file100  
dir1/file11  
dir1/file12  
dir1/file13  
dir1/file14  
dir1/file15  
dir1/file16  
dir1/file17  
dir1/file18
```

This output shows that the directory was migrated, not just its files.

## Keeping a Local System in Synch with a Remote One Using Rsync

To utilise rsync with a remote system, you need SSH access and rsync on both machines. Once SSH access is confirmed between two machines, use the following syntax to sync dir1 to a remote machine. In this example, you'll omit the trailing slash to transmit the real directory:

```
#rsync -a ~/dir1 username@remote_host:destination_directory
```

This technique "pushes" a local directory to a distant machine. Pull syncs a remote directory with the local system. If dir1 were remote, the syntax would be:

```
#rsync -a username@remote_host:/home/username/dir1 place_to_sync_on_local_machine
```

Like cp and similar tools, source is always first, destination second.

## Utilizing Alternative Rsync Methods

Rsync's flag choices can change the utility's default behaviour.

Text files that haven't been compressed can be compressed with the -z option to minimise network transfer.

```
#rsync -az source destination
```

Also useful is -P. It's —progress and —partial. The first flag shows transfer progress, and the second resumes interrupted transfers.

```
#rsync -azP source destination
```

```
#Output  
sending incremental file list  
created directory destination  
source/  
source/file1  
0 100% 0.00kB/s 0:00:00 (xfr#1, to-chk=99/101)  
sourcefile10  
0 100% 0.00kB/s 0:00:00 (xfr#2, to-chk=98/101)  
source/file100  
0 100% 0.00kB/s 0:00:00 (xfr#3, to-chk=97/101)  
source/file11  
0 100% 0.00kB/s 0:00:00 (xfr#4, to-chk=96/101)  
source/file12  
0 100% 0.00kB/s 0:00:00 (xfr#5, to-chk=95/101)
```

If you execute the command once more, you will see a condensed version of the results because no modifications have been made since the last time. This exemplifies Rsync's capability of making use of modification times in order to detect whether or not changes have been made:

```
#rsync -azP source destination
```  
```
Output  
sending incremental file list  
sent 818 bytes received 12 bytes 1660.00 bytes/sec  
total size is 0 speedup is 0.00
```

Say you updated several files' modification time with this command:

```
#touch dir1/file{1..10}
```

If you run rsync with -azP again, you'll see how it intelligently recopies just modified files.

```
#rsync -azP source destination
```

```
#Output  
sending incremental file list  
file1  
0 100% 0.00kB/s 0:00:00 (xfer#1, to-check=99/101)  
file10  
0 100% 0.00kB/s 0:00:00 (xfer#2, to-check=98/101)  
file2  
0 100% 0.00kB/s 0:00:00 (xfer#3, to-check=87/101)  
file3  
0 100% 0.00kB/s 0:00:00 (xfer#4, to-check=76/101)
```

To sync two directories, delete files from the destination if they're removed from the source. rsync doesn't remove files by default.

—delete modifies this behaviour. Before using this option, use -n, —dry-run, to prevent data loss.

```
#rsync -an --delete source destination
```

You can exclude files or directories from a syncing directory by using the —exclude= option and a comma-separated list.

```
#rsync -a --exclude=pattern_to_exclude source destination
```

You can override an exclusion pattern with the —include= option.

```
#rsync -a --exclude=pattern_to_exclude --include=pattern_to_include source destination  

```

The Rsync —backup option stores backups of essential files. It's used with the —backup-dir option, which defines where backup files go.

```
#rsync -a --delete --backup --backup-dir=/path/to/backups /path/to/source destination  

```

Rsync simplifies networked file transfers and local directory syncing. Rsync's versatility makes it useful for many file-level tasks.

Mastering Rsync lets you construct elaborate backups and control how and what is shared.

Hope you have understand the above steps To Use Rsync to Sync Local and Remote Directories

Must read:- [https://utho.com/docs/tutorial/find-multiple-ways-to-user-account-info-and-login-details-in-linux/](https://utho.com/docs/tutorial/find-multiple-ways-to-user-account-info-and-login-details-in-linux/)

**Thankyou**
