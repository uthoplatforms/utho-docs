---
slug: Build-lamp-stack-and-fail2ban-with-salt-states-across-minions
description: 'Leverage Salt States to Deploy a LAMP Stack and Fail2ban on All Specified Salt Minions Running Debian 8'
keywords: ["salt", "salt state", "lamp stack", "apache", "mysql", "php", "fail2ban", "salt minions", "debian 8"]
tags: ["automation","salt","debian","lamp","apache"]
modified: 2024-05-23
modified_by:
    name: Utho
published: 2024-05-23
title: Use Salt States to Create LAMP Stack and Fail2ban Across Salt minions
authors: ["Utho"]
---

Salt States allow you to install and configure a server setup across multiple servers. This tutorial demonstrates using Salt States to create a LAMP stack on all Salt Minions.

## Configure the Salt Master

Before you begin, ensure you have installed a Salt Master and Salt Minions using the utho Install Salt guide. This tutorial is tailored for Debian 8 but can be easily adapted for other Linux distributions.

Open the /etc/salt/master file. Search for file_roots, optionally review the surrounding "File Server settings" section, and edit it as follows:

                    {{< file "/etc/salt/master" >}}

# Example:
                         file_roots:
                          base:
                          - /etc/salt/base

                          {{< /file >}}

Note: Copy the text exactly to ensure the correct two-space YAML nesting. Also, observe the other potential Minion States listed under the example base file root.

Create the newly listed file root directory:

                              mkdir /etc/salt/base

The Salt Master's configuration file is now adjusted for a new base directory. This base directory typically contains the SLS files, creating a tree-like organization for Salt States pertaining to that directory. You can create additional directories similar to the base directory with additional SLS files for different Salt State categories.

## Create the Top and Additional SLS Files

The top file creates the top-level organization for Salt States and Minions within the directory. Other SLS files typically correspond to the top file listings.

As mentioned in the note above, each of these configuration files requires specific spacing. To ensure consistency, copy the examples below, including:

Create the /srv/salt/top.sls file and add the following. Ensure exact formatting for the YAML two-space nesting:

           {{< file "/etc/salt/base/top.sls" >}}
         base:
           '*':
           - lamp
          - extras

            {{< /file >}}

Create the /srv/salt/lamp.sls file referred to in Step 1, and add the following:

            {{< file "/etc/salt/base/lamp.sls" >}}
                 lamp-stack:
               pkg.installed:
                      - pkgs:
               - mysql-server
                       - php5
                    - php-pear
                    - php5-mysql

                    {{< /file >}}

This file defines a simple Salt State using the pkg State Module. This Salt State ensures that a LAMP stack is installed across Minions.

The second bullet listed in top.sls declares an extras file which will list and install additional software. Create a /srv/salt/extras.sls file and add the following:

                            {{< file "/etc/salt/base/extras.sls" >}}
                             fail2ban:
                             pkg.installed

                              {{< /file >}}

Restart the Salt Master:

                             systemctl restart salt-master

## Create the Salt State on the Minions

To install the packages listed above and create a Salt State, run:

                              salt '*' state.highstate

This process will take a few minutes. If successful, a report will be displayed with a summary similar to the following:

        Summary
        ------------
        Succeeded: 2 (changed=2)
        Failed:    0
        ------------
        Total states run:     2

For additional verification that the services are active on the minion, run:

                        salt '*' cmd.run "service --status-all | grep 'apache2\|mysql\|fail2ban'"

A LAMP stack and Fail2ban Salt State have been created on all listed Salt Minions. For more information on how to configure the LAMP Stack, refer to the Salt States for Configuration of Apache, MySQL, and PHP (LAMP) guide.
