---
title: "How to fix \"Command not found\" error in CentOS"
date: "2022-09-05"
---

![](images/How-to-fix_Command-not-found_-error-in-centos_utho.jpg)

New Linux or Unix users often ask this. "Command not found" means Linux or UNIX looked everywhere for that application but couldn't find it. You may have misspelled the command name (typo) or the system administrator did not install it. Try these recommendations to fix this error:

Ensure that command is the path  
PATH is a shell environment variable that specifies the folders that your shell will examine to locate commands. The current search path can be viewed with the following echo/printf command:

**Step 1.** Log in to your server via SSH.

![](images/Screenshot_12-6.png)  
**step :2**  
```
#echo "$PATH"
```

![](images/Screenshot_2-22.png)

Most user commands are in the directories /bin, /usr/bin, or /usr/local/bin. These directories are where all of your programmes are put. When you type "clear," you run the /usr/bin/clear file. So, if it isn't in your PATH, try adding directories to your search path as follows (setup Linux or UNIX search path with the following bash export command):

**Step:3**  
```
#export PATH=$PATH:/bin:/usr/local/bin
```

![](images/Screenshot_1-15.png)

copy this command(export PATH=$PATH:/bin:/usr/local/bin) to .bashrc file  
**Step:4**  
```
#vi .bashrc
```

![](images/Screenshot_3-16.png)

bashrc file is a script, and by source it, the commands contained within it are executed.

**Step:5**  
```
#source .bashrc
```

Why source bashrc

Running the bashrc file is one reason to use source. bashrc is a script file executed when launching an interactive shell. It's per-user and in your home directory.  
![](images/Screenshot_4-19.png)
