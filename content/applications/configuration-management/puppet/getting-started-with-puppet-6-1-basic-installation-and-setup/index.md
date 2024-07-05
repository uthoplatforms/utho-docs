---
slug: "Master Puppet 6.1: Essential Installation and Setup Guide"
description: 'Step-by-Step Guide to Setting Up and Configuring a Puppet Master and Agents on Ubuntu and CentOS Servers'
keywords: ["puppet installation", "configuration change management", "server automation"]
tags: ["ubuntu","automation","centos"]
modified: 2024-05-21
modified_by:
    name: Utho
published: 2024-05-21
title: Getting Started with Puppet - Installation and Setup
authors: ["Utho"]
---

Puppet is your go-to solution for simplifying system administration through configuration management. It operates on a client/server model, where managed nodes, represented by the Puppet agent, communicate with and receive configuration profiles from a Puppet master.


<!--
Tnis graphic doesn't have the same title as the new title for this doc.

-->

Whether you're managing a handful of servers or orchestrating enterprise-level operations, Puppet has got you covered. This guide will walk you through installing Puppet 6.1 on three servers:


- A Puppet master running Ubuntu 18.04

- A managed Puppet node running Ubuntu 18.04

- A managed Puppet node running CentOS 7

Following the installation, the subsequent section will guide you through securing these servers using Puppet. Here, you'll delve into the fundamental features of the Puppet language.

Note: Before proceeding, it's typically recommended to follow the Setting Up and Securing a Compute Instance guide. However, in this guide, Puppet will handle this task, so you should start as the root user. Later steps will configure a limited user with administrative privileges using Puppet.

## Before You Begin

Below is a table illustrating example system information for the servers covered in this guide:

| Description       | OS | Hostname | FQDN | IP |
| ----------------- | ----------- | ----- | ----- | ---- |
| Puppet master     | Ubuntu 18.04 | puppet | puppet.example.com | 192.0.2.2
| Node 1 (Ubuntu)   | Ubuntu 18.04 | puppet-agent-ubuntu |puppet-agent-ubuntu.example.com | 192.0.2.3 |
| Node 2 (CentOS)   | CentOS 7     | puppet-agent-centos | puppet-agent-centos.example.com | 192.0.2.4 |

Feel free to customize the hostnames and fully qualified domain names (FQDN) for your servers. Additionally, ensure that the IP addresses for your servers are distinct from the example addresses listed. You'll need a registered domain name to specify FQDNs for your servers.

Throughout this guide, commands and code snippets will reference the values displayed in this table. Whenever you encounter such values, replace them with your own configurations.

### Create your Uthos

Create three Uthos corresponding to the servers listed in the table above. Your Puppet master Utho should have at least four CPU cores; the Utho 8GB plan is recommended. The two other nodes can be of any plan size, depending on how you intend to use them after Puppet is installed and configured.

Configure your timezone on your master and agent nodes to ensure they all have the same time data.

Set the hostname for each server.

Set the FQDN for each Utho by editing the servers' /etc/hosts files.

Note: You can model the contents of your /etc/hosts files on these snippets:

    ```file {title="Master"}
    127.0.0.1	localhost
    192.0.2.2   puppet.example.com puppet

    # The following lines are desirable for IPv6 capable hosts
    ::1     localhost ip6-localhost ip6-loopback
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters
    ```

    ```file {title="Node 1 (Ubuntu)"}
    127.0.0.1	localhost
    192.0.2.3   puppet-agent-ubuntu.example.com puppet-agent-ubuntu

    # The following lines are desirable for IPv6 capable hosts
    ::1     localhost ip6-localhost ip6-loopback
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters
    ```

    ```file {title="Node 2 (CentOS)"}
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    192.0.2.4   puppet-agent-centos.example.com puppet-agent-centos
    ```

Set up DNS records for your Uthos' FQDNs. For each Utho, create a new A record with the name specified by its FQDN and assign it to that Utho's IP address.

