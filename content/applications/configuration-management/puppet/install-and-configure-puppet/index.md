---
slug: "Mastering Puppet: Step-by-Step Installation and Configuration"
description: 'Essential Guide to Setting Up and Configuring a Puppet Master and Agents on Ubuntu or CentOS Servers'
keywords: ["puppet installation", "configuration change management", "server automation"]
tags: ["ubuntu","automation","centos"]
deprecated: true
deprecated_link: 'applications/configuration-management/getting-started-with-puppet-6-1-basic-installation-and-setup/'
modified: 2024-05-22
modified_by:
    name: Utho
published: 2024-05-22
title: Install and Configure Puppet
authors: ["Pawan Kumar"]
---

Puppet is a powerful configuration automation platform designed to streamline system administration tasks. Utilizing a client/server model, Puppet allows managed servers, known as Puppet agents, to communicate with and retrieve configuration profiles from the Puppet master.

Written in its own accessible language, Puppet enables system administrators to describe the desired state of their systems in modules located on the Puppet master. These modules are then translated into code by Puppet, which adjusts the agent servers accordingly when the puppet agent command is executed on an agent node or at predetermined intervals.

Puppet is versatile, capable of managing a few personal servers to an extensive enterprise-level infrastructure. While it primarily runs on Linux and other Unix-like operating systems, it has been adapted for Windows as well. For this guide, we will focus on setting up an Ubuntu 16.04 LTS master server and two agent nodes: one running Ubuntu 16.04, and the other CentOS 7.

Note: Begin this guide as the root user. An administrative limited user will be configured in the later steps.

## Before You Begin

Ensure you have three Uthos available, with at least one having four CPU cores for the Puppet master. We recommend the Utho 8GB plan for the master. The other two nodes can be any plan size based on your intended use after Puppet installation and configuration.

Follow the Setting Up and Securing a Compute Instance guide, ensuring all Uthos are set to the same timezone.

Note: For simplicity, set the Puppet master's hostname to puppet and ensure it has a valid fully-qualified domain name (FQDN).

To check your hostname, run hostname, and to check your FQDN, run hostname -f.

## Puppet Master

### Install Puppet Master

Install the puppetlabs-release repository on Ubuntu 16.04 and update your system. This process involves downloading a .deb file that will configure the necessary repositories for you:

        wget https://apt.puppet.com/puppetlabs-release-pc1-xenial.deb
        dpkg -i puppetlabs-release-pc1-xenial.deb
        apt update

Note: If you prefer to use a different Linux distribution for your master server, substitute the initial .deb file with the appropriate one for your chosen distribution based on the following formats:

**Red Hat-based systems:**

`wget https://yum.puppet.com/puppetlabs-release-pc1-OS-VERSION.noarch.rpm`

**Debian-based systems:**

`wget https://apt.puppet.com/puppetlabs-release-pc1-VERSION.deb`

Any Ubuntu-specific commands will need to be adjusted for the appropriate distribution. For more details, refer to Puppet's Installation Documentation or our package management guide.

Install the puppetmaster-passenger package:

                         apt install puppetmaster-passenger

The default Puppet installation might start Apache and configure it to listen on the same port as Puppet. Stop Apache to avoid this conflict (if using CentOS 7, replace apache2 with httpd):

                         systemctl stop apache2

Ensure you have the latest version of Puppet by running:

                          puppet resource package puppetmaster ensure=latest

### Configure Puppet Master

Update /etc/puppet/puppet.conf by adding the dns_alt_names line to the [main] section, replacing puppet.example.com with your own FQDN:

                    {{< file "/etc/puppet/puppet.conf" >}}
                    [main]
                    dns_alt_names=puppet,puppet.example.com
                    {{< /file >}}

Start the Puppet master:

                     systemctl start puppetmaster

By default, the Puppet master listens for client connections on port 8140. If the puppetmaster service fails to start, check that the port is not already in use:

                     netstat -anpl | grep 8140

## Puppet Agents

### Install Puppet Agent

For agent nodes running Ubuntu 16.04 or other Debian-based distributions, install Puppet with this command:

                     apt install puppet

For agent nodes running CentOS 7 or other Red Hat systems, follow these steps:

For CentOS 7 only, add the Puppet Labs repository:

                     rpm -ivh https://yum.puppet.com/el/7/products/x86_64/puppetlabs-release-22.0-2.noarch.rpm

Note: If you're on a Red Hat system other than CentOS 7, skip this step.

Install the Puppet agent:

                      yum install puppet

### Configure Puppet Agent

Modify your Puppet Agent's host file to resolve the Puppet master IP as puppet:

                    {{< file "/etc/hosts" >}}
                    198.51.100.1    puppet
                    {{< /file >}}

