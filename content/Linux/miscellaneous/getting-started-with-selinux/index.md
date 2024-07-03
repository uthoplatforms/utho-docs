---
title: "Getting Started with SELinux"
date: "2020-03-27"
title_meta: "Getting Started with SELinux"
description: "SELinux( Security Enhanced Linux) is a security system which is developed by the United States National Security Agency for an additional layer of system security. Selinux was integrated in 2003 with linux kernel it also ships with most of the Linux distributions."
keywords:  ['SELinux', 'Linux']
tags: ["SELinux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/miscellaneous/getting-started-with-selinux']
---

![](images/selinux-1024x576.jpg)

## Introduction to SELinux

SELinux( Security Enhanced Linux) is a security system which is developed by the United States National Security Agency for an additional layer of system security. Selinux was integrated in 2003 with linux kernel it also ships with most of the Linux distributions.

The standard access policy based on the users, groups, and other permissions, known as Discretionary Access Control (DAC). However, SELinux implements Mandatory Access Control (MAC).

- **Discretionary access control** (DAC) does not enable system administrators to develop detailed and fine-grained security policies, such as limiting existing applications to viewing only log files, while allowing additional applications to add new data to log files
- **Mandatory Access Control** (MAC) act upon SELinux context. This security policy is centrally controlled by a security policy administrator, users do not have the ability to override the policy even if they have root privileges.

Ideally, it should be trusted by the person with root access. However, if safety was affected, the system was affected too. SELinux and MACs solve this problem both by confining preferential processes and by automating the creation of security policies.

SELinux denies anything not explicitly permitted by default. SELinux has two global modes, **Enforcement** and **Permissive** modes. Permissive mode enables the system to operate as a discretionary access control system while logging any SELinux violation. The mode of enforcement imposes a strict denial of access to anything not explicitly permitted. You, as the system administrator, must write policies to specifically allow certain behavior on a machine.

\[ht\_message mstyle="alert" title="NOTE" " show\_icon="true" id="" class=""style="" \]You are not advised to disable SELinux. However, if you want you can disable SELinux.\[/ht\_message\]

## Before You Begin

- SELinux is a security control system, which can make your system vulnerable by a small misconfiguration.
- Upgrade the packages of your system

```
# yum update
```

## Installation of SELinux on Centos 7

Command to see the default packages of SELinux:

```
#`[root@Microhost ~]# rpm -aq | grep selinux`
```

The output would be shown as given below:

```
#`[root@Microhost ~]# rpm -aq | grep selinux`  
libselinux-python-2.5-14.1.el7.x86_64  
libselinux-2.5-14.1.el7.x86_64  
libselinux-utils-2.5-14.1.el7.x86_64 
```

Install the SELinux packages with the command given below :

```
# [root@Microhost ~]# `yum install policycoreutils policycoreutils-python selinux-policy selinux-policy-targeted libselinux-utils setools setools-console` 
```

You can install some optional daemons of SELinux, They are **Setroubleshoot-servers** and **mctrans**. Among other things, the setroubleshoot-server allows e-mail notifications from the server to notify you of any breach of policy. The mctrans daemon translates the SELinux output to the human-readable text.

## Modes of SELinux

SELinux offers two modes : `**Enforcing**` and `**Permissive**`:

- **Enforcing**: In **Enforcing** mode, SELinux implements strict system policies. Things would not be permitted under any circumstances if they are not allowed.

- **Permissive**: In **Permissive** mode, Your system is not SELinux-protected instead, SELinux only records any violations into the log file.

You can check the mode of SELinux by the command:

```
[root@Microhost ~]# getenforce
 Enforcing
```

You can enable the SELinux if it is disabled as shown below:

```
[root@Microhost ~]# getsebool
getsebool:  SELinux is disabled
```

You can enable the SELinux in any mode( **Enforcing** | **Permissive** ) by editing the file /etc/selinux/config as shown below:

```
[root@Microhost ~]# vi /etc/selinux/config
 
```
This file controls the state of SELinux on the system.
SELINUX= can take one of these three values:
    enforcing - SELinux security policy is enforced.
    permissive - SELinux prints warnings instead of enforcing.
    disabled - No SELinux policy is loaded.
```SELINUX=disabled
```
SELINUXTYPE= can take one of three two values:
    targeted - Targeted processes are protected,
    minimum - Modification of targeted policy. Only selected processes are protected.
    mls - Multi Level Security protection.
```SELINUXTYPE=targeted


~                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       
"/etc/selinux/config" 14L, 546C
```

You have to change the line SELINUX=disabled to SELINUX=permissive or SELINUX=Enforcing as per your requirement. More information can also be obtained by using **sestatus** :

```
[root@Microhost ~]# sestatus
SELinux status:                 enabled
SELinuxfs mount:                /sys/fs/selinux
SELinux root directory:         /etc/selinux
Loaded policy name:             targeted
Current mode:                   enforcing
Mode from config file:          permissive
Policy MLS status:              enabled
Policy deny_unknown status:     allowed
Max kernel policy version:      28
```

You need to set SELinux to permissive mode to implement policies on your system. You need to restart your system after changing SELinux mode.

```
[root@Microhost ~]# setenforce 0
[root@Microhost ~]# getenforce
Permissive
[root@Microhost ~]# reboot
```

Permissive mode has been enebled for SELinux,you can see the log of privacy violations by using:

```
grep "selinux" /var/log/messages
```

The output will look much like that

```
[root@Microhost ~]# grep "selinux" /var/log/messages
li482-93 yum[4572]: Updated: selinux-policy-3.13.1-102.el7_3.16.noarch
li482-93 yum[4572]: Updated: selinux-policy-targeted-3.13.1-102.el7_3.16.noarch
li482-93 systemd: Removed slice system-selinux\x2dpolicy\x2dmigrate\x2dlocal\x2dchanges.slice.
li482-93 systemd: Stopping system-selinux\x2dpolicy\x2dmigrate\x2dlocal\x2dchanges.slice.
li482-93 systemd: Removed slice system-selinux\x2dpolicy\x2dmigrate\x2dlocal\x2dchanges.slice.
li482-93 systemd: Stopping system-selinux\x2dpolicy\x2dmigrate\x2dlocal\x2dchanges.slice.
li482-93 kernel: EVM: security.selinux
li482-93 kernel: EVM: security.selinux
li482-93 kernel: EVM: security.selinux
```

You can edit the file /etc/selinux/config to modify the default configuration of SELinux.

```
[root@Microhost ~]# vi /etc/selinux/config
```
This file controls the state of SELinux on the system.
SELINUX= can take one of these three values:
    enforcing - SELinux security policy is enforced.
    permissive - SELinux prints warnings instead of enforcing.
    disabled - No SELinux policy is loaded.
```SELINUX=permissive
```
SELINUXTYPE= can take one of three two values:
    targeted - Targeted processes are protected,
    minimum - Modification of targeted policy. Only selected processes are protected.
    mls - Multi Level Security protection.
```SELINUXTYPE=targeted
```

After changing the state of SELinux, reboot the machine/server for the changes to take effect.

## SELinux Boolean

A SELinux Boolean variable can be activated and disabled without reloading or recompiling a SELinux policy. Use the getsebool -a command to view the list of boolean variables. It's a long list, so that the results can be pipeed by grep

```
[root@Microhost ~]# getsebool -a | grep xdm
xdm_bind_vnc_tcp_port --> off
xdm_exec_bootloader --> off
xdm_sysadm_login --> off
xdm_write_home --> off
```

You can use the **setsebool** command to change the value of any variable. The you will use argument -P flag then setting will remain same after the reboot. You need to edit boolean policies variable if you want a service like openVPN to run unconfined on your system:

```
[root@Microhost ~]# getsebool -a  | grep "vpn"
openvpn_can_network_connect --> on
openvpn_enable_homedirs --> on
openvpn_run_unconfined --> off

[root@Microhost ~]# setsebool -P openvpn_run_unconfined ON

[root@Microhost ~]# getsebool -a  | grep "vpn"
openvpn_can_network_connect --> on
openvpn_enable_homedirs --> on
openvpn_run_unconfined --> on
```

Now, even in active enforcement mode, you can use OpenVPN . Set your system up and allow SELinux to protect your system.

```
[root@Microhost ~]# setenforce 1
[root@Microhost ~]# getenforce
Enforcing
```

## SELinux Contexts

Files and processes are marked with a SELinux context which provides additional information such as the user of SELinux, role, type and, optionally,a level. All this information is used when running SELinux to make decisions on access control. SELinux combines Role-Based Access Control, Type Enforcement , as well as Multileive Security (MLS).

Use the below given command to view the SELinux context of files and directories:

```
[root@Microhost ~]# ls -Z linux.txt
-rwxrw-r-- root root unconfined_u:object_r:user_home_t:s0      linux.txt
```

As per above output -Z context flag shows the SELinux security context of any file. Output `**unconfined_u**` is a user role .

Thank You ! :)