If you don't use Utho's name servers for your domain, consult your name server authority's website for instructions on how to edit your DNS records.

       {{< content "update-dns-at-common-name-server-authorities" >}}

## Puppet Master

### Install the Puppet Server Software

The Puppet master runs the `puppetserver` service, which is responsible The Puppet master runs the puppetserver service, which compiles and provides configuration profiles to your managed nodes.

The puppetserver service depends on the Puppet agent service (referred to as puppet when running on your system). This means the agent software will also be installed and can run on your master, allowing you to configure the master with Puppet just like any other managed node.

Log in to your Puppet master via SSH as root:

        ssh root@puppet.example.com

Download the Puppet repository, update your system packages, and install puppetserver:

        wget https://apt.puppet.com/puppet-release-bionic.deb
        dpkg -i puppet-release-bionic.deb
        apt update
        apt install puppetserver

### Configure the Server Software

Use the puppet config command to set values for the dns_alt_names setting:

        /opt/puppetlabs/bin/puppet config set dns_alt_names 'puppet,puppet.example.com' --section main

If you inspect the configuration file, you'll see that the setting has been added.

           cat /etc/puppetlabs/puppet/puppet.conf

              {{< output >}}
               [main]
               dns_alt_names = puppet,puppet.example.com
               # ...
             {{< /output >}}

Note: The puppet command is not added to your PATH by default. To use Puppet's interactive commands without specifying the full path, update your PATH for your current shell session:

           export PATH=/opt/puppetlabs/bin:$PATH

Note: A more permanent solution would be to add this to your .profile or .bashrc files.

Update your Puppet master's /etc/hosts file to resolve your managed nodes' IP addresses. For example, your /etc/hosts file might look like this:

             {{< file "/etc/hosts" >}}
            127.0.0.2   localhost
            192.0.2.1   puppet.example.com puppet
            192.0.2.2   puppet-agent-ubuntu.example.com puppet-agent-ubuntu
            192.0.2.3   puppet-agent-centos.example.com puppet-agent-centos

# The following lines are desirable for IPv6 capable hosts
           ::1     localhost ip6-localhost ip6-loopback
          ff02::1 ip6-allnodes
          ff02::2 ip6-allrouters
           {{< /file >}}

Note: This snippet includes the FQDN declarations mentioned in the Create your Uthos section.

Start and enable the puppetserver service:

             systemctl start puppetserver
             systemctl enable puppetserver

By default, the Puppet master listens for client connections on port 8140. If the puppetserver service fails to start, check if the port is already in use.

             netstat -anpl | grep 8140

## Puppet Agents

### Install Puppet Agent

On your managed node running Ubuntu 18.04, install the puppet-agent package:

        wget https://apt.puppet.com/puppet-release-bionic.deb
        dpkg -i puppet-release-bionic.deb
        apt update
        apt install puppet-agent

On your managed node running CentOS 7, install the puppet-agent package by entering:

        rpm -Uvh https://yum.puppet.com/puppet/puppet-release-el-7.noarch.rpm
        yum install puppet-agent

### Configure Puppet Agent

Modify your managed nodes' /etc/hosts files to resolve the Puppet master's IP. Add a line like this:

           {{< file "/etc/hosts" >}}
           192.0.2.1    puppet.example.com puppet
            {{< /file >}}

Note: You can model the contents of your managed nodes' /etc/hosts files on these snippets. These include the FQDN declarations mentioned in the Create your Uthos section.

    ```file {title="Node 1 (Ubuntu)"}
    127.0.0.1	localhost
    192.0.2.3   puppet-agent-ubuntu.example.com puppet-agent-ubuntu

    192.0.2.2   puppet.example.com puppet

    # The following lines are desirable for IPv6 capable hosts
    ::1     localhost ip6-localhost ip6-loopback
    ff02::1 ip6-allnodes
    ff02::2 ip6-allrouters
    ```

    ```file {title="Node 2 (CentOS)"}
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
    ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
    192.0.2.4   puppet-agent-centos.example.com puppet-agent-centos

    192.0.2.2   puppet.example.com puppet
    ```

