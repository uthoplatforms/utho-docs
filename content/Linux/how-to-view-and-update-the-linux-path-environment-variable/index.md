---
title: "How To View and Update the Linux PATH Environment Variable"
date: "2022-09-16"
---

![How To View and Update the Linux PATH Environment Variable](images/How-To-View-and-Update-the-Linux-PATH-Environment-Variable_utho.jpg)

**Introduction**

Command-line programmes may only be launched in their installation directory. PATH lets you run command-line programmes from any directory.

The PATH [variable](https://en.wikipedia.org/wiki/Environment_variable) lists folders checked before executing an operation. By updating PATH, you can execute executables from any directory without inputting the absolute file path.

Instead of typing:

To View and Update the Linux PATH [Environment](https://utho.com/docs/tutorial/methods-for-disabling-the-root-account-in-linux/) Variable please follow the below steps that will help you to increase you knowledge..

```
# /usr/bin/python3
```

You can type this in its place because the PATH variable includes the /usr/bin directory:

```
# python3 
```

Priority directories are listed first.

This lesson updates the PATH variable.

## Step.1 Taking a Look at the PATH Variable

```
#echo $PATH
```

Unmodified PATH looks like this (file paths may vary by system):

```
Output  
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games
```

PATH includes some default directories, each separated by a colon :. The system checks these folders from left to right when launching an application.

When a command-line programme isn't in any of the specified folders, you may need to add it to PATH.

## Step.2 Setting the PATH environment variable to include a directory

A directory can be added to PATH at the beginning or end.

Adding a directory to PATH will make it checked first (/the/file/path, for example).

```
# export PATH=/the/file/path:$PATH 
```

Putting a directory at the end of PATH means it will be checked last.

```
# export PATH=$PATH:/the/file/path 
```

Multiple directories can be added to PATH by using a colon:

```
# export PATH=$PATH:/the/file/path:/the/file/path2 
```

Once the export command is executed, you can view the PATH variable to see the changes:

```
# export PATH=$PATH:/the/file/path 
```

```
# echo $PATH 
```

You'll get something like this:

```
# Output  
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games:/the/file/path  

```

Only the current shell session can use this method. Once you exit and restart, the PATH variable will reset to its default value and no longer contain the added directory. PATH must be stored in a file to persist across shell sessions.

## Step .3 Adding a directory to PATH  
In this step, you'll add a directory to the shell configuration file, which is either /.bashrc or /.zshrc, depending on the shell you're using. In this guide, /.bashrc will be used as an example.

First, open the file called /.bashrc:

```
# nano ~/.bashrc 
```

/.bashrc has existing info you won't change. At the bottom of the file, add your new directory:

```
# export PATH=$PATH:the/file/path 
```

Use the methods in the previous section to specify whether the new directory should be checked first or last.

Close the file. A fresh shell session will alter the PATH variable. Source command applies modifications to current session.

```
# source ~/.bashrc 
```

You can add new directories by opening this file and attaching colon-separated directories to the export command.

Hope you have understand the above steps to View and Update the Linux PATH Environment Variable..

Must read:- [https://utho.com/docs/tutorial/cheat-sheet-for-15-nmcli-commands-in-linux-rhel-centos/](https://utho.com/docs/tutorial/cheat-sheet-for-15-nmcli-commands-in-linux-rhel-centos/)
