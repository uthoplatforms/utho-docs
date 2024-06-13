---
title: "Introduction to rsync"
date: "2020-04-20"
---

Rsync is a command line tool that syncs files and folders between locations. Some workflows, which can be implemented using rsync like, update a production host on a developer or call rsync using a cron job to save the data backup to a storage location regularly. You can even move your server from other providers to Microhost using rsync.

Rsync is incremental, thus successive backup operations completed very quickly once the initial operation is finished. Only the differences are copied between source and target files. This rsync property is ideal for automated operations.

## Install rsync on linux

Linux / Unix: Not all \* nix systems are default to rsync, but they may be installed from the software repository of your distribution or compiled from source. Use below command to install rsync package.

```
[root@Microhost ~]# yum install rsync -y
```

**Reasons for considering rsync over cp or scp**

- Creates incremental data backups.
- rsync’s - -del option deletes files located at the destination which are no longer at the source.
- rsync can resume failed transfers (as long as they were started with rsync).
- rsync can compress data with the -z option, so no need to pipe to an archiving utility.
- Copies from source to destination only the data which is different between the two locations.

## Working With rsync

A wide range of options are available to use with rsync, and many people have their favorite set of option while using this tool. Single rsync options can be aliases from several others as well, so rsync -a, for instance, gives the same result as rsync -rlptgoD.

\[ht\_message mstyle="success" title="Note:" " show\_icon="true" id="" class="" style="" \] Rsync should be installed on both sources (locally and remote system),if you’re synchronizing files over a network.\[/ht\_message\]

So rsync is a device for which you ought to be very careful when copying commands from forum posts and other websites on the internet without understanding exactly what they are doing. If you take the time to investigate and experiment before using your information, you get the most out of rsync.

The two commands you will need to start familiarizing yourself with rsync are:

```
[root@Microhost ~]# man rsync
[root@Microhost ~]# rsync -help
```

A rsync command's basic structure is identical to cp, and SCP.

```
[root@Microhost ~]# rsync -[argument] source destination
```

When you have several destinations, they are connected to the end of the string just as the cp command does:

```
[root@Microhost ~]# rsync -zarvh source loation1 location2 location3
```

Either source or destination may be local or distant, or both. If you are synchronizing files over a network then rsync would need to be enabled on both local and remote machines. Rsync uses SSH to encrypt the data while transmitting across networks, and it operates with SSH keys to easily authenticate remote servers.

Remote locations such as SSH or SCP commands are formatted. For instance, you would use: to synchronize a local folder with one on a remote server,

```
[root@Microhost ~]# rsync -zarvh /path_of _source_folder username@:/path_of_destination_folder
```

To synchronize a remote location folder on local machine:

```
[root@Microhost ~]# rsync -zarvh username@:/path_of_destination_folder /path_of _source_folder 
```

Thank You :)