On each managed node, set the server configuration to the FQDN of the master using the puppet config command:

        /opt/puppetlabs/bin/puppet config set server 'puppet.example.com' --section main

You can verify the configuration file on the nodes to see that the setting has been added.

         cat /etc/puppetlabs/puppet/puppet.conf

              {{< output >}}
              [main]
             server = puppet.example.com
             # ...
             {{< /output >}}

Start and enable the Puppet agent service using the puppet resource command:

             /opt/puppetlabs/bin/puppet resource service puppet ensure=running enable=true

Note: On systemd systems, this command is equivalent to using the following systemctl commands:

              systemctl start puppet
              systemctl enable puppet

### Generate and Sign Certificates

Before the managed nodes can receive configurations from the master, they need to be authenticated:

On your Puppet agents, generate a certificate for the Puppet master to sign:

        /opt/puppetlabs/bin/puppet agent -t

After running the command to generate the certificate on the Puppet agents, you'll likely encounter an error indicating that no certificate has been found. This occurs because the generated certificate requires approval from the Puppet master.

Log in to your Puppet master and list the certificates pending approval:

        /opt/puppetlabs/bin/puppetserver ca list

This command should output a list containing the hostnames of your agent nodes.

Approve the certificates for the agent nodes:

        /opt/puppetlabs/bin/puppetserver ca sign --certname puppet-agent-ubuntu.example.com,puppet-agent-centos.example.com

Return to the Puppet agent nodes and rerun the Puppet agent:

        /opt/puppetlabs/bin/puppet agent -t

You should see a message indicating that the Puppet run was successful.

         {{< output >}}
      Info: Downloaded certificate for hostname.example.com from puppet
      Info: Using configured environment 'production'
      Info: Retrieving pluginfacts
      Info: Retrieving plugin
      Info: Retrieving locales
      Info: Caching catalog for hostname.example.com
      Info: Applying configuration version '1547066428'
      Info: Creating state file /opt/puppetlabs/puppet/cache/state/state.yaml
      Notice: Applied catalog in 0.02 seconds
         {{< /output >}}

## Add Modules to Configure Agent Nodes

The Puppet master and agent nodes are operational, but they lack security measures. Drawing from the concepts outlined in the Setting Up and Securing a Compute Instance guide, it's essential to configure a limited user and a firewall on all nodes. This can be achieved by creating basic Puppet modules, as demonstrated below.

Please note that these configurations are not exhaustive and should be adapted to suit your specific security requirements. Puppet modules serve as a structured approach to organizing configuration code for various tasks, such as application installation and configuration. You have the option to develop custom modules or utilize pre-existing ones available on Puppet Forge.

### Add a Limited User

To establish a new limited user on your nodes, you'll develop and apply a new module named accounts, leveraging the user resource.

Start from the Puppet master and navigate to the /etc/puppetlabs/code/environments/production/modules directory. When a managed node requests its configuration from the master, the Puppet server process will search this location for your modules.

        cd /etc/puppetlabs/code/environments/production/modules/

Establish a directory for the new accounts module.

        mkdir accounts
        cd accounts

Inside the accounts module, create the following subdirectories:

        mkdir {examples,files,manifests,templates}

    | Directory | Description |
    | --------- | ----------- |
    | `manifests` | The Puppet code which powers the module |
    | `files` | Static files to be copied to managed nodes |
    | `templates` | Template files to be copied to managed nodes that can e customized with variables |
    | `examples` | Example code which shows how to use the module |

Note: Refer to Puppet's Module fundamentals article for insights into module structuring.

Move to the manifests directory.

        cd manifests

Any file containing Puppet code is referred to as a manifest, with each manifest ending in .pp. Within a module, a manifest should define only one class. If the module's manifests directory contains an init.pp file, the class definition within it is considered the main class. The class definition in init.pp should bear the same name as the module.

