---
slug: Ansible-one-off-commands
description: 'In this guide, you'll explore various Ansible ad-hoc commands utilized by system and DevOps engineers for swift task execution and playbook administration'
keywords: ["ansible", "commands", "adhoc", "ansible adhoc commands"]
published: 2024-05-16
modified_by:
  name: Utho
title: 'A Tutorial for Learning Adhoc Commands in Ansible'
title_meta: 'Ansible Adhoc Commands - A Tutorial'
tags: ["automation"]
authors: ["Pawan Kumar"]
---

In this tutorial, you'll explore various Ansible ad-hoc commands commonly used by system and DevOps engineers.

Ad-hoc commands are executed directly from the command line, outside of a playbook. These commands run on one or more managed nodes and perform quick and simple tasks, often ones that don't require repetition. For instance, reloading Apache across a web server cluster can be accomplished with a single ad-hoc command.

Note: In Ansible, all modules can be executed either within a playbook or via an ad-hoc command.

The basic syntax for invoking an ad-hoc command is:

                ansible host_pattern -m module_name -a "module_options"

## Before You Begin

To execute the commands outlined in this tutorial, you'll require:

A workstation or server with the Ansible command-line tool installed, serving as the control node. Refer to the [Set Up the Control Node](/docs/guides/getting-started-with-ansible/#set-up-the-control-node) section of the [Getting Started With Ansible](/docs/guides/getting-started-with-ansible/) guide for instructions on configuring a utho as a control node. For installation guidelines on non-Linux distributions, consult the [Ansible documentation site](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html).

At least one additional server managed by Ansible. Some commands in this guide will target a non-root user on this server, who should possess sudo privileges. You have a couple of options for setting up this user:

You can utilize Ansible to create the user, as detailed in the [Add a Limited User Account](/docs/guides/running-ansible-playbooks/#add-a-limited-user-account) section of the [Automate Server Configuration with Ansible Playbooks](/docs/guides/running-ansible-playbooks/) guide.

Alternatively, you can manually add the user, as explained in the [Add a Limited User Account](/docs/products/compute/compute-instances/guides/set-up-and-secure/#add-a-limited-user-account) section of the [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide.

Note: Refer to the Creating a Compute Instance guide for assistance in creating uthos.

The commands provided in this guide will be executed from the control node and will target a host named `Client`. Ensure that your control node's Ansible inventory is configured to include at least one managed node with this name. For instructions on setting up an inventory file, consult the [Create an Ansible Inventory](/docs/guides/getting-started-with-ansible/#create-an-ansible-inventory) section of the Getting Started With Ansible guide.

Note: Alternatively, you can adjust the commands in this guide to utilize a different host name.

## Basic Commands

### Ping

To verify connectivity to your managed node, utilize the [`ping` module](https://docs.ansible.com/ansible/latest/modules/ping_module.html):

             ansible -m ping Client

             {{< output >}}
             node1 | SUCCESS => {
             "ansible_facts": {
             "discovered_interpreter_python": "/usr/bin/python"
              },
             "changed": false,
             "ping": "pong"
              }
             {{< /output >}}

### Run with Privilege Escalation

This ad-hoc command illustrates how a non-root user on the managed node can attain root user privileges when executing a module. Specifically, this example demonstrates using [privilege escalation](https://docs.ansible.com/ansible/latest/user_guide/become.html) to execute the `fdisk` command via the [`shell` module](https://docs.ansible.com/ansible/latest/modules/shell_module.html):

              ansible Client -m shell -a 'fdisk -l' -u non_root_user --become -K

             {{< output >}}
             BECOME password:
             node1 | CHANGED | rc=0 >>
             Disk /dev/sda: 79.51 GiB, 85362475008 bytes, 166723584 sectors
             Disk model: QEMU HARDDISK
             Units: sectors of 1 * 512 = 512 bytes
             Sector size (logical/physical): 512 bytes / 512 bytes
             I/O size (minimum/optimal): 512 bytes / 512 bytes
             Disk /dev/sdb: 512 MiB, 536870912 bytes, 1048576 sectors
             Disk model: QEMU HARDDISK
             Units: sectors of 1 * 512 = 512 bytes
             Sector size (logical/physical): 512 bytes / 512 bytes
             I/O size (minimum/optimal): 512 bytes / 512 bytes
             {{< /output >}}

The `-u` option specifies the user on the managed node.

Note: By default, Ansible attempts to establish a connection to the managed node using the same user as the one executing the Ansible CLI on the control node.

The `--become` option is employed to execute the command with root user privileges.
The `-K` option prompts for the privilege escalation password of the user.

### Reboot a Managed Node

Below is a command to reboot the managed node:

                ansible Client -a "/sbin/reboot" -f 1

This command does not include the -m option, which specifies the module. When the module is not specified, the default module used is the [`command` module](https://docs.ansible.com/ansible/latest/modules/command_module.html).

The `command` module operates similarly to the `shell` module in that both execute a command provided to them. However, the shell module executes the command through a shell on the managed node, while the command module does not.

Note: The `-f` option is utilized to set the number of [forks](https://docs.ansible.com/ansible/latest/user_guide/playbooks_strategies.html#setting-the-number-of-forks) Ansible will use on the control node when executing your command.

Note: If your managed node is a utho, enabling utho's shutdown watchdog Lassie is necessary for the reboot to succeed. This is because a utho cannot power itself on; instead, utho's host environment must initiate the utho's boot process.

## Collecting System Diagnostics

### Check Free Disk Space

This command retrieves information about the free disk space on all mounted disks of a managed node. It displays details such as filesystem size, space used, and available space in a human-readable format.

           ansible Client -a "df -h"

                 {{< output >}}
                           node1 | CHANGED | rc=0 >>
                           Filesystem      Size  Used Avail Use% Mounted on
                           udev            1.9G     0  1.9G   0% /dev
                           tmpfs           394M  596K  394M   1% /run
                           /dev/sda         79G  2.6G   72G   4% /
                           tmpfs           2.0G  124K  2.0G   1% /dev/shm
                           tmpfs           5.0M     0  5.0M   0% /run/lock
                           tmpfs           2.0G     0  2.0G   0% /sys/fs/cgroup
                           tmpfs           394M     0  394M   0% /run/user/0
                 {{< /output >}}

To check available and used space on a specific filesystem:

                   ansible Client -m shell -a 'df -h /dev/sda'

                {{< output >}}
                        node1 | CHANGED | rc=0 >>
                        Filesystem      Size  Used Avail Use% Mounted on
                        /dev/sda         79G  2.6G   72G   4% /
                {{< /output >}}

### Check Memory and CPU Usage

Utilize the `free` command with the `shell` module to view the free and used memory of your managed node in megabytes.

           ansible Client -m shell -a 'free -m'

            {{< output >}}
            node1 | CHANGED | rc=0 >>
                      total        used        free      shared  buff/cache   available
        Mem:           3936         190        3553           0         192        3523
        Swap:           511           0         511
           {{< /output >}}

Employ the `mpstat` command with the `shell` module to monitor CPU usage.

          ansible Client -m shell -a 'mpstat -P ALL'

        {{< output >}}
              node1 | CHANGED | rc=0 >>
               Linux 5.3.0-40-generic (localhost)      03/21/2020      _x86_64_        (2 CPU)

          07:41:27 PM  CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
          07:41:27 PM  all    0.96    0.00    0.72    0.08    0.00    0.02    0.01    0.00    0.00   98.21
          07:41:27 PM    0    0.93    0.00    0.73    0.06    0.00    0.03    0.01    0.00    0.00   98.24
          07:41:27 PM    1    1.00    0.00    0.71    0.09    0.00    0.01    0.01    0.00    0.00   98.17
       {{< /output >}}

### Check System Uptime

This Ansible command displays the uptime of your managed nodes.

            ansible Client -a "uptime"

          {{< output >}}
             node1 | CHANGED | rc=0 >>
             19:40:11 up 8 min,  2 users,  load average: 0.00, 0.02, 0.00
          {{< /output >}}

## File Transfer

### Copy Files

The [`copy` module](https://docs.ansible.com/ansible/latest/modules/copy_module.html) facilitates transferring a file or directory from the control node to managed nodes by specifying the source and destination paths. Ownership and permissions of the file can also be defined within the command.

           cd ~
           echo "Hello World" > test.txt
            ansible Client -m copy -a 'src=test.txt dest=/etc/ owner=root mode=0644' -u non_root_user --become -K

             {{< output >}}
             BECOME password:
             node1 | CHANGED => {
              "ansible_facts": {
              "discovered_interpreter_python": "/usr/bin/python"
               },
               "changed": true,
               "checksum": "13577023221e91069c21d8f10a4b90f8192d6a26",
               "dest": "/etc/test",
               "gid": 0,
               "group": "root",
               "md5sum": "eb662c21e683b643f0fcb5997d7bbccf",
               "mode": "0644",
               "owner": "root",
               "size": 18,
               "src": "/root/.ansible/tmp/ansible-tmp-1584820375.14-54524496813834/source",
               "state": "file",
               "uid": 0
               }
              {{< /output >}}

You can verify if your file was successfully copied to the destination location using Ansible.

               sudo ansible Client -m shell -a 'ls -l /etc/test*'

             {{< output >}}
             node1 | CHANGED | rc=0 >>
             -rw-r--r-- 1 root root 12 Jun  1 22:35 /etc/test.txt
             {{< /output >}}

### Fetch Files

The [`fetch` module](https://docs.ansible.com/ansible/latest/modules/fetch_module.html) is used to transfer a file from a managed node to the control node. Upon successful execution, the `changed` variable in Ansible's output is set to `true`.

             ansible Client -m fetch -a 'src=/etc/test.txt dest=/etc/'

             {{< output >}}
             node1 | CHANGED => {
             "changed": true,
             "checksum": "648a6a6ffffdaa0badb23b8baf90b6168dd16b3a",
             "dest": "/etc/192.0.2.4/etc/test.txt",
             "md5sum": "e59ff97941044f85df5297e1c302d260",
             "remote_checksum": "648a6a6ffffdaa0badb23b8baf90b6168dd16b3a",
             "remote_md5sum": null
              }
             {{< /output >}}

Note that fetched files are placed into separate directories for each hostname by default, preventing files from overwriting each other.

To prevent directory creation, include the `flat=yes` option.

             ansible Client -m fetch -a 'src=/etc/test.txt dest=/etc/ flat=yes'

           {{< output >}}
            node1 | SUCCESS => {
            "changed": false,
            "checksum": "648a6a6ffffdaa0badb23b8baf90b6168dd16b3a",
            "dest": "/etc/test.txt",
            "file": "/etc/test.txt",
            "md5sum": "e59ff97941044f85df5297e1c302d260"
             }
             {{< /output >}}

### Create Directories

The [`file` module](https://docs.ansible.com/ansible/latest/modules/file_module.html) facilitates creating, removing, and setting permissions on files and directories, as well as creating symlinks. This command creates a directory at `/root/utho/new/` on the managed node with specified owner and permissions.

             ansible Client -m file -a "dest=/root/utho/new/ mode=755 owner=root group=root state=directory" -u non_root_user --become -K

                 {{< output >}}
                        node1 | CHANGED => {
                        "ansible_facts": {
                        "discovered_interpreter_python": "/usr/bin/python"
                         },
                        "changed": true,
                        "gid": 0,
                        "group": "root",
                        "mode": "0755",
                        "owner": "root",
                        "path": "/root/utho/new",
                        "size": 4096,
                        "state": "directory",
                        "uid": 0
                         }
                        {{< /output >}}

Note that all intermediate directories that do not exist will also be created.

## Managing Packages

### Install a Package

The [`package` module](https://docs.ansible.com/ansible/latest/modules/package_module.html) installs a new package on the managed node. This command installs the latest version of NGINX.

                    ansible Client -m package -a 'name=nginx state=present' -u non_root_user --become -K

                {{< output >}}
                     node1 | CHANGED => {
                     "ansible_facts": {
                     "discovered_interpreter_python": "/usr/bin/python"
                      },
                     "cache_update_time": 1584821061,
                     "cache_updated": false,
                     "changed": true,
                     "stderr": "",
                     "stderr_lines": [],
                     "stdout": "Reading package lists...\nBuilding dependency tree...
                     "Unpacking nginx (1.16.1-0ubuntu2.1) ...",
                     "Setting up libxpm4:amd64 (1:3.5.12-1) ...",
                     "Setting up nginx-common (1.16.1-0ubuntu2.1) ...",
                     "Setting up nginx-core (1.16.1-0ubuntu2.1) ...",
                     "Setting up nginx (1.16.1-0ubuntu2.1) ...",
                    ]
                    }
                 {{< /output >}}

Note: The `package` module is cross-distribution compatible. However, specific package manager modules (e.g., [`apt` module](https://docs.ansible.com/ansible/latest/modules/apt_module.html) and [`yum` module](https://docs.ansible.com/ansible/latest/modules/yum_module.html)) offer more options tailored to those package managers.

### Uninstall a Package

To remove a package, specify `state=absent` in the command's options:

             ansible Client -m package -a 'name=nginx state=absent' -u non_root_user --become -K

             {{< output >}}
                 node1 | CHANGED => {
                    "ansible_facts": {
                    "discovered_interpreter_python": "/usr/bin/python"
                     },
                    "changed": true,
                    "stderr": "",
                    "stderr_lines": [],
                    "stdout": "Reading package lists...\nBuilding dependency tree …
                    "  nginx-core",
                    "Use 'sudo apt autoremove' to remove them.",
                    "The following packages will be REMOVED:",
                    "  nginx*",
                    "Removing nginx (1.16.1-0ubuntu2.1) ..."
                    ]
                   }
             {{< /output >}}

## Managing Services

### Start a Service

Utilize the [`service` module](https://docs.ansible.com/ansible/latest/modules/service_module.html) to initiate a service on the managed node. This command will start and enable the NGINX service:



                      ansible Client -m service -a 'name=nginx state=started enabled=yes' -u non_root_user --become -K

              {{< output >}}
                 node1 | SUCCESS => {
                  "ansible_facts": {
                  "discovered_interpreter_python": "/usr/bin/python"
                   },
                   "changed": false,
                   "enabled": true,
                   "name": "nginx",
                   "state": "started",
                   "status": {
                   "ActiveEnterTimestamp": "Sat 2020-03-21 20:04:35 UTC",
                   "ActiveEnterTimestampMonotonic": "1999615481",
                   "ActiveExitTimestampMonotonic": "0",
                   "ActiveState": "active",
                   "After": "system.slice systemd-journald.socket network.target sysinit.target basic.target",
                   "AllowIsolate": "no",
                   "AmbientCapabilities": "",
                   "AssertResult": "yes",
                   "AssertTimestamp": "Sat 2020-03-21 20:04:35 UTC",
                   "AssertTimestampMonotonic": "1999560256",
                   "Before": "multi-user.target shutdown.target",
                   }
                   }
                   {{< /output >}}

### Stop a Service

Changing the state to stopped will halt the service.

                ansible Client -m service -a 'name=nginx state=stopped' -u non_root_user --become -K

                {{< output >}}
                   node1 | CHANGED => {
                       "ansible_facts": {
                       "discovered_interpreter_python": "/usr/bin/python"
                         },
                       "changed": true,
                       "name": "nginx",
                       "state": "stopped",
                       "status": {
                       "ActiveEnterTimestamp": "Sat 2020-03-21 20:04:35 UTC",
                      "ActiveEnterTimestampMonotonic": "1999615481",
                      "ActiveExitTimestampMonotonic": "0",
                      "ActiveState": "active",
                      "After": "system.slice systemd-journald.socket network.target sysinit.target basic.target",
                      "AllowIsolate": "no",
                      "AmbientCapabilities": "",
                      "AssertResult": "yes",
                      "AssertTimestamp": "Sat 2020-03-21 20:04:35 UTC",
                       }
                       }
                      {{< /output >}}

## Gathering Facts

The [`setup` module](https://docs.ansible.com/ansible/latest/modules/setup_module.html) collects information about your managed nodes:

                      ansible Client -m setup

                      {{< output >}}
                           node1 | SUCCESS => {
                              "ansible_facts": {
                              "ansible_all_ipv4_addresses": [
                                                  "192.0.2.2"
                                                           ],
                             "ansible_all_ipv6_addresses": [
                            "2400:8904::f03c:92ff:fee9:dcb3",
                                 "fe80::f03c:92ff:fee9:dcb3"
                                             ],
                                "ansible_apparmor": {
                                  "status": "enabled"
                                      },
                            "ansible_architecture": "x86_64",
                            "ansible_bios_date": "04/01/2014",
                            "ansible_bios_version": "rel-1.12.0-0-ga698c8995f-prebuilt.qemu.org",
                            "ansible_cmdline": {
                            "BOOT_IMAGE": "/boot/vmlinuz-5.3.0-40-generic",
                            "console": "ttyS0,19200n8",
                            "net.ifnames": "0",
                            "ro": true,
                            "root": "/dev/sda"
                             },
                            "ansible_date_time": {
                            "date": "2020-03-21",
                            "day": "21",
                            "epoch": "1584821656",
                            "hour": "20",
                            "iso8601": "2020-03-21T20:14:16Z",
                            "iso8601_basic": "20200321T201416267047",
                            "iso8601_basic_short": "20200321T201416",
                            "iso8601_micro": "2020-03-21T20:14:16.267127Z",
                            "minute": "14",
                            "month": "03",
                            "second": "16",
                            "time": "20:14:16",
                            "tz": "UTC",
                            "tz_offset": "+0000",
                            "weekday": "Saturday",
                            "weekday_number": "6",
                            "weeknumber": "11",
                            "year": "2020"
                             },
                             "ansible_default_ipv4": {
                             "address": "192.0.2.2",
                             "alias": "eth0",
                             "broadcast": "192.0.2.254",
                             "gateway": "192.0.2.2",
                             "interface": "eth0",
                             "macaddress": "f2:3c:92:e9:dc:b3",
                             "mtu": 1500,
                             "netmask": "255.255.255.0",
                             "network": "192.0.2.1",
                             "type": "ether"
                              },
                              "gather_subset": [
                              "all"
                              ],
                              "module_setup": true
                              },
                             "changed": false
                              }
                             {{< /output >}}

### Filtering Facts

By using the `filter` option with the `setup` module, you can restrict the output of the module. This command displays the details of the installed distributions on your managed nodes:

               ansible Client -m setup -a "filter=ansible_distribution*"

                         {{< output >}}
                             node1 | SUCCESS => {
                             "ansible_facts": {
                              "ansible_distribution": "Ubuntu",
                              "ansible_distribution_file_parsed": true,
                              "ansible_distribution_file_path": "/etc/os-release",
                              "ansible_distribution_file_variety": "Debian",
                              "ansible_distribution_major_version": "19",
                              "ansible_distribution_release": "eoan",
                              "ansible_distribution_version": "19.10",
                              "discovered_interpreter_python": "/usr/bin/python"
                                },
                             "changed": false
                             }
                          {{< /output >}}
