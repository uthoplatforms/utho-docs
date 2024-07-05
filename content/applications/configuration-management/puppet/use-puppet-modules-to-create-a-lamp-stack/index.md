---
slug: "Streamline Your LAMP Setup with Puppet Modules: Effortless Configuration Made Simple"
description: "This guide demonstrates the effective utilization of Puppet modules for seamless management of files, services, template creation, and data storage using Hiera on Ubuntu 14.04 LTS."
keywords: ["puppet", "automation", "puppet master", "puppet agent", "modules", "server automation", "configuration management"]
tags: ["lamp","automation"]
deprecated: true
deprecated_link: 'applications/configuration-management/use-puppet-modules-to-create-a-lamp-stack-ubuntu-18-04-master/'
modified: 2015-11-12
modified_by:
    name: Pawan Kumar
published: 2024-04-01
title: "Building a LAMP Stack with Puppet Modules"
relations:
    platform:
        key: install-puppet-lamp-master
        keywords:
            - distribution: Ubuntu 14.04
authors: ["Pawan Kumar"]
---

In Puppet, modules are like Lego blocks for setting up server configurations. They handle tasks such as installing software, creating folders, and making any other necessary changes to your system. A module is designed to handle all aspects of a specific task, like installing Apache, configuring its files, and setting up virtual hosts. Modules are further divided into classes, which are `.pp` files that simplify tasks and make the module easier to understand for future users.