Create an init.pp file with the following content. Replace all occurrences of username with your desired username:

         {{< file "accounts/manifests/init.pp" puppet >}}
        class accounts {

         user { 'username':
         ensure      => present,
         home        => '/home/username',
         shell       => '/bin/bash',
         managehome  => true,
         gid         => 'username',
         }

         }
        {{< /file >}}

    | Option | Description |
    | --------- | ----------- |
    | `ensure` | Ensures that the user exists if set to `present`, or does not exist if set to `absent` |
    | `home` | The path for the user's home directory |
    | `managehome` | Controls whether a home directory should be created when creating the user |
    | `shell` | The path to the shell for the user |
    | `gid` | The user's primary group |

Although the class specifies the user's primary group, it doesn't create the group itself. Create a new file named groups.pp within the manifests directory, containing the following content. Substitute username with your chosen username:

         {{< file "accounts/manifests/groups.pp" puppet >}}
          class accounts::groups {

          group { 'username':
          ensure  => present,
           }

           }
         {{< /file >}}

Your accounts class can invoke your new accounts::groups class for utilization within the accounts class scope. Open init.pp in your editor and insert a new include declaration at the beginning of the class:

    {{< file "accounts/manifests/init.pp" puppet >}}
            class accounts {

           include accounts::groups

           # ...

           }

           {{< /file >}}

To grant administrative privileges to the new user, we need to ensure that it is in the appropriate group. Since we have agent nodes based on both Debian and Red Hat systems, the new user should be added to the sudo group on Debian systems and the wheel group on Red Hat systems.

This requirement can be fulfilled dynamically using Puppet facts. The facts system gathers system information about your nodes, making it accessible in your manifests.

Include a selector statement at the beginning of your accounts class:

          {{< file "accounts/manifests/init.pp" puppet >}}
            class accounts {

            $rootgroup = $osfamily ? {
            'Debian'  => 'sudo',
            'RedHat'  => 'wheel',
             default   => warning('This distribution is not supported by the Accounts module'),
             }

            include accounts::groups

             # ...

             }

             {{< /file >}}

This snippet defines the value for the $rootgroup variable by checking the value of $osfamily, one of Puppet's core facts. If the value for $osfamily does not match Debian or Red Hat, the default value will issue a warning that the selected distribution is not supported by this module.

Note: Puppet Configuration Language executes code from top to bottom. Since the user resource declaration references the $rootgroup variable, it must be defined before the user declaration.

Modify the user resource to include the groups option as follows:

           {{< file "accounts/manifests/init.pp" puppet >}}
            # ...

         user { 'username':
         ensure      => present,
         home        => '/home/username',
         shell       => '/bin/bash',
         managehome  => true,
         gid         => 'username',
         groups      => "$rootgroup",
         }

         # ...
         {{< /file >}}

The value "$rootgroup" is enclosed in double quotes " " instead of single quotes ' ' because it's a variable requiring interpolation in your code.

The last piece to add is the user's password. To avoid using plain text, the password should be provided to Puppet as a SHA1 digest, which is supported by default. Generate a digest with the openssl command:

               openssl passwd -1

You'll be prompted to input your password. A hashed password will be output. Copy this value to your clipboard.

Update the user resource to include the password option as follows; insert your copied password hash as the value for the option:

          {{< file "accounts/manifests/init.pp" puppet >}}
           # ...

             user { 'username':
             ensure      => present,
             home        => '/home/username',
             shell       => '/bin/bash',
             managehome  => true,
             gid         => 'username',
             groups      => "$rootgroup",
             password    => 'your_password_hash',
             }

             # ...
             {{< /file >}}

Note: The hashed password must be enclosed in single quotes ' '.

After saving your changes, utilize the Puppet parser to validate the code:

           /opt/puppetlabs/bin/puppet parser validate init.pp

Any errors that require attention will be logged to standard output. If no output is returned, your code is valid.

Navigate to the examples directory and create another init.pp file:

               cd ../examples

             {{< file "accounts/examples/init.pp" puppet >}}
              include accounts
               {{< /file >}}

While still in the examples directory, test the module:

             /opt/puppetlabs/bin/puppet apply --noop init.pp

