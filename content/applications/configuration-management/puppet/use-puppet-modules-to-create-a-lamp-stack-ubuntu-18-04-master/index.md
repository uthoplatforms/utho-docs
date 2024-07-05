---
slug: Use-puppet-modules-to-create-a-lamp-stack-ubuntu-18-04-master
description: 'Puppetize Your LAMP Stack: Building with Modules on Ubuntu 18.04 Master'
keywords: ["puppet", "automation", "lamp", "configuration management"]
tags: ["lamp","ubuntu","automation","centos"]
modified: 2024-05-22
modified_by:
    name: Utho
published: 2024-05-22
title: Use Puppet Modules to Create a LAMP Stack
relations:
    platform:
        key: install-puppet-lamp-master
        keywords:
            - distribution: Ubuntu 18.04
authors: ["Utho"]
---

Puppet modules are the foundation of managing your servers with Puppet. They handle tasks like installing and configuring packages, creating directories, and implementing any other server changes specified by the user. A well-crafted module can automate an entire process, such as installing Apache, configuring its files, modifying MPM settings, and setting up virtual hosts. Modules are composed of classes, which are .pp files designed to break down tasks into manageable pieces, enhancing the module's readability and maintainability.

In this guide, you'll create modules for Apache and PHP. You'll also adapt a MySQL module from Puppet Lab's MySQL module available on the Puppet Forge. These steps will build a complete LAMP stack on your server, showcasing various ways to utilize modules.

## Before You Begin

First, set up a Puppet Master on Ubuntu 18.04 and two Puppet agents (one on Ubuntu 18.04 and one on CentOS 7) by following the steps in the Getting Started with Puppet - Basic Installation and Setup guide.

## Create the Apache Module

Start by navigating from the Puppet Master to Puppet's module directory and create the apache directory.


                       cd /etc/puppetlabs/code/environments/production/modules/
                       sudo mkdir apache

                       Inside the apache directory, create subdirectories for manifests, templates, files, and examples.


                       cd apache
                       sudo mkdir {manifests,templates,files,examples}

                       Enter the manifests directory:


                         cd manifests

                         Organize the module into classes within this directory, each dedicated to specific tasks. Create an init.pp class for Apache package installation, a params.pp file for defining variables and parameters, a config.pp for managing Apache configuration files, and a vhosts.pp file for defining virtual hosts. Utilize Hiera to store variables for each node.


### Create the Initial Apache Class and Parameters

Within the manifests directory, create an init.pp file to contain the apache class, matching the module name. This file will handle Apache package installation, using a variable to account for package name discrepancies between Ubuntu 18.04 and CentOS 7.



             {{< file "/etc/puppetlabs/code/environments/production/modules/apache/manifests/init.pp" puppet >}}
               class apache {

              package { 'apache':
              name    => $apachename,
              ensure  => present,
               }

               }

               {{< /file >}}


               The package resource manages package installation, referencing the actual package name via the $apachename variable. The ensure attribute ensures the package presence.


               Define necessary variables within the params.pp file, facilitating their use across multiple classes and within if statements.


               Create a params.pp file with code defining the apache::params class, using an if statement to determine parameters based on the operating system family (Red Hat or Debian).


                     {{< file "/etc/puppetlabs/code/environments/production/modules/apache/manifests/params.pp" puppet >}}
                       class apache::params {

                       if $::osfamily == 'RedHat' {
                       $apachename   = 'httpd'
                       }
                       elsif $::osfamily == 'Debian' {
                       $apachename   = 'apache2'
                       }
                      else {
                      fail ( 'this is not a supported distro.')
                      }

                      }

                     {{< /file >}}

Beyond the original init.pp class, each class name should stem from apache. Hence, this class is termed apache::params, aligning with the file name.

An if statement is employed to establish parameters, drawing from data provided by Facter, already present on the Puppet master. Here, Facter retrieves the operating system family (osfamily) to determine if it's Red Hat or Debian-based.

