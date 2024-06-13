---
title: "How To Use ps, kill, and nice to Manage Processes in Linux"
date: "2022-09-16"
---

![Use ps, kill and nice to Manage Processes in Linux](images/How-To-Use-ps-kill-and-nice-to-Manage-Processes-in-Linux-1024x576.png)

**Introduction**

  
Like any modern computer, a Linux server can run more than one programme at the same time. These are called "individual processes" and are handled as such.

Linux will take care of the low-level, behind-the-scenes parts of a process's life cycle, such as starting up, shutting down, allocating memory, etc., but you will need a way to interact with the operating system to manage them at a higher level.

This guide will teach you about some of the most important parts of process management. Linux has a number of built-in tools that can be used for this.

You will try these ideas out in Ubuntu 20.04, but any modern Linux distribution will work the same way.

Please follow these instructions in order to use the ps, [kill](https://en.wikipedia.org/wiki/Kill_(command)), and nice commands:

## Step.1 How to View Linux Running Processes

Using the top command, you may view every process running on your server:

```
# top 
```

```
# Output  
top - 15:14:40 up 46 min, 1 user, load average: 0.00, 0.01, 0.05  
Tasks: 56 total, 1 running, 55 sleeping, 0 stopped, 0 zombie  
Cpu(s): 0.0%us, 0.0%sy, 0.0%ni,100.0%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st  
Mem: 1019600k total, 316576k used, 703024k free, 7652k buffers  
Swap: 0k total, 0k used, 0k free, 258976k cached

PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND  
1 root 20 0 24188 2120 1300 S 0.0 0.2 0:00.56 init  
2 root 20 0 0 0 0 S 0.0 0.0 0:00.00 kthreadd  
3 root 20 0 0 0 0 S 0.0 0.0 0:00.07 ksoftirqd/0  
6 root RT 0 0 0 0 S 0.0 0.0 0:00.00 migration/0  
7 root RT 0 0 0 0 S 0.0 0.0 0:00.03 watchdog/0  
8 root 0 -20 0 0 0 S 0.0 0.0 0:00.00 cpuset  
9 root 0 -20 0 0 0 S 0.0 0.0 0:00.00 khelper  
10 root 20 0 0 0 0 S 0.0 0.0 0:00.00 kdevtmpfs 
```

The first few lines of output include CPU/memory load and running jobs.

There's 1 operating process and 55 sleeping processes not requiring CPU cycles.

The output indicates running processes and their utilisation. By default, top sorts by CPU utilisation, so the busiest processes appear first. top will run until you exit it with Ctrl+C. This sends a kill signal to stop the process gracefully if possible.

Most package repositories have the enhanced top programme, htop. Ubuntu 20.04 apt:

```
# sudo apt install htop 
```

The htop command will thereafter be available:

```
 # htop 
```

```
Output  
Mem[||||||||||| 49/995MB] Load average: 0.00 0.03 0.05  
CPU[ 0.0%] Tasks: 21, 3 thr; 1 running  
Swp[ 0/0MB] Uptime: 00:58:11

PID USER PRI NI VIRT RES SHR S CPU% MEM% TIME+ Command  
1259 root 20 0 25660 1880 1368 R 0.0 0.2 0:00.06 htop  
1 root 20 0 24188 2120 1300 S 0.0 0.2 0:00.56 /sbin/init  
311 root 20 0 17224 636 440 S 0.0 0.1 0:00.07 upstart-udev-brid  
314 root 20 0 21592 1280 760 S 0.0 0.1 0:00.06 /sbin/udevd --dae  
389 messagebu 20 0 23808 688 444 S 0.0 0.1 0:00.01 dbus-daemon --sys  
407 syslog 20 0 243M 1404 1080 S 0.0 0.1 0:00.02 rsyslogd -c5  
408 syslog 20 0 243M 1404 1080 S 0.0 0.1 0:00.00 rsyslogd -c5  
409 syslog 20 0 243M 1404 1080 S 0.0 0.1 0:00.00 rsyslogd -c5  
406 syslog 20 0 243M 1404 1080 S 0.0 0.1 0:00.04 rsyslogd -c5  
553 root 20 0 15180 400 204 S 0.0 0.0 0:00.01 upstart-socket-br 
```

htop improves CPU thread visibility, terminal colour support, and sorting options. It's not always installed by default, but it can replace top. Ctrl+C exits htop like top. Learn top and htop.

Next, learn how to query specific processes.

## Step.2 How to List Processes Using ps

top and htop provide a dashboard-like interface for monitoring running processes, comparable to a graphical task manager. A dashboard interface can provide an overview, but it does not typically return directly actionable data. Linux has a second standard tool named ps for querying running processes.

Running ps without arguments offers minimal information.

```
# ps 
```

```
Output  
PID TTY TIME CMD  
1017 pts/0 00:00:00 bash  
1262 pts/0 00:00:00 ps 
```

This output lists all processes related to the current user and terminal session. This makes sense if you are simply running the bash shell and this ps command in the terminal at the moment.

To obtain a more comprehensive view of this system's processes, you can do ps aux:

```
# ps aux 
```

```
Output  
USER PID %CPU %MEM VSZ RSS TTY STAT START TIME COMMAND  
root 1 0.0 0.2 24188 2120 ? Ss 14:28 0:00 /sbin/init  
root 2 0.0 0.0 0 0 ? S 14:28 0:00 [kthreadd]  
root 3 0.0 0.0 0 0 ? S 14:28 0:00 [ksoftirqd/0]  
root 6 0.0 0.0 0 0 ? S 14:28 0:00 [migration/0]  
root 7 0.0 0.0 0 0 ? S 14:28 0:00 [watchdog/0]  
root 8 0.0 0.0 0 0 ? S< 14:28 0:00 [cpuset]  
root 9 0.0 0.0 0 0 ? S< 14:28 0:00 [khelper] 
```

These parameters instruct ps to display processes owned by all users in a more readable format, regardless of their terminal relationship.

Using pipes, grep may be used to scan the output of ps aux in order to return the name of a specific process. This is useful if you suspect the programme has crashed or need to terminate it for whatever reason.

```
# ps aux | grep bash 
```

```
 Output  
microhost 41664 0.7 0.0 34162880 2528 s000 S 1:35pm 0:00.04 -bash  
microhost 41748 0.0 0.0 34122844 828 s000 S+ 1:35pm 0:00.00 grep bash 
```

This returns both the recently executed grep process and the currently running bash shell. It also returns their total memory and CPU consumption, how long they've been running, and their process ID, which is shown in the result above. Each process in Linux and Unix-like systems is issued a process ID, or PID. This is how the operating system keeps track of and identifies processes.

The pgrep command provides a simple method for obtaining a process's PID.

```
# pgrep bash 
```

The PID "1" is assigned to the init process, which is the initial process created upon system boot.

```
# pgrep init 
```

This process spawns all system processes. Later processes have higher PIDs.

The process that spawned another is its parent. Parent processes have a PPID, which is visible in top, htop, and ps column headings.

Any user-OS process communication involves translating process names and PIDs. These tools always output the PID. Next, you'll learn how to use PIDs to stop, resume, and other processes.

## Step.3 How to Send Signals to Processes in Linux

Signals control Linux processes. Signals are an OS-level technique of terminating or changing programme behaviour.

Kill is the most common programme signal. This utility's default function is to kill a process.

```
# kill PID_of_target_process 
```

It sends the process TERM. Term tells a process to end. So, the software may clean up and quit cleanly.

If a misbehaving programme doesn't exit when given the TERM signal, send the KILL signal.

```
# kill -KILL PID_of_target_process  

```

A specific signal not sent to the programme.

The process is shut down by the operating system kernel. This bypasses programmes that disregard signal input.

Every signal has a number that can be passed instead of the name. You can pass "-15" for "-TERM" and "-9" for "-KILL."

Signals don't just stop programmes. Other actions can be taken with them.

Many background programmes (called "daemons") restart when given the HUP, or hang-up, signal. Apache usually works this way.

```
# sudo kill -HUP pid_of_apache  

```

The command above reloads Apache's configuration file and resumes service.

\-l lists all kill signals.

```
# kill -l  

```

Although PIDs are the typical mechanism for transmitting signals, it is also possible to do so using ordinary process names.

The pkill command behaves nearly identically to kill, except it operates on a process name.

```
# pkill -9 ping  

```

The preceding command is equal to:

```
# kill -9 pgrep ping  

```

The preceding command is equal to:

```
# killall firefox  

```

This command will deliver the TERM signal to all instances of Firefox currently operating on the system.

## Step.4 Modifying Process Priorities

You may need to prioritise server processes.

Some processes may be mission-critical, while others may be run when resources are available.

Niceness controls Linux's priority.

Priority jobs don't share resources properly, thus they're less nice. Low priority operations take minimum resources, which is beneficial.

When you run top, there was a "NI" column. Process value:

```
# top 
```

```
[secondary_label Output]  
Tasks: 56 total, 1 running, 55 sleeping, 0 stopped, 0 zombie  
Cpu(s): 0.0%us, 0.3%sy, 0.0%ni, 99.7%id, 0.0%wa, 0.0%hi, 0.0%si, 0.0%st  
Mem: 1019600k total, 324496k used, 695104k free, 8512k buffers  
Swap: 0k total, 0k used, 0k free, 264812k cached

PID USER PR NI VIRT RES SHR S %CPU %MEM TIME+ COMMAND  
1635 root 20 0 17300 1200 920 R 0.3 0.1 0:00.01 top  
1 root 20 0 24188 2120 1300 S 0.0 0.2 0:00.56 init  
2 root 20 0 0 0 0 S 0.0 0.0 0:00.00 kthreadd  
3 root 20 0 0 0 0 S 0.0 0.0 0:00.11 ksoftirqd/0  

```

Depending on the system, nice values can range from -19/-20 to 19/20.

The nice command runs a programme with a specified lovely value.

```
# nice -n 15 command_to_execute 
```

Only fresh programmes function.

To change a program's nice value, use renice:  
```
# renice 0 PID_to_prioritize 
```

Hopefully you have understand the above steps how to use the ps, kill, and nice commands:

Must read:- [https://utho.com/docs/tutorial/linux-port-test-commandsredhat-7-centos-7-and-ubuntu-18-04/](https://utho.com/docs/tutorial/linux-port-test-commandsredhat-7-centos-7-and-ubuntu-18-04/)

**Thankyou**