Note: The --noop parameter prevents Puppet from applying the module to your system and making any changes.

It should return:

         {{< output >}}
        Notice: Compiled catalog for puppet.example.com in environment production in 0.26 seconds
        Notice: /Stage[main]/Accounts::Groups/Group[username]/ensure: current_value absent, should be present (noop)
        Notice: Class[Accounts::Groups]: Would have triggered 'refresh' from 1 events
        Notice: /Stage[main]/Accounts/User[username]/ensure: current_value absent, should be present (noop)
        Notice: Class[Accounts]: Would have triggered 'refresh' from 1 events
        Notice: Stage[main]: Would have triggered 'refresh' from 2 events
        Notice: Finished catalog run in 0.02 seconds
         {{< /output >}}

From the examples directory, run puppet apply to apply these changes to the Puppet master server:


        /opt/puppetlabs/bin/puppet apply init.pp

Puppet will create the limited Linux user on your master.

Log out as root and log back in to the Puppet master as your new user.

### Edit SSH Settings

Although a new limited user has been successfully added to the Puppet master, it is still possible to log in to the system as root. To properly secure your system, root access should be disabled.

Note: Since you are now logged in to the Puppet master as a limited user, you will need to execute commands and edit files with the user's sudo privileges.

Navigate to the files directory within the accounts module:

        cd /etc/puppetlabs/code/environments/production/modules/accounts/files

Copy your system's existing sshd_config file to this directory:

        sudo cp /etc/ssh/sshd_config .

Open the file in your editor (using sudo privileges) and set the PermitRootLogin value to no:

         {{< file "accounts/files/sshd_config" aconf >}}
         PermitRootLogin no

         {{< /file >}}

Navigate back to the manifests directory:

        cd ../manifests

Create a new manifest called ssh.pp. Use the file resource to replace the default SSH configuration file with one managed by Puppet:

         {{< file "accounts/manifests/ssh.pp" puppet >}}
         class accounts::ssh {

        file { '/etc/ssh/sshd_config':
        ensure  => present,
         source  => 'puppet:///modules/accounts/sshd_config',
          }

          }  
          {{< /file >}}

Note: The files directory is omitted from the source line because it is the default location for files within a module. For more details on accessing resources in a module, refer to the official Puppet module documentation.

Create a second resource to restart the SSH service and set it to run whenever sshd_config is changed. This will also require a selector statement because the SSH service is named ssh on Debian systems and sshd on Red Hat systems:

         {{< file "accounts/manifests/ssh.pp" puppet >}}
         class accounts::ssh {

          $sshname = $osfamily ? {
          'Debian'  => 'ssh',
          'RedHat'  => 'sshd',
           default   => warning('This distribution is not supported by the Accounts module'),
           }

         file { '/etc/ssh/sshd_config':
         ensure  => present,
         source  => 'puppet:///modules/accounts/sshd_config',
         notify  => Service["$sshname"],
          }

          service { "$sshname":
          hasrestart  => true,
          }

          }
         {{< /file >}}

Note: Notify is one of Puppet's relationship metaparameters. Include the accounts::ssh class within the accounts class in init.pp:

        {{< file "accounts/manifests/init.pp" puppet >}}
         class accounts {

          # ...

         include accounts::groups
         include accounts::ssh

         # ...

         }
       {{< /file >}}

The contents of your `init.pp` should now look like the following snippet:

          {{< file "accounts/manifests/init.pp" puppet >}}
           class accounts {

            $rootgroup = $osfamily ? {
            'Debian' => 'sudo',
            'RedHat' => 'wheel',
            default => warning('This distro not supported by Accounts module'),
             }

          include accounts::groups
          include accounts::ssh

          user { 'example':
          ensure  => present,
          home    => '/home/username',
          shell   => '/bin/bash',
          managehome  => true,
          gid     => 'username',
          groups  => "$rootgroup",
          password => 'your_password_hash'
          }

         }
         {{< /file >}}

