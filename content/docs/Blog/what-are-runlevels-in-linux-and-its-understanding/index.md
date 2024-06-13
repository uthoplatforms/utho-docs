---
title: "What are Runlevels in Linux and its understanding"
date: "2022-10-10"
---

![what is runlevel and its explanation](images/what-is-runlevel-and-its-explanation-1024x576.png)

In this article you will know about Runlevels in Linux, A runlevel is a way that a Unix or Unix-based operating system is set to run. This way of working is already set up on a [Linux](https://utho.com/docs/tutorial/category/linux-tutorial/)\-based system. The numbers for runlevels go from 0 to 6.

Some system administrators use run levels to tell which subsystems are working, like if X is running, if the network is up and running, and so on.

Runlevels decide which programmes can run once the [OS](https://en.wikipedia.org/wiki/Operating_system) is up and running. The runlevel tells what the machine does after it boots up.

System administrators can change a system's default runlevel to suit their needs, or they can use the runlevel command to find out what the system's current runlevel is so they can evaluate it. For example, the runlevel can show if the system's network is up and running or not. Use the /sbin/runlevel command to find out what the current runlevel is and what it was before.

Runlevels 0–6 are usually assigned to single-user mode, multi-user mode with and without network services running, shutting down the system, and restarting the system. Different Linux distributions and versions of Unix have different ways of setting up these configurations.

Each basic level is used for something different. Runlevels 0, 1, and 6 never change. Different Linux distributions have different runlevels 2 through 5. When the system starts up, only one runlevel is used. They are not done one after the other. For instance, either runlevel 4 or runlevel 5 or runlevel 6 is used. It is not 4 then 5 then 6.

When a LINUX system starts up, the first thing it does is start the init process, which is responsible for running other start scripts. These scripts do things like initialise your hardware, start the network, and start the graphical user interface.  
Now, the init first finds out what the system's default run level is so that it can run the start scripts for that run level.

<table><tbody><tr><td>Runlevel 0</td><td>shuts down the system</td></tr><tr><td>Runlevel 1</td><td>single-user mode</td></tr><tr><td>Runlevel 2</td><td>multi-user mode without networking</td></tr><tr><td>Runlevel 3</td><td>multi-user mode with networking</td></tr><tr><td>Runlevel 4</td><td>user-definable</td></tr><tr><td>Runlevel 5</td><td>multi-user mode with networking</td></tr><tr><td>Runlevel 6</td><td>reboots the system to restart it</td></tr></tbody></table>

Booting a system into different runlevels solves few problems. For instance, if a machine fails to boot due to a wrong configuration file, refuses to allow the root user to log in due to a wrong /etc/passwd file or if you forget your password, you can solve these problems by booting into single-user mode.

## How to change runlevel in Redhat/ CentOS

First you would like to know, in which runlevel your machine is running. You can know this by two methods

1. By runlevel command  
    ```
# runlevel 
```
2. By systemctl command  
    ```
# systemctl get-default 
```

> Output:
> 
> Output of first command will be- multi-user.target  
> Output of second command will be- 3 5
> 
> Please note that the above output is according to my machine which is running in init 3. If you switch from runlevel 3 to runlevel 5( graphical runlevel), it will need a restart of your machine. Also to run your machine in graphical user mode will require the necessary GUI packages.

Please note that, upto redhat 8, you can change the runlevel by init command and use 0 to 6 runlevels, but you can also change the runlevel by systemctl command.

```
# init 3 
```

The above command, in centos or redhat flavor linux will run the system in multiuser mode and networking enabled but without graphical user interface. If you want to switch to the mode in which you do now want to use networking but multiple users can login and with no graphical user interface.

```
# init 2 
```

you can restart or shutdown your system by changing the runlevel of your machine to six or zero respectively.

```
# init 6 # Restart the machine  
```
# init 0 # Shutdown the machine immediately 
```

```

You can also the change the runlevel on the latest redhat( RHEL 8) and centos( CentOS 8) machines by systemctl command

```
# systemctl isolate multi-user.target # will be equal to init 3 
```

The above command will enable or run your machine in runlevel 3 which is multiuser with networking but without graphical user mode.

To run your machine in graphical machine use the below command

```
# init 5 # using init utility  
```
# systemctl isolate graphical.target # for systemctl utility 
```

```