In this guide, we'll build Apache and PHP modules from scratch, while adapting a MySQL module from Puppet Lab's MySQL module on the [Puppet Forge](https://forge.puppet.com/). By following these steps, you'll set up a complete LAMP stack on your server and gain insights into how modules can be used effectively.

{{< note respectIndent=false >}}
This guide assumes you're using an Ubuntu 14.04 LTS Puppet master along with CentOS 7 and Ubuntu 14.04 nodes, as configured in the [Puppet Setup](/docs/guides/install-and-configure-puppet/) guide. If you have a different setup, you may need to adjust the instructions accordingly.
{{< /note >}}

## Creating the Apache Module
1.  Navigate to Puppet's module directory from the Puppet Master, and create the `apache` directory.

        cd /etc/puppet/modules
        sudo mkdir apache

2. Within the apache directory, create the following directories: `manifests`, `templates`, `files`, and `examples`.

        cd apache
        sudo mkdir {manifests,templates,files,examples}

3.  Go to the `manifests` directory.

        cd manifests

- `init.pp`: Responsible for downloading the Apache package.
- `params.pp`: Defines variables and parameters.
- `config.pp`: Manages configuration files for the Apache service.
- `vhosts.pp`: Defines virtual hosts.

Additionally, this module will utilize [Hiera](http://docs.puppet.com/hiera/latest/) to store node-specific variables.

### Define the Initial Apache Class and Parameters

1.  Inside the `manifests` directory, create an `init.pp` class. This class should have the same name as the module:

        {{< file "/etc/puppet/modules/apache/manifests/init.pp" puppet >}}
        class apache {
        }
        {{< /file >}}

This file will handle the installation of the Apache package. Since Ubuntu 14.04 and CentOS 7 have different package names for Apache, we'll use a variable to account for this difference:

    {{< file "/etc/puppet/modules/apache/manifests/init.pp" puppet >}}
    class apache {

    package { 'apache':
    name    => $apachename,
    ensure  => present,
    }

    }

{{< /file >}}

1. The `package` resource manages the installation, removal, or presence of a package. Typically, the resource `name` ('apache' here) matches the package name. However, due to naming differences, we use apache as the resource name, while the actual package name is referenced with the name parameter. In this case, name calls for the yet-to-be-defined variable `$apachename`. The `ensure` parameter ensures the package is `present`.


2. Now, with the need for defined variables, the `params.pp` class becomes essential. Although these variables could be defined within the `init.pp` code, using a `params.pp` class allows for better organization and reuse of variables across multiple classes. This separation also facilitates conditional statements for defining variables based on different scenarios.

    Create and open `params.pp`:

       {{< file "/etc/puppet/modules/apache/manifests/params.pp" puppet >}}
       class apache::params {

       }

       {{< /file >}}

To maintain consistency with the original `init.pp` class, each subsequent class name should start with apache::. Therefore, for this class, it will be named `apache::params`, matching the file name.

3. Now, let's define the parameters. We'll use an `if` statement to check the operating system family (`osfamily`) retrieved from  [Facter](https://puppet.com/docs/puppet/7/facter.html), which is already installed on the Puppet master. This will help determine if the system is Red Hat or Debian-based.

Here's a basic structure for the `if` statement:

    {{< file "/etc/puppet/modules/apache/manifests/params.pp" puppet >}}
    class apache::params {

    if $::osfamily == 'RedHat' {

    } elseif $::osfamily == 'Debian' {

    } else {
    print "This is not a supported distro."
    }

    }

    {{< /file >}}

And now, let's add the variables that have already been referenced:

    {{< file "/etc/puppet/modules/apache/manifests/params.pp" puppet >}}
    class apache::params {

    if $::osfamily == 'RedHat' {
    $apachename     = 'httpd'
    } elseif $::osfamily == 'Debian' {
    $apachename     = 'apache2'
    } else {
    print "This is not a supported distro."
    }

    }

    {{< /file >}}


{{< note respectIndent=false >}}
Throughout this guide, when new variables need to be added to the parameter list, the required variables for both Red Hat and Debian systems will be provided without displaying the expanded code. You can view the complete `params.pp` file [here](/docs/assets/params.pp).
{{< /note >}}

4. After defining the parameters in the params.pp file, we need to include this file and its parameters in the init.pp class. To achieve this, the parameters should be added after the class name but before the opening curly bracket (`{`).

       {{< file "/etc/puppet/modules/apache/manifests/init.pp" puppet >}}
       class apache (
       $apachename   = $::apache::params::apachename,
       ) inherits ::apache::params {

       {{< /file >}}

The string `$::apache::params::value` directs Puppet to retrieve the values from the apache module's params class, followed by the parameter name. The line `inherits ::apache::params` enables `init.pp` to inherit these values.

### Manage Configuration Files
Apache has two different configuration files, depending on whether you are working on a Red Hat- or Debian-based system. These can be accessed via the following links: [httpd.conf (Red Hat)](/docs/assets/httpd.conf), [apache2.conf (Debian)](/docs/assets/apache2.conf).

1. Copy the `httpd.conf`and `apache2.conf` files to the files directory located at `/etc/puppet/modules/apache/files/`.

2.  Edit both files to set the `KeepAlive` settings to `Off`. This setting should be added to `httpd.conf`. Otherwise, add a comment to the top of each file.

        {{< file "/etc/puppet/modules/apache/files/httpd.conf" aconf >}}

# Ensuring Puppet Management of the File
{{< /file >}}

3. The inclusion of these files into the `init.pp` file is now necessary for Puppet to recognize their locations on both the master server and agent nodes. This task involves utilizing the `file` resource.

       {{< file "/etc/puppet/modules/apache/manifests/init.pp" puppet >}}
       file { 'configuration-file':
       path    => $conffile,
       ensure  => file,
       source  => $confsource,
       }

       {{< /file >}}

To simplify, we use a generic name `configuration-file` for the resource because it's located in two places. The file `path` is specified as a parameter using path. We ensure it's a file with `ensure`. `Source` parameter points to the location of the master files on the Puppet master.

4.  Next, in the `params.pp` file, define `$conffile` and `$confsource` variables within the if statement.

        {{< file "/etc/puppet/modules/apache/manifests/params.pp" puppet >}}
if $::osfamily == 'RedHat' {

         ...

        $conffile     = '/etc/httpd/conf/httpd.conf'
        $confsource   = 'puppet:///modules/apache/httpd.conf'
        } elsif $::osfamily == 'Debian' {

         ...

         $conffile     = '/etc/apache2/apache2.conf'
         $confsource   = 'puppet:///modules/apache/apache2.conf'
         } else {

         ...

        {{< /file >}}

To make things clear, add these parameters to the init.pp file similar to how additional parameters are added. You can find a full version of the init.pp file [here](/docs/assets/puppet_apacheinit.pp).

5.  When the configuration file is modified, Apache requires a restart. To streamline this process, use the `service` resource along with the `notify` attribute. This ensures that the resource executes every time the configuration file undergoes changes.

        {{< file "/etc/puppet/modules/apache/manifests/init.pp" puppet >}}
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

The `service` resource utilizes the previously established parameter that specifies the name of Apache on Red Hat and Debian systems. By employing the `hasrestart` attribute, a restart of the designated service is initiated.

### Create the Virtual Hosts Files

To manage Virtual Hosts files effectively, we'll adopt different approaches based on whether the server operates on Red Hat or Debian distributions. To handle this, we'll enclose the code for virtual hosts within an if statement. This mirrors the structure of the `params.pp` class but integrates actual Puppet resources. This serves as an illustrative example of how Puppet's code utilizes conditional statements.

1. Navigate to the `apache/manifests/` directory and create a new file named `vhosts.pp`.

2. Begin by crafting the framework of the `if` statement.

       {{< file "/etc/puppet/modules/apache/manifests/vhosts.pp" puppet >}}
       class apache::vhosts {

       if $::osfamily == 'RedHat' {

       } elsif $::osfamily == 'Debian' {

       } else {

       }

       }

       {{< /file >}}

3.
For our CentOS 7 server, the virtual hosts file is located at `/etc/httpd/conf.d/vhost.conf`, while for Ubuntu, it's at `/etc/apache2/sites-available/example.com.conf`, where example.com should be replaced with the server's FQDN. We need to create `templates` for both files on the Puppet master. Navigate to the templates directory within the `apache` module, and then create two files for your virtual hosts.

For Red Hat systems:

    {{< file "/etc/puppet/modules/apache/templates/vhosts-rh.conf.erb" aconf >}}
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

In these files, we'll only use two variables: `adminemail` and `servername`. We'll define these variables individually for each node in the site.pp file.

4. Return to the `vhosts.pp` file. You can now refer to the templates created in the code.

       {{< file "/etc/puppet/modules/apache/manifests/vhosts.pp" puppet >}}
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
       print "This is not a supported distro."
       }

       }

       {{< /file >}}

Both distribution families utilize the `file` resource, with the title representing the location of the virtual host on each distribution. For Debian, this involves referencing the `$servername` value once again. The `content` attribute refers to the respective templates.

{{< note respectIndent=false >}}
When dealing with values containing variables, such as the name of the Debian file resource mentioned earlier, it's essential to enclose them in double quotes (`"`). Variables within single quotes (`'`) are interpreted exactly as written and won't substitute the variable's value.
{{< /note >}}

5.  Both virtual hosts files mention two directories not existing by default in the distributions. These directories can be created using the `file` resource, each positioned within the `if` statement. The complete `vhosts.conf` file should appear as follows:

        {{< file "/etc/puppet/modules/apache/manifests/vhosts.pp" puppet >}}
        class apache::vhosts {

        if $::osfamily == 'RedHat' {
        file { '/etc/httpd/conf.d/vhost.conf':
        ensure    => file,
        content   => template('apache/vhosts-rh.conf.erb'),
        }
        file { "/var/www/$servername":
        ensure    => directory,
        }
        file { "/var/www/$servername/public_html":
        ensure    => directory,
        }
        file { "/var/www/$servername/log":
        ensure    => directory,
        }

        } elsif $::osfamily == 'Debian' {
        file { "/etc/apache2/sites-available/$servername.conf":
        ensure  => file,
        content  => template('apache/vhosts-deb.conf.erb'),
        }
        file { "/var/www/$servername":
        ensure    => directory,
        }
        file { "/var/www/html/$servername/public_html":
        ensure    => directory,
        }
        file { "/var/www/html/$servername/logs":
        ensure    => directory,
        }
        } else {
        print "This is not a supported distro."
        }

        }

        {{< /file >}}

### Test and Run the Module

1. Navigate to the `apache/manifests/` directory, then execute the `puppet parser` command on all files to verify that the Puppet code is error-free.

        sudo puppet parser validate init.pp params.pp vhosts.pp

It should display no output unless there are issues.

2. Go to the examples directory within the apache module. Create a file named `init.pp` and incorporate the defined classes. Specify variables for `servername` and `adminemail`.

       {{< file "/etc/puppet/modules/apache/examples/init.pp" puppet >}}
       $serveremail = 'webmaster@example.com'
       $servername = 'example.com'

       include apache
       include apache::vhosts

        {{< /file >}}

3. Test the module by executing puppet apply with the `--noop` flag.:

        sudo puppet apply --noop init.pp

It should display no errors and indicate that it will trigger refreshes from events. To install and configure Apache on the Puppet master, you can rerun the command without the `--noop` flag, if desired.

4. Navigate back to the main Puppet directory, then access the manifests folder (not the one located in the Apache module). If you've followed the [Puppet Setup](/docs/guides/install-and-configure-puppet/) guide, you should already have a site.pp file. If not, create one now.

5. Open the `site.pp` file and include the Apache module for each agent node. Also, provide values for the `adminemail` and `servername` parameters. If you followed the [Puppet Setup](/docs/guides/install-and-configure-puppet/) guide, a configuration for a single node within `site.pp` will look like the following:

       {{< file "/etc/puppet/manifests/site.pp" puppet >}}
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


6. To apply the new module on your agent nodes, log in to each node and execute the following command:

        sudo puppet agent -t

## Using the MySQL Module

You can find many ready-to-use server modules on [Puppet Forge](https://forge.puppet.com), such as the Puppet Labs MySQL module. These modules can be customized extensively and save time because you don't have to build them from scratch.

To install the [Puppet Forge's MySQL module](https://forge.puppet.com/puppetlabs/mysql) by Puppet Labs, use the following command:

    sudo puppet module install puppetlabs-mysql

This will also automatically install any required modules.

###Create Databases Using Hiera

Before you start creating configuration files for the MySQL module, think about whether you want to use different values for each agent node. To provide Puppet with the right data for each node, we'll use Hiera. In this case, we'll use different root passwords for each node, resulting in different MySQL databases.

1.  Go to `/etc/puppet` and make a new configuration file named `hiera.yaml` in the main puppet directory:

        {{< file "/etc/puppet/hiera.yaml" yaml >}}
        :backends:
        - yaml
        :yaml:
        :datadir: /etc/puppet/hieradata
        :hierarchy:
        - "nodes/%{::fqdn}"
        - common

        {{< /file >}}

Under `:backends:`, specify that you're using YAML for data storage, while `:datadir:` points to the directory where Hiera data will be stored. In the `:hierarchy:` section, indicate that your data will be stored in files within the `node` directory, with filenames matching the node's FQDN. Additionally, include a `common` file containing default variables.

2. Make sure you're in the `/etc/puppet/` directory, then create directories

       sudo mkdir -p hieradata/nodes

3. Navigate to the `/etc/puppet/` directory and create directories for `hieradata` and `nodes`:

        cd hieradata/nodes

4.  Utilize the `puppet cert` command to list all available nodes. Then, create a YAML file for each node, naming each file after its FQDN.

        sudo puppet cert list --all
        sudo touch {ubuntuhost.example.com.yaml,centoshost.example.com.yaml}

5. Open the configuration file for the first node to define the initial database. For instance, let's name this database `webdata1`. We'll specify a self-defined `username` and `password`. The grant value provides the user with full access to the `webdata1` database.

       {{< file "/etc/puppet/hieradata/nodes/ubuntuhost.example.com.yaml" yaml >}}
       databases:
       webdata1:
       user: 'username'
       password: 'password'
       grant: 'ALL'

       {{< /file >}}

Repeat the process for the second server. For this instance, let's name the database `webdata2`.

    {{< file "/etc/puppet/hieradata/nodes/centoshost.example.com.yaml" yaml >}}
    databases:
    webdata2:
    user: 'username'
    password: 'password'
    grant: 'ALL'

    {{< /file >}}

Save and close the files once you're done editing them.

6. Go back to the `hieradata` directory and create a file named `common.yaml`. This file will set the default `root` password for MySQL.

        {{< file "/etc/puppet/hieradata/common.yaml" yaml >}}
        mysql::server::root_password: 'password'

        {{< /file >}}

The `common.yaml` file serves as a fallback when a variable is not defined elsewhere. This means that all servers will share the same MySQL root password. These passwords can also be hashed for added security.

7. Now, Puppet needs to know how to use the information provided in Hiera to create the specified database. Navigate to the `mysql` module directory and create a file named `database.pp` within the manifests directory. In this file, define a class that links the `mysql::db` resource to the Hiera data. Additionally, include the `mysql::server` class so it won't need to be included separately later.

       {{< file "/etc/puppet/modules/mysql/manifests/database.pp" puppet >}}
       class mysql::database {

       include mysql::server

       create_resources('mysql::db', hiera_hash('databases'))
       }

       {{< /file >}}


8.  Add `include mysql::database` to your `site.pp` file for both nodes.

## Create the PHP Module

1. Create a directory named `php` in the `modules` path.
2. Inside the `php` directory, create the following sub-folders: `files`, `manifests`, `templates`, and `examples`.

        sudo mkdir php
        cd php
        sudo mkdir {files,manifests,examples,templates}

3. Create and open a file named `init.pp`. Since the goal is to install PHP and ensure the PHP service is enabled and starts on boot, all code will be written in this file.

4. Install two packages: the PHP package itself and the PHP Extension and Application Repository (PEAR). Use the `package` resource to achieve this.

       {{< file "/etc/puppet/modules/php/manifests/init.pp" puppet >}}
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

Since the `php` package has different names on Ubuntu and CentOS, it needs to be defined with a parameter. However, since this is the only parameter we'll need, it will be directly added to the `init.pp` file.

    {{< file "/etc/puppet/modules/php/manifests/init.pp" puppet >}}
    class php {

    $phpname = $osfamily ? {
    'Debian'    => 'php5',
    'RedHat'    => 'php',
     default     => warning('This distribution is not supported by the PHP module'),
     }

    package { 'php':
    name    => $phpname,
    ensure  => present,
    }

    package { 'php-pear':
    ensure  => present,
     }

     }

    {{< /file >}}


4.  Utilize the `service` resource to ensure that PHP is running and set to start automatically at boot.

        {{< file "/etc/puppet/modules/php/manifests/init.pp" puppet >}}
        class php {

        $phpname = $osfamily ? {
        'Debian'    => 'php5',
        'RedHat'    => 'php',
         default     => warning('This distribution is not supported by the PHP module'),
         }

        package { 'php':
        name    => $phpname,
        ensure  => present,
        }

        package { 'php-pear':
        ensure  => present,
        }

        service { 'php-service':
        name    => $phpname,
        ensure  => running,
        enable  => true,
        }

        }

        {{< /file >}}


5. Include `include php` in the host configurations within your `sites.pp` file. Then, execute `puppet agent -t` on your agent nodes to apply any changes to your servers.