Run the Puppet parser to test the syntax of the new class, then navigate to the examples directory to test and run the update to your accounts class:
        sudo /opt/puppetlabs/bin/puppet parser validate ssh.pp
        cd ../examples
        sudo /opt/puppetlabs/bin/puppet apply --noop init.pp
        sudo /opt/puppetlabs/bin/puppet apply init.pp

Note: You may see the following line in your output when validating:

         {{< output >}}
       Error: Removing mount "files": /etc/puppet/files does not exist or is not a directory
        {{< /output >}}

This refers to a Puppet configuration file, not the module resource you're trying to copy. If this is the only error in your output, the operation should still succeed.

To ensure that the ssh class is working properly, log out of the Puppet master and then try to log in as root. You should not be able to do so.

### Add and Configure IPtables

To complete this guide's security settings, the firewall needs to be configured on your Puppet master and nodes. The iptables firewall software will be used.

By default, changes to your iptables rules will not persist across reboots. To avoid this, install the appropriate package on your Puppet master and nodes:

    **Ubuntu/Debian**:

        sudo apt install iptables-persistent

    **CentOS 7**:

    CentOS 7 uses firewalld by default as a controller for iptables. Be sure firewalld is stopped and disabled before starting to work directly with iptables:

        sudo systemctl stop firewalld && sudo systemctl disable firewalld
        sudo yum install iptables-services

On your Puppet master, install Puppet Lab's firewall module from the Puppet Forge:

        sudo /opt/puppetlabs/bin/puppet module install puppetlabs-firewall

The module will be installed in your /etc/puppetlabs/code/environments/production/modules directory.

Navigate to the manifests directory inside the new firewall module:

        cd /etc/puppetlabs/code/environments/production/modules/firewall/manifests/

Create a file titled pre.pp, which will contain all basic networking rules that should be run first:

         {{< file "firewall/manifests/pre.pp" puppet >}}
          class firewall::pre {

          Firewall {
            require => undef,
              }

          # Accept all loopback traffic
          firewall { '000 lo traffic':
         proto       => 'all',
         iniface     => 'lo',
         action      => 'accept',
         }->

           #Drop non-loopback traffic
           firewall { '001 reject non-lo':
           proto       => 'all',
           iniface     => '! lo',
           destination => '127.0.0.0/8',
           action      => 'reject',
         }->

          #Accept established inbound connections
          firewall { '002 accept established':
          proto       => 'all',
          state       => ['RELATED', 'ESTABLISHED'],
          action      => 'accept',
         }->

         #Allow all outbound traffic
         firewall { '003 allow outbound':
         chain       => 'OUTPUT',
         action      => 'accept',
        }->

        #Allow ICMP/ping
        firewall { '004 allow icmp':
        proto       => 'icmp',
        action      => 'accept',
        }

        #Allow SSH connections
        firewall { '005 Allow SSH':
         dport    => '22',
         proto   => 'tcp',
         action  => 'accept',
       }->

        #Allow HTTP/HTTPS connections
        firewall { '006 HTTP/HTTPS connections':
        dport    => ['80', '443'],
        proto   => 'tcp',
        action  => 'accept',
        }

        }

        {{< /file >}}

In the same directory, create post.pp, which will run any firewall rules that need to be input last:

         {{< file "firewall/manifests/post.pp" puppet >}}
         class firewall::post {

         firewall { '999 drop all':
         proto  => 'all',
         action => 'drop',
         before => undef,
          }

          }

         {{< /file >}}

These rules will direct the system to drop all inbound traffic that is not already permitted in the firewall.

Run the Puppet parser on both files to check their syntax for errors:

            sudo /opt/puppetlabs/bin/puppet parser validate pre.pp
            sudo /opt/puppetlabs/bin/puppet parser validate post.pp

Navigate to the main manifests directory:

            cd /etc/puppetlabs/code/environments/production/manifests

