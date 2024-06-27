---
title: "How to Execute a Command with a Timeout in Linux"
date: "2022-11-04"
title_meta: "How to Execute a Command with a Timeout in Linux"
description: "How to Execute a Command with a Timeout in Linux"
keywords:  ['linux', 'timeout']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/how-to-execute-a-command-with-a-timeout-in-linux']
---

<figure>

![How to Execute a Command with a Timeout in Linux](images/How-to-Execute-a-Command-with-a-Timeout-in-Linux-1024x576.png)

<figcaption>

How to Execute a Command with a Timeout in Linux

</figcaption>

</figure>

In this tutorial, we will see how to execute a command with a timeout in Linux because Linux has a multiplicity of commands, each of which serves a specific function and may be run only under specified conditions. Linux is intended to help users be as efficient and successful as possible. One of the features of a Linux command is its maximum execution time. You have the power to limit the duration of any command you wish. The order will no longer be carried out when the time limit has expired.

## 1 - Timeout utility

In Linux, you may perform a command with a time limit since the operating system has a command-line capability called a timeout.

An example of its syntax is as follows:

```
timeout [OPTION] DURATION COMMAND [ARG]...
```

To use the command, you must give a timeout value in seconds along with the command that you want to perform. For example, to timeout a ping command after 5 seconds, use the following command.

```
timeout 3s ping google.com
```
You are not required to provide the number(s) that come after 3. The command shown below has not been modified and will continue to work correctly.

```
timeout 3 ping google.com
```
Even after the timeout has sent the first signal, the instructions may continue to execute in certain circumstances. The —kill-after option is available for usage in situations like these.

The syntax is as follows.

```
-k, --kill-after=DURATION
```

You must give timeout with a period so that it knows after how much time the kill signal will be sent.

For instance, the command being shown will be terminated after 10 seconds.

```
timeout 10 tail -f /var/log/syslog
```
## 2- Timelimit Program

When a particular length of time has passed, the Timelimit programme performs a given instruction and then terminates the operation by delivering a supplied signal. It starts by providing a warning signal, then after a set period of time, it activates the death signal.

In contrast to the timeout option, the timelimit option gives users with additional options such as killsig, warnsig, killtime, and warntime.

Timelimit may be found in the repositories of Debian-based operating systems, and the following command can be used to install it on your system.

```
# apt install timelimit 
```

After the installation is complete, execute the following command and give the time. In this instance, you are permitted to use 5 seconds.

```
# timelimit -t5 ping google.com 
```

Note that if you do not provide any parameters, Timelimit will use the default values: warntime=3600 seconds, warnsig=15, killtime=120, and killsig=9.

In this tutorial, you have learnt how to execute a command with a timeout

**Also Read**: [How to Install Xrdp Server on Ubuntu 22.04](https://utho.com/docs/tutorial/how-to-install-xrdp-server-on-ubuntu-22-04/), [How to Install Node.js and npm on Ubuntu 20.04](https://utho.com/docs/tutorial/how-to-install-node-js-and-npm-on-ubuntu-20-04/)[](https://www.tecmint.com/wp-content/uploads/2019/11/Set-Time-Limit-to-Commands.png)