Add the server value to the [main] section of the node's puppet.conf file, replacing puppet.example.com with the FQDN of your Puppet master:

                     {{< file "/etc/puppet/puppet.conf" conf >}}
                       [main]
                       server=puppet.example.com
                       {{< /file >}}

Restart the Puppet service:

                         systemctl restart puppet

### Generate and Sign Certificates

On each agent, generate a certificate for the Puppet master to sign:

        puppet agent -t

This will output an error, stating that no certificate has been found. This error is because the generated certificate needs to be approved by the Puppet master.

Log in to your Puppet master and list the certificates that need approval:

                      puppet cert list

It should output a list with your agent node's hostname.

Approve the certificates, replacing hostname.example.com with the hostname of each agent node:

                  puppet cert sign hostname.example.com

                  Return to the Puppet agent nodes and run the Puppet agent again:

                   puppet agent -t

## Add Modules to Configure Agent Nodes

Both the Puppet master and agent nodes configured above are functional, but not secure. Based on concepts from the Setting Up and Securing a Compute Instance guide, a limited user and a firewall should be configured. This can be done on all nodes through the creation of basic Puppet modules, shown below.

Note: This is not meant to provide a basis for a fully-hardened server, and is intended only as a starting point. Alter and add firewall rules and other configuration options, depending on your specific needs

### Add a Limited User

From the Puppet master, navigate to the /etc/puppet/modules directory and create a new module for adding user accounts, then cd into that directory:

        mkdir /etc/puppet/modules/accounts
        cd /etc/puppet/modules/accounts

Create the following directories to structure the module properly:

            mkdir {examples,files,manifests,templates}

- examples for testing the module locally.
- files for any static files that may need to be edited or added.
- manifests for the actual Puppet code for the module.
- templates for any non-static files that may be needed.

Move to the manifests directory and create the initial class file, init.pp. All modules require an init.pp file to serve as the main definition file.

                           cd manifests

Within the init.pp file, define a limited user to replace root. Replace all instances of username with your chosen username:

                          {{< file "/etc/puppet/modules/accounts/manifests/init.pp" puppet >}}
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

This init.pp file defines the accounts class and calls for the user resource. The ensure attribute ensures the user exists. The home sets the user's home directory, shell specifies the shell type, managehome ensures the home directory is created, and gid sets the primary group for the user.

Although the primary group is set to share the username, the group itself has not been created. Save and exit init.pp. Then, create a new file called groups.pp and add the following contents to create the user's group:


                            {{< file "/etc/puppet/modules/accounts/manifests/groups.pp" puppet >}}
                             class accounts::groups {

                              group { 'username':
                               ensure  => present,
                               }

                                }

                               {{< /file >}}

Include this file by adding include accounts::groups to the init.pp file within the accounts class:

                            {{< file "/etc/puppet/modules/accounts/manifests/init.pp" puppet >}}
                            class accounts {

                             include groups
                              ...
                              }

                              {{< /file >}}