Note: Throughout this guide, when additions to the parameter list are necessary, variables for Red Hat and Debian will be provided, without displaying the expanding code. Access a complete copy of params.pp here.

After defining the parameters, integrate the params.pp file and its parameters into init.pp. This involves appending the parameters after the class name but before the opening curly bracket ({).

                      {{< file "/etc/puppetlabs/code/environments/production/modules/apache/manifests/init.pp" puppet >}}
                        class apache (
                        $apachename   = $::apache::params::apachename,
                        ) inherits ::apache::params {

                        {{< /file >}}

Retrieve parameter values from apache::params in init.pp, inheriting these values through inherits ::apache::params.

### Manage Configuration Files

Depending on whether you're operating on a Red Hat or Debian-based system, the Apache configuration file will differ. View them here: httpd.conf (Red Hat), apache2.conf (Debian).

Copy the content of httpd.conf and apache2.conf into separate files and save them in the files directory located at /etc/puppetlabs/code/environments/production/modules/apache/files.

Both files require editing to disable keepalive. Add the line KeepAlive Off to the httpd.conf file. If you prefer not to change this setting, add a comment to the top of each file.

                 {{< file "/etc/puppetlabs/code/environments/production/modules/apache/files/httpd.conf" aconf >}}

# This file is managed by Puppet

                 {{< /file >}}

Incorporate these files into the init.pp file, so Puppet recognizes their location on both the master server and agent nodes. Utilize the file resource for this:

                  {{< file "/etc/puppetlabs/code/environments/production/modules/apache/manifests/init.pp" puppet >}}
                     file { 'configuration-file':
                     path    => $conffile,
                     ensure  => file,
                     source  => $confsource,
                      }

                  {{< /file >}}

Since the configuration file is stored in two distinct locations, assign the resource the generic name configuration-file, defining the file path as a parameter with the path attribute. Ensure it's a file with ensure, and specify the location on the Puppet master using source.

Access the params.pp file. Define the $conffile and $confsource variables within the if statement:

                  {{< file "/etc/puppetlabs/code/environments/production/modules/apache/manifests/params.pp" puppet >}}
                      if $::osfamily == 'RedHat' {

                        ...

                      $conffile     = '/etc/httpd/conf/httpd.conf'
                      $confsource   = 'puppet:///modules/apache/httpd.conf'
                      }
                      elsif $::osfamily == 'Debian' {

                      ...

                     $conffile     = '/etc/apache2/apache2.conf'
                     $confsource   = 'puppet:///modules/apache/apache2.conf'
                     }
                     else {

                    ...
                    {{< /file >}}

Include these parameters at the beginning of the apache class declaration in init.pp, akin to the previous example. Refer to a complete copy of the init.pp file here for guidance.

Whenever the configuration file undergoes changes, Apache requires a restart. Automate this process using the service resource in conjunction with the notify attribute, prompting the resource to run whenever the configuration file alters:

                     {{< file "/etc/puppetlabs/code/environments/production/modules/apache/manifests/init.pp" puppet >}}
                     file { 'configuration-file':
                     path    => $conffile,
                     ensure  => file,
                     source  => $confsource,
                     notify  => Service['apache-service'],
                     }

                      service { 'apache-service':
                      name          => $apachename,
                      hasrestart    => true,
                     }

                     {{< /file >}}

Utilize the already-defined parameter specifying the Apache name on Red Hat and Debian systems within the service resource. Trigger a restart of the defined service with the hasrestart attribute.

### Create the Virtual Hosts Files

The management of virtual hosts files varies depending on your system's distribution. Consequently, the code for virtual hosts will be encapsulated within an if statement, akin to the one utilized in the params.pp class but incorporating actual Puppet resources. This illustrates an alternate application of if statements within Puppet's code.

Within the apache/manifests/ directory, create and open a vhosts.pp file. Establish the framework of the if statement:

                      {{< file "/etc/puppetlabs/code/environments/production/modules/apache/manifests/vhosts.pp" puppet >}}
                        class apache::vhosts {

                        if $::osfamily == 'RedHat' {

                        } elsif $::osfamily == 'Debian' {

                        } else {

                        }

                        }

                        {{< /file >}}

On our CentOS 7 server, the virtual hosts file is located at /etc/httpd/conf.d/vhost.conf. This file must be created as a template on the Puppet master. Similarly, for Ubuntu, the virtual hosts file is at /etc/apache2/sites-available/example.com.conf, with example.com replaced by the server's FQDN. Navigate to the templates directory within the apache module and create two files for your virtual hosts:

For Red Hat systems:

                        {{< file "/etc/puppetlabs/code/environments/production/modules/apache/templates/vhosts-rh.conf.erb" aconf >}}
                          <VirtualHost *:80>
                          ServerAdmin	<%= @adminemail %>
                          ServerName <%= @servername %>
                          ServerAlias www.<%= @servername %>
                           DocumentRoot /var/www/<%= @servername -%>/public_html/
                           ErrorLog /var/www/<%- @servername -%>/logs/error.log
                           CustomLog /var/www/<%= @servername -%>/logs/access.log combined
                            </Virtual Host>

                           {{< /file >}}

For Debian systems:

                          {{< file "/etc/puppet/modules/apache/templates/vhosts-deb.conf.erb" aconf >}}
                               <VirtualHost *:80>
                                ServerAdmin	<%= @adminemail %>
                                ServerName <%= @servername %>
                                ServerAlias www.<%= @servername %>
                                DocumentRoot /var/www/html/<%= @servername -%>/public_html/
                                ErrorLog /var/www/html/<%- @servername -%>/logs/error.log
                                CustomLog /var/www/html/<%= @servername -%>/logs/access.log combined
                               <Directory /var/www/html/<%= @servername -%>/public_html>
                                 Require all granted
                                 </Directory>
                                 </Virtual Host>

                                {{< /file >}}

These files use only two variables: adminemail and servername, defined on a node-by-node basis within the site.pp file.

Return to the vhosts.pp file. Now, reference the created templates in the code:

                      {{< file "/etc/puppetlabs/code/environments/production/modules/apache/manifests/vhosts.pp" puppet >}}
                         class apache::vhosts {

                            if $::osfamily == 'RedHat' {
                             file { '/etc/httpd/conf.d/vhost.conf':
                             ensure    => file,
                             content   => template('apache/vhosts-rh.conf.erb'),
                               }
                                } elsif $::osfamily == 'Debian' {
                                file { "/etc/apache2/sites-available/$servername.conf":
                                  ensure  => file,
                                  content  => template('apache/vhosts-deb.conf.erb'),
                                   }
                                 } else {
                                     fail('This is not a supported distro.')
                                      }

                                     }

                                   {{< /file >}}

Both distribution families utilize the file resource, adopting the title of the virtual host's location on the respective distribution. For Debian, this entails referencing the $servername value. The content attribute calls upon the respective templates.

Note: Values containing variables, like the Debian file resource name above, should be enclosed in double quotes ("). Variables within single quotes (') are parsed exactly as written and won't pull in a variable.

Both virtual hosts files mention two directories not present on the systems by default. These can be created using the file resource, each situated within the if statement. The complete vhosts.conf file should resemble:

                          {{< file "/etc/puppetlabs/code/environments/production/modules/apache/manifests/vhosts.pp" puppet >}}
                             class apache::vhosts {

                             if $::osfamily == 'RedHat' {
                             file { '/etc/httpd/conf.d/vhost.conf':
                             ensure    => file,
                             content   => template('apache/vhosts-rh.conf.erb'),
                               }
                              file { [ '/var/www/$servername',
                              '/var/www/$servername/public_html',
                              '/var/www/$servername/log', ]:
                               ensure    => directory,
                                }
                                } elsif $::osfamily == 'Debian' {
                               file { "/etc/apache2/sites-available/$servername.conf":
                               ensure  => file,
                               content  => template('apache/vhosts-deb.conf.erb'),
                                }
                                file { [ '/var/www/$servername',
                                '/var/www/$servername/public_html',
                                '/var/www/$servername/logs', ]:
                                 ensure    => directory,
                                 }
                                 } else {
                                 fail ( 'This is not a supported distro.')
                                  }

                                   }

                                   {{< /file >}}

### Test and Run the Module

Within the apache/manifests/ directory, execute the puppet parser on all files to validate the Puppet code for errors:

                       sudo /opt/puppetlabs/bin/puppet parser validate init.pp params.pp vhosts.pp

It should return empty, indicating no issues present.

Go to the examples directory within the apache module. Create an init.pp file and incorporate the created classes. Customize the values for $servername and $adminemail:

                {{< file "/etc/puppetlabs/code/environments/production/modules/apache/examples/init.pp" puppet >}}
                       $serveremail = 'webmaster@example.com'
                       $servername = 'example.com'

                       include apache
                       include apache::vhosts

                      {{< /file >}}

Validate the module by executing puppet apply with the --noop tag:

                      sudo /opt/puppetlabs/bin/puppet apply --noop init.pp

It should produce no errors and indicate that it will trigger refreshes from events. To install and configure Apache on the Puppet master, rerun without --noop if desired.

Return to the main Puppet directory, then navigate to the manifests folder (not the one within the Apache module).

                       cd /etc/puppetlabs/code/environments/production/manifests

If you're following from the Getting Started with Puppet - Basic Installation and Setup guide, you should already have a site.pp file. If not, create one now.

Open site.pp and integrate the Apache module for each agent node. Specify the variables for adminemail and servername parameters. If following the Getting Started with Puppet - Basic Installation and Setup guide, a single node configuration within site.pp resembles:

               {{< file "/etc/puppetlabs/code/environments/production/manifests/site.pp" puppet >}}
               node 'ubuntuhost.example.com' {
               $adminemail = 'webmaster@example.com'
                $servername = 'hostname.example.com'

               include accounts
               include apache
               include apache::vhosts

               resources { 'firewall':
               purge => true,
                }

              Firewall {
                 before        => Class['firewall::post'],
                 require       => Class['firewall::pre'],
                 }

                class { ['firewall::pre', 'firewall::post']: }

                 }

                   node 'centoshost.example.com' {
                   $adminemail = 'webmaster@example.com'
                   $servername = 'hostname.example.com'

                   include accounts
                   include apache
                   include apache::vhosts

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

If not following the Getting Started with Puppet - Basic Installation and Setup guide, your site.pp file should resemble this example:

                      {{< file "/etc/puppetlabs/code/environments/production/manifests/site.pp" puppet >}}
                        node 'ubupuppet.ip.Uthousercontent.com' {
                        $adminemail = 'webmaster@example.com'
                        $servername = 'hostname.example.com'

                        include apache
                        include apache::vhosts

                         }

                          node 'centospuppet.ip.Uthousercontent.com' {
                          $adminemail = 'webmaster@example.com'
                          $servername = 'hostname.example.com'

                           include apache
                           include apache::vhosts

                           }
                         {{</ file >}}

By default, the Puppet agent service on managed nodes checks with the master every 30 minutes for new configurations. You can also manually invoke the Puppet agent process between automatic runs. To manually run the new module on agent nodes, log in to each node and execute:

                               sudo /opt/puppetlabs/bin/puppet agent -t

## Using the MySQL Module

Numerous modules necessary for server setup are available on Puppet Labs' Puppet Forge. These can be configured as extensively as custom modules and save time by not requiring creation from scratch.

Ensure you're in the /etc/puppetlabs/code/environments/production/modules directory and install the Puppet Forge's MySQL module by PuppetLabs. This will handle prerequisite module installations as well.

                          cd /etc/puppetlabs/code/environments/production/modules
                          sudo /opt/puppetlabs/bin/puppet module install puppetlabs-mysql

### Use Hiera to Create Databases

Before configuring the MySQL module, consider the need for distinct values across agent nodes. Utilize Hiera to supply Puppet with node-specific data. For instance, each node may require a different root password, resulting in unique MySQL databases.

Head to /etc/puppet and create Hiera's configuration file hiera.yaml within the main puppet directory, using Hiera's default settings.

              {{< file "/etc/puppetlabs/code/environments/production/hiera.yaml" yaml >}}
                     ---
                  version: 5
                  hierarchy:
                  - name: Common
                    path: common.yaml
                    defaults:
                    data_hash: yaml_data
                    datadir: data

                    {{< /file >}}

Establish the common.yaml file. Here, define the default root password for MySQL:

                    {{< file "/etc/puppetlabs/code/environments/production/common.yaml" yaml >}}
                     mysql::server::root_password: 'password'

                      {{< /file >}}

The common.yaml file serves as the fallback when a variable is undefined elsewhere, meaning all servers share the same MySQL root password. Alternatively, these passwords can be hashed for enhanced security.

To utilize the MySQL module's defaults, simply include ::mysql::server in the site.pp file. However, in this scenario, override some defaults to create a database for each node. Edit site.pp accordingly:

                              {{< file >}}
                              node 'ubupuppet.ip.Uthousercontent.com' {
                               $adminemail = 'webmaster@example.com'
                               $servername = 'hostname.example.com'

                              include apache
                              include apache::vhosts
                              include php

                              mysql::db { "mydb_${fqdn}":
                              user     => 'myuser',
                              password => 'mypass',
                              dbname   => 'mydb',
                              host     => $::fqdn,
                               grant    => ['SELECT', 'UPDATE'],
                               tag      => $domain,
                                }

                                 }

                           node 'centospuppet.ip.Uthousercontent.com' {
                           $adminemail = 'webmaster@example.com'
                           $servername = 'hostname.example.com'

                           include apache
                           include apache::vhosts
                           include mysql::server
                           include php

                            mysql::db { "mydb_${fqdn}":
                            user     => 'myuser',
                             password => 'mypass',
                             dbname   => 'mydb',
                             host     => $::fqdn,
                              grant    => ['SELECT', 'UPDATE'],
                              tag      => $domain,
                                }

                                }
                                 {{</ file >}}

Manually update each node by SSHing into them and executing the provided command.

                          sudo /opt/puppetlabs/bin/puppet agent -t

Otherwise, the Puppet agent service on managed nodes automatically checks with the master every 30 minutes for new configurations.

## Create the PHP Module

Establish the php directory within /etc/puppetlabs/code/environments/production/modules, then create the files, manifests, templates, and examples directories.

                   sudo mkdir php
                   cd php
                   sudo mkdir {files,manifests,examples,templates}

Create init.pp, utilizing the package resource to install PHP and its extension and application repository:

                    {{< file "/etc/puppetlabs/code/environments/production/modules/php/manifests/init.pp" puppet >}}
                           class php {

                        package { 'php':
                         name: $phpname,
                        ensure: present,
                                     }

                       package { 'php-pear':
                            ensure: present,
                                    }

                                 }

                              {{< /file >}}

                              Append include php to the hosts in your sites.pp file.

                  {{< file "/etc/puppetlabs/code/environments/production/manifests/site.pp" puppet >}}
                       node 'ubupuppet.ip.Uthousercontent.com' {
                       $adminemail = 'webmaster@example.com'
                         $servername = 'hostname.example.com'

                           include apache
                           include apache::vhosts
                           include mysql::database
                           include php

                             }

                          node 'centospuppet.ip.Uthousercontent.com' {
                           $adminemail = 'webmaster@example.com'
                           $servername = 'hostname.example.com'

                           include apache
                           include apache::vhosts
                           include mysql::database
                           include php

                             }
                            {{</ file >}}

Execute the command on your agent nodes to implement any changes to your servers.

                                sudo /opt/puppetlabs/bin/puppet agent -t

Otherwise, the Puppet agent service on managed nodes will automatically apply any new configurations from the master every 30 minutes.

With these steps completed, you'll have a fully operational LAMP stack on each of your Puppet-managed nodes.
