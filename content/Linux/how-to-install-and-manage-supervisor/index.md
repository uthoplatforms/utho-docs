---
title: "How To Install and Manage Supervisor"
date: "2022-09-18"
title_meta: "How To Install and Manage Supervisor"
description: "How To Install and Manage Supervisor"
keywords:  ['linux', 'manage Supervisor']
tags: ["linux", "LVM"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/how-to-install-and-manage-supervisor']
---

![Install supervisor](images/How-To-Install-and-Manage-Supervisor-1024x576.png)

### Introduction

In many VPS environments, you may have a number of small programs that you want to run all the time. These could be small shell scripts, Node.js apps, or large packages.

Most of the time, external packages come with a unit file that lets an init system like systemd handle them, or they come as docker images that a container engine can handle. But a lightweight alternative is helpful for software that isn't well-packaged or for users who don't want to interact with a low-level init system on their server.

[Supervisor](http://supervisord.org/) is a process manager which provides a singular interface for managing and monitoring a number of long-running programs. In this tutorial, you will install Supervisor on a Linux server and learn how to manage Supervisor configurations for multiple applications.

```
Prerequisites
```
- A  Linux server and either a super user ( root) or any non-root user with sudo privileges. 
- apt repository configured server to install packages

```
1\. Installation
```
Begin by updating your package sources and installing Supervisor:

```
apt update && sudo apt install supervisor
```
After installation, the supervisor service will run on its own. You can find out how it's going:

```
systemctl status supervisor
```
```
2 - Adding a Program
```
The best way to use Supervisor is to write a configuration file for each programme it will run.

> All programmes that run under Supervisor must run in a mode that doesn't run in the background. This is sometimes called "foreground mode." If your programme automatically goes back to the shell after running by default, you may need to look in the program's manual to find the option to turn on this mode. If you don't, Supervisor won't be able to tell what the program's status is.

To show how Supervisor works, we'll make a shell script that does nothing but print out a predictable message every second. This script will run in the background until someone manually stops it. Open up a file in your home directory called idle.sh with nano or your favourite text editor:

```
vi ~/idle.sh
```
Add the following contents:

```
#!/bin/bash
while true
do 
	# Echo current date to stdout
	echo `date`
	# Echo 'error!' to stderr
	echo 'error!' >&2
	sleep 1
done
```

Next, make your script executable:

```
chmod +x ~/idle.sh
```
Supervisor programmes' per-program configuration files are in the /etc/supervisor/conf.d directory. Most of the time, one programme runs per file, and the files end in.conf. We'll make a configuration file for this script called "/etc/supervisor/conf.d/idle.conf:

```
vi /etc/supervisor/conf.d/idle.conf
```
```
command=/home/ubuntu/idle.sh
autostart=true
autorestart=true
stderr_logfile=/var/log/idle.err.log
stdout_logfile=/var/log/idle.out.log
```

Now review this by

```
command=/home/ubuntu/idle.sh
```
The configuration begins by defining a program with the name `idle` and the full path to the program:

```
autostart=true
autorestart=true
```

The **`autostart`** option tells Supervisor to start this programme when the system starts up. If you set this to false, the system will need to be started by hand after being shut down.

`autorestart` defines how Supervisor should manage the program in the event that it exits:

Once our configuration file is made and saved, we can use the supervisorctl command to tell Supervisor about our new programme. First, we tell Supervisor to look in the /etc/supervisor/conf.d directory for any new or changed programme configurations.

```
supervisorctl reread
supervisorctl update
```
Any time you make a change to any program configuration file, running the two previous commands will bring the changes into effect.

```
3\. Managing Programs
```
You won't just want to run programmes; you'll also want to stop, restart, or check the status of them. The supervisorctl programme, which we used in Step 2, also has a mode that lets us control our programmes by talking to it.

To enter the interactive mode, run supervisorctl with no arguments:

```
supervisorctl
```
You can `start` or `stop` a program with the associated commands followed by the program name:

```
supervisorctl> start idle
```

Using `status` you can view again the current execution state of each program after making any changes:

```
supervisorctl> status 
```

```
idle                      STOPPED    Nov 21 12:36 PM
```

Finally, you can exit supervisorctl with Ctrl+C or by entering `quit` into the prompt:

```
supervisorctl> quit
```