To grant administrative privileges to the new user, add them to the appropriate group based on the operating system. On Debian systems, use the sudo group, and on Red Hat systems, use the wheel group. This can be set dynamically with a selector and facter, a program included in Puppet that tracks information (or facts) about every server. Add a selector statement at the top of the init.pp file within the accounts class brackets:


                             {{< file "/etc/puppet/modules/accounts/manifests/init.pp" puppet >}}
                             class accounts {

                              $rootgroup = $osfamily ? {
                              'Debian'  => 'sudo',
                              'RedHat'  => 'wheel',
                              default   => warning('This distribution is not supported by the Accounts module'),
                               }

                              user { 'username':
                               ...

                               }

                              {{< /file >}}

This sequence tells Puppet to evaluate the operating system family using facter. If the value is Debian, set $rootgroup to sudo. If the value is RedHat, set $rootgroup to wheel. If neither, output a warning that the distribution is not supported by this module.

Note: The user definition includes the $rootgroup, and the Puppet Configuration Language executes code from top to bottom. You must define $rootgroup before the user so it can be referenced.

Add the groups value to the user resource, referencing the $rootgroup variable defined in the previous step:

                    {{< file "/etc/puppet/modules/accounts/manifests/init.pp" puppet >}}
                    user { 'username':
                    ensure      => present,
                    home        => '/home/username',
                    shell       => '/bin/bash',
                    managehome  => true,
                    gid         => 'username',
                    groups      => "$rootgroup",
                     }

                    {{< /file >}}

The value "$rootgroup" is enclosed in double quotes (") because it is a variable. Variables enclosed within double quotes can accept dynamic values, whereas those in single quotes will be treated as literals.

After running this command, you'll be prompted to enter your password. Once entered, a hashed password will be generated and displayed. This hashed password should then be copied and added to the user resource in your Puppet manifest. Remember to enclose the hashed password in single quotes for proper formatting.

                           openssl passwd -1

You will be prompted to enter your password and confirm. A hashed password will be output. This should then be copied and added to the `user` resource:

                        {{< file "/etc/puppet/modules/accounts/manifests/init.pp" puppet >}}
                         class accounts {

                          user { 'username':
                          ensure      => present,
                          home        => '/home/username',
                          shell       => '/bin/bash',
                          managehome  => true,
                          gid         => 'username',
                          groups      => "$rootgroup",
                          password    => '$1$07JUIM1HJKDSWm8.NJOqsP.blweQ..3L0',
                           }

                           }

                           {{< /file >}}

Note: The hashed password **must** be included in single quotes (').

Once you've made the necessary changes to your Puppet manifest, it's essential to validate the syntax to ensure there are no errors. You can do this using the Puppet parser. Simply run the following command:

                         puppet parser validate init.pp

Any errors will be logged to standard output. If no errors are returned, your code is valid.

Next, navigate to the examples directory and create another init.pp file to call the accounts module:

                              cd ../examples

                              {{< file "/etc/puppet/modules/accounts/examples/init.pp" puppet >}}
                              include accounts

                              {{< /file >}}

After adding this line, save and exit the file.

While you're still in the examples directory, you can test the module to ensure it functions as expected without actually making any changes to the system. To do this, run the following command:

                             puppet apply --noop init.pp

Note: The `--noop` parameter prevents Puppet from actually running the module.

It should return:

                     Notice: Compiled catalog for puppet.example.com in environment production in 0.26 seconds
                     Notice: /Stage[main]/Accounts::Groups/Group[username]/ensure: current_value absent, should be present (noop)
                     Notice: Class[Accounts::Groups]: Would have triggered 'refresh' from 1 events
                     Notice: /Stage[main]/Accounts/User[username]/ensure: current_value absent, should be present (noop)
                     Notice: Class[Accounts]: Would have triggered 'refresh' from 1 events
                     Notice: Stage[main]: Would have triggered 'refresh' from 2 events
                     Notice: Finished catalog run in 0.02 seconds

Run puppet apply to make changes to the Puppet master server:

                     puppet apply init.pp

Log out as root and log in to the Puppet master as your new user. Proceed with the rest of the guide using this user.

### Edit SSH Settings

Although a new user has been successfully added to the Puppet master, the account is still not secure. Root access should be disabled for the server before continuing.

Navigate to files within the account module directory:


                       cd /etc/puppet/modules/accounts/files

Copy the sshd_config file to this directory:

                      sudo cp /etc/ssh/sshd_config .

Open the file with sudo, and set the PermitRootLogin value to no:

                    {{< file "/etc/puppet/modules/accounts/files/sshd_config" aconf >}}
                    PermitRootLogin no

                    {{< /file >}}

Navigate back to the manifests directory and, using sudo, create a file called ssh.pp. Use the file resource to replace the default configuration file with the one managed by Puppet:

                     cd ../manifests

                     {{< file "/etc/puppet/modules/accounts/manifests/ssh.pp" puppet >}}
                      class accounts::ssh {

                      file { '/etc/ssh/sshd_config':
                      ensure  => present,
                      source  => 'puppet:///modules/accounts/sshd_config',
                       }

                       }

                     {{< /file >}}

Note: The file directory is excluded from the source line because the files folder is where files are typically stored by default within a module. For a deeper understanding of the formatting conventions for accessing resources within a module, consult the official Puppet module documentation.

Next, create a second resource to restart the SSH service whenever changes are made to sshd_config. Given that the SSH service is named ssh on Debian systems and sshd on Red Hat systems, a selector statement is required:

                               {{< file "/etc/puppet/modules/accounts/manifests/ssh.pp" puppet >}}
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

Include the ssh class within init.pp:


                              {{< file "/etc/puppet/modules/accounts/manifests/init.pp" puppet >}}
                              class accounts {
                               include groups
                                include ssh

                                ...

                               {{< /file >}}

Your complete init.pp should resemble the following:

                            {{< file "/etc/puppet/modules/accounts/manifests/init.pp" puppet >}}
                            class accounts {
                            include groups
                            include ssh

                            $rootgroup = $osfamily ? {
                            'Debian' => 'sudo',
                            'RedHat' => 'wheel',
                            default => warning('This distro not supported by Accounts module'),
                             }
                            user { 'example':

                           ensure  => present,
                           home    => '/home/username',
                           shell   => '/bin/bash',
                           managehome  => true,
                           gid     => 'username',
                           groups  => "$rootgroup",
                           password => '$1$07JUIM1HJKSPWm8.NJOqsP.blweQ..3L0'
                           }

                           }

                           {{< /file >}}

After validating the Puppet parser, navigate to the examples directory to test and execute puppet apply:

                         sudo puppet parser validate ssh.pp
                         cd ../examples
                        sudo puppet apply --noop init.pp
                        sudo puppet apply init.pp

Note: During validation, you might encounter a line in the output similar to:

`Error: Removing mount "files": /etc/puppet/files does not exist or is not a directory`

This line pertains to a Puppet configuration file, not the module resource being copied. If this is the sole error in your output, the operation should still proceed successfully.

Verify the functionality of the ssh class by logging out and attempting to log in as root. Access should be denied.

### Add and Configure IPtables

In this section, we'll set up firewall rules using iptables. However, these rules won't persist across reboots by default. To address this, install the necessary package on each node (both master and agents) before proceeding:

**Ubuntu/Debian**:

                          sudo apt install iptables-persistent

**CentOS 7**:

For CentOS 7, which utilizes firewalld by default, ensure firewalld is stopped and disabled before working directly with iptables:

                           sudo systemctl stop firewalld && sudo systemctl disable firewalld

                          sudo yum install iptables-services

On your Puppet master node, install Puppet Lab's firewall module from the Puppet Forge:

                         sudo puppet module install puppetlabs-firewall

The module will be installed in your /etc/puppet/modules directory.

Navigate to the manifests directory within the new firewall module:

                            cd /etc/puppet/modules/firewall/manifests/

Create a file named pre.pp, which will contain all the basic networking rules that should be applied first:

                    {{< file "/etc/puppet/modules/firewall/manifests/pre.pp" puppet >}}
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

Each rule is documented with commented text. For additional information, refer to the Puppet Forge Firewall page.

In the same directory, create post.pp, which will run any firewall rules that need to be applied last:

                     {{< file "/etc/puppet/modules/firewall/manifests/post.pp" puppet >}}
                     class firewall::post {

                      firewall { '999 drop all':
                      proto  => 'all',
                      action => 'drop',
                      before => undef,
                      }

                      }

                    {{< /file >}}

These rules will instruct the system to drop all inbound traffic that isn't already permitted in the firewall.

Use the Puppet parser on both files to ensure there are no errors in the code:

                             sudo puppet parser validate pre.pp
                             sudo puppet parser validate post.pp

Navigate to the parent directory, create a new directory named examples, and enter it:

        cd ..
        sudo mkdir examples
        cd examples

Inside examples, create an init.pp file to test the firewall functionality on the Puppet master:

                 {{< file "/etc/puppet/modules/firewall/examples/init.pp" puppet >}}
                  resources { 'firewall':
                  purge => true,
                   }

                    Firewall {
                    before        => Class['firewall::post'],
                    require       => Class['firewall::pre'],
                    }

                  class { ['firewall::pre', 'firewall::post']: }

                   firewall { '200 Allow Puppet Master':
                   dport          => '8140',
                   proto         => 'tcp',
                  action        => 'accept',
                  }

                   {{< /file >}}

This code snippet ensures the proper execution of pre.pp and post.pp and adds a firewall rule to allow nodes to access the Puppet master.

Validate the init.pp file using the Puppet parser and then test its functionality:

                      sudo puppet parser validate init.pp
                      sudo puppet apply --noop init.pp

If successful, execute puppet apply without the --noop option:

        sudo puppet apply init.pp

After Puppet completes the execution, verify the iptables rules:

                          sudo iptables -L

It should display:

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

### Add Modules to the Agent Nodes

With the accounts and firewall modules now created, tested, and applied on the Puppet master, proceed to add them to the Puppet agent nodes created earlier.

From the Puppet master, navigate to /etc/puppet/manifests.

                               cd /etc/puppet/manifests

Display all available agent nodes:

                                sudo puppet cert list -all

Create the site.pp file to assign modules to specific nodes. Replace ubuntuagent.example.com and centosagent.example.com with the FQDNs of your agent nodes:

                          {{< file "/etc/puppet/manifests/site.pp" puppet >}}
                          node 'ubuntuagent.example.com' {
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

                          node 'centosagent.example.com' {
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

This configuration includes the accounts module and applies the same firewall settings as previously defined to ensure consistent firewall rules.

On each Puppet agent node, activate the puppet agent command:

                       puppet agent --enable

                       Execute the Puppet agent:

                       puppet agent -t

                       To verify the Puppet agent's functionality, log in as the limited user created earlier and inspect the iptables:

                        sudo iptables -L

Congratulations! You've successfully deployed Puppet on a master and two agent nodes. With everything confirmed operational, you can develop additional modules to automate configuration management on your agent nodes. For further guidance, explore Puppet module fundamentals. You can also discover and utilize modules created by others on the Puppet Forge.