Create a file named site.pp inside /etc/puppetlabs/code/environments/production/manifests. This file is the main manifest for the Puppet server service. It is used to map modules, classes, and resources to the nodes that they should be applied to.

           {{< file "site.pp" puppet >}}
            node default {

             }

           node 'puppet.example.com' {

           include accounts

           resources { 'firewall':
           purge => true,
           }

           Firewall {
           before        => Class['firewall::post'],
           require       => Class['firewall::pre'],
           }

          class { ['firewall::pre', 'firewall::post']: }

         firewall { '200 Allow Puppet Master':
           dport         => '8140',
            proto         => 'tcp',
            action        => 'accept',
                }

                }
             {{< /file >}}

Run the site.pp file through the Puppet parser to check its syntax for errors. Then, test the file with the --noop option to see if it will run:

               sudo /opt/puppetlabs/bin/puppet parser validate site.pp
               sudo /opt/puppetlabs/bin/puppet apply --noop site.pp

If successful, run puppet apply without the --noop option:

        sudo /opt/puppetlabs/bin/puppet apply site.pp

Once Puppet has finished applying the changes, check the Puppet master's iptables rules:

        sudo iptables -L

It should return:

        Chain INPUT (policy ACCEPT)
        target     prot opt source               destination
        ACCEPT     all  --  anywhere             anywhere             /* 000 lo traffic */
        REJECT     all  --  anywhere             127.0.0.0/8          /* 001 reject non-lo */ reject-with icmp-port-unreachable
        ACCEPT     all  --  anywhere             anywhere             /* 002 accept established */ state RELATED,ESTABLISHED
        ACCEPT     icmp --  anywhere             anywhere             /* 004 allow icmp */
        ACCEPT     tcp  --  anywhere             anywhere             multiport ports ssh /* 005 Allow SSH */
        ACCEPT     tcp  --  anywhere             anywhere             multiport ports http,https /* 006 HTTP/HTTPS connections */
        ACCEPT     tcp  --  anywhere             anywhere             multiport ports 8140 /* 200 Allow Puppet Master */
        DROP       all  --  anywhere             anywhere             /* 999 drop all */

        Chain FORWARD (policy ACCEPT)
        target     prot opt source               destination

        Chain OUTPUT (policy ACCEPT)
        target     prot opt source               destination
        ACCEPT     tcp  --  anywhere             anywhere             /* 003 allow outbound */

### Apply Modules to the Agent Nodes

Now that the accounts and firewall modules have been created, tested, and run on the Puppet master, it’s time to apply them to your managed nodes.

On the Puppet master, navigate to /etc/puppetlabs/code/environments/production/manifests:

        cd /etc/puppetlabs/code/environments/production/manifests

Update site.pp to declare the modules, classes, and resources that should be applied to each managed node:

           {{< file "site.pp" puppet >}}
           node default {

            }

             node 'puppet.example.com' {
              # ...
            }

          node 'puppet-agent-ubuntu.example.com' {

            include accounts

            resources { 'firewall':
            purge => true,
             }

           Firewall {
           before        => Class['firewall::post'],
           require       => Class['firewall::pre'],
            }

            class { ['firewall::pre', 'firewall::post']: }

              }

            node 'puppet-agent-centos.example.com' {

              include accounts

               resources { 'firewall':
               purge => true,
               }

                Firewall {
                 before        => Class['firewall::post'],
                require       => Class['firewall::pre'],
                   }

                class { ['firewall::pre', 'firewall::post']: }

                }
                     {{< /file >}}

By default, the Puppet agent service on your managed nodes will automatically check with the master once every 30 minutes and apply any new configurations from the master. You can also manually invoke the Puppet agent process in between automatic agent runs.

Log in to each managed node (as root) and run the Puppet agent:

            /opt/puppetlabs/bin/puppet agent -t

To ensure the Puppet agent worked:

Log out from your root SSH session and log back in as the limited user that was created.

Check the node's firewall rules:

            sudo iptables -L

Congratulations! You've successfully installed Puppet on a master and two managed nodes. Now that you've confirmed everything is working, you can create additional modules to automate configuration management on your nodes. For more information, review Puppet's open source documentation. You can also install and use modules others have created on the Puppet Forge.
