---
slug: "Mastering MySQL: Puppet Hiera Database Wizardry for Ubuntu 16.04"
description: "Explore this guide to effortlessly deploy Puppet alongside MySQL modules and Puppet Hiera configuration manifests. Streamline MySQL management across diverse environments with ease"
keywords: ["puppet installation", "configuration change management", "server automation", "mysql", "database", "hiera"]
tags: ["database","ubuntu","automation","mysql"]
published: 2024-03-27
modified: 2024-03-27
modified_by:
    name: Utho
title: ""Database Wizardry: Puppet Hiera's Power Unleashed for MySQL on Ubuntu 16.04"
deprecated: true
deprecated_link: 'applications/configuration-management/install-and-manage-mysql-databases-with-puppet-hiera-on-ubuntu-18-04/'
relations:
    platform:
        key: install-puppet-mysql-hiera
        keywords:
            - distribution: Ubuntu 16.04

---

Install and Manage MySQL Databases with Puppet Hiera on Ubuntu 16.04

[Puppet](https://puppet.com/) is a configuration management system designed to simplify software deployment and system administration. In this guide, we leverage Puppet to manage the installation of [MySQL](https://www.mysql.com/), a widely-used relational database powering applications like WordPress and Ruby on Rails. We'll also utilize [Hiera](https://docs.puppet.com/hiera/) to streamline MySQL configuration.

Throughout this guide, you'll deploy Puppet [modules](https://docs.puppet.com/puppet/latest/modules_fundamentals.html) on your server. By the end, MySQL will be installed, configured, and ready for use in various applications requiring a database backend.

{{< note respectIndent=false >}}
This guide is tailored for a non-root user. Commands requiring elevated privileges are prefixed with `sudo`. If you're unfamiliar with the `sudo` command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
{{< /note >}}

## Before You Begin

1. If you haven't already, create a Utho account and Compute Instance by referring to our Getting Started with Utho and Creating a Compute Instance guides.

2. Follow our Setting Up and Securing a Compute Instance guide to update your system. You might want to set the timezone, configure your hostname, create a limited user account, and enhance SSH access security as well.

## Install and Configure Puppet

Follow these steps to set up Puppet for a single-host, local-only deployment. If you require configuration for multiple servers or need to deploy a Puppet master, refer to our [multi-server Puppet guide](/docs/guides/install-and-configure-puppet/).

### Install the Puppet Package

1. Install the `puppetlabs-release` repository to incorporate the Puppet packages:

        wget https://apt.puppet.com/puppetlabs-release-pc1-xenial.deb
        sudo dpkg -i puppetlabs-release-pc1-xenial.deb

2. Update the apt package index to access the Puppet Labs repository packages, then proceed to install Puppet. This process will install the `puppet-agent` package, offering the `puppet` executable within a compatible Ruby environment:

        sudo apt update && sudo apt install puppet-agent=1.10.4-1xenial

3. Verify the installed version of Puppet:

        puppet --version

Please note that the `puppet-agent` package contains a distinct version of Puppet, as anticipated:

        4.10.4

### Install the Puppet MySQL Module

[Puppet Forge](https://forge.puppet.com/) offers a repository of modules designed to simplify the installation of various software. The [MySQL module](https://forge.puppet.com/puppetlabs/mysql) specifically facilitates the installation and configuration of MySQL, eliminating the need to manually manage multiple configuration files and services.

1. Install the MySQL module using the following command:

        sudo -i puppet module install puppetlabs-mysql --version 3.11

This command will install the `mysql` module into the default directory path: /`etc/puppetlabs/code/environments/production/modules/`.

### Puppet MySQL Manifest

In this guide, a Puppet manifest is utilized to furnish Puppet with installation and configuration directives. Alternatively, you can set up [a Puppet master](/docs/guides/install-and-configure-puppet/).

While a Puppet manifest can encompass the entire configuration for a host, Hiera configuration files can streamline the process by defining values for Puppet classes or types. In this scenario, parameters for the `mysql::server` class will be specified in Hiera. However, the class must first be applied to the host.

To apply the `mysql::server` class to all hosts by default, create the following Puppet manifest:

    {{< file "/etc/puppetlabs/code/environments/production/manifests/site.pp" puppet >}}
    include ::mysql::server

    {{< /file >}}

Keep in mind that `site.pp` serves as the default manifest file. When there's no specific `node { .. }` line, the class gets applied to any host utilizing the manifest. Now, Puppet is aware of applying the `mysql::server` class, but it still requires values for resources such as databases, users, and other settings. In the next section, we'll configure Hiera to supply these values.

## Install and Configure Puppet Hiera

To grasp the workings of Hiera, let's examine this snippet from the default `hiera.yaml` file:

    {{< file "/etc/puppetlabs/puppet/hiera.yaml" yaml >}}
    ---
    :backends:
    - yaml
    :hierarchy:
    - "nodes/%{::trusted.certname}"
    - common

    {{< /file >}}

This Hiera configuration directs Puppet to retrieve variable values from `nodes/%{::trusted.certname}.yaml`. For instance, if your Utho's hostname is examplehostname, create a file named `nodes/examplehostname.yaml`. Variables found in YAML files higher in the hierarchy take precedence, while any variables not found in those files will default to files lower in the hierarchy (such as `common.yaml` in this case).

The subsequent configuration will define Puppet variables in common.yaml to supply variables for the `mysql::server` class.

### Initial Hiera Configuration

Hiera configuration files are structured as YAML, where keys define Puppet parameters along with their corresponding values. To begin, let's set the MySQL root password. The Puppet manifest example below demonstrates one method of managing this password:

    {{< file "example.pp" >}}
    class { '::mysql::server':
    root_password => 'examplepassword',
    }
    {{< /file >}}

Alternatively, we can specify the root password using the Hiera configuration file shown below. Create the YAML file and observe how the `root_password` parameter is defined in Hiera YAML format:

    {{< file "/etc/puppetlabs/code/environments/production/hieradata/common.yaml" >}}
    mysql::server::root_password: examplepassword
    {{< /file >}}

Replace `examplepassword` with your preferred secure password. Then, execute Puppet to configure MySQL with default settings and the selected root password:

    sudo -i puppet apply /etc/puppetlabs/code/environments/production/manifests/site.pp

Puppet will display its progress before finishing the setup. To verify that MySQL has been configured correctly, execute the following command:

    mysql -u root -p -e 'select version();'

Enter the password, and MySQL will display its version:

    +-------------------------+
    | version()               |
    +-------------------------+
    | 5.7.19-0ubuntu0.16.04.1 |
    +-------------------------+

### Define MySQL Resources

Using Hiera, we can fully define the MySQL configuration in YAML format. Follow these steps to create a database and user for a WordPress installation:

1. Generate a pre-hashed MySQL password. Replace `wordpresspassword` with your desired password. When prompted for the root MySQL password, use the root password chosen earlier. Take note of the string starting with a * returned by the command in Step 2.

        mysql -u root -p -NBe 'select password("wordpresspassword")'
        *E62D3F829F44A91CC231C76347712772B3B9DABC

2. With the MySQL password hash ready, we can define Hiera values. The following YAML snippet defines parameters to create a database named `wordpress` and a user named `wpuser`. This user is granted permission to connect from `localhost` and has full privileges (ALL) on the `wordpress` database:

       {{< file "/etc/puppetlabs/code/environments/production/hieradata/  common.yaml" yaml >}}
       mysql::server::root_password: examplepassword
       mysql::server::databases:
       wordpress:
       ensure: present
       mysql::server::users:
       wpuser@localhost:
       ensure: present
       password_hash: '*E62D3F829F44A91CC231C76347712772B3B9DABC'
       mysql::server::grants:
       wpuser@localhost/wordpress.*:
       ensure: present
       privileges: ALL
       table: wordpress.*
       user: wpuser@localhost

       {{< /file >}}


3. Run Puppet again:

        sudo -i puppet apply /etc/puppetlabs/code/environments/production/manifests/site.pp

4. Now, the `wpuser` should be able to access the `wordpress` database. To confirm, connect to the MySQL daemon as the `wpuser` user to the `wordpress` database:

        mysql -u wpuser -p wordpress

After entering the password for wpuser, exit the MySQL prompt:

        exit

### Add Hierarchies for Specific Environments

You can add additional configurations that are only applied to specific environments. For instance, backup jobs might only be applied to hosts in a particular region, or certain databases might be created in a specific deployment.

In the example below, Puppet will configure the MySQL server with an additional database, but only if the server's distribution is Debian-based.

1. Update hiera.yaml with the following content:

      {{< file "/etc/puppetlabs/puppet/hiera.yaml" yaml >}}
       ---
       :backends:
       - yaml
       :hierarchy:
       - "%{facts.os.family}"
       - common

       {{< /file >}}

This modification directs Hiera to search for Puppet parameters initially in `"%{facts.os.family}.yaml"` and then in `common.yaml`. The first part of the hierarchy, based on facts, is dynamic and depends on the host controlled by Puppet and Hiera. For instance, in an Ubuntu-based scenario, Hiera will search for `Debian.yaml`.

2.  Create the YAML file shown below:

        {{< file "/etc/puppetlabs/code/environments/production/hieradata/Debian.yaml" yaml >}}
        lookup_options:
        mysql::server::databases:
        merge: deep

        mysql::server::databases:
        ubuntu-backup:
        ensure: present

        {{< /file >}}

Similar to the `common.yaml` file described in earlier steps, this file will exclusively add the ubuntu-backup database on Debian-based hosts (such as Ubuntu). Furthermore, the lookup_options setting ensures that the `mysql::server:databases` parameter is merged across `Debian.yaml` and `common.yaml`, ensuring that all databases are managed. Without lookup_options configured to deeply merge these hashes, only the most specific hierarchy file (in this case, `Debian.yaml`) would be applied to the host.

*  Alternatively, since our Puppet manifest is concise, we can test the same command using the -e flag to apply an inline manifest:

            sudo -i puppet apply -e 'include ::mysql::server'

3. Execute Puppet and observe the changes:

        sudo -i puppet apply /etc/puppetlabs/code/environments/production/manifests/site.pp

4. Check for the presence of the newly created ubuntu-backup database:

        mysql -u root -p -e 'show databases;'

This includes the new `ubuntu-backup` database:

        +---------------------+
        | Database            |
        +---------------------+
        | information_schema  |
        | mysql               |
        | performance_schema  |
        | sys                 |
        | ubuntu-backup       |
        | wordpress           |
        +---------------------+

Congratulations! You've successfully configured your Puppet setup using highly customizable Hiera definitions.
