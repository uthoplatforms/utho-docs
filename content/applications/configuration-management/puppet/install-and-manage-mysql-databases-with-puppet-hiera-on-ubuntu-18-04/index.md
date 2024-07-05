---
slug: "Master MySQL Magic: Puppet & Hiera Unleashed on Ubuntu 18.04"
description: "Explore this guide to deploy Puppet alongside MySQL modules and Puppet Hiera configuration manifests, empowering you to efficiently manage MySQL across diverse environments on Ubuntu 18.04"
keywords: ["puppet installation", "configuration change management", "server automation", "mysql", "database", "hiera"]
tags: ["database","ubuntu","automation","mysql"]
published: 2024-03-27
modified: 2024-03-27
modified_by:
    name: Utho
title: "Unleash Puppet's Power: MySQL Management with Hiera on Ubuntu 18.04"
relations:
    platform:
        key: install-puppet-mysql-hiera
        keywords:
            - distribution: Ubuntu 18.04
authors: ["Utho"]
---

[Puppet](https://puppet.com/) is a configuration management system that streamlines software deployment and system administration. In this guide, we harness Puppet's power to oversee an installation of [MySQL](https://www.mysql.com/), a widely-used relational database employed in applications like WordPress and Ruby on Rails. We'll leverage [Hiera](https://docs.puppet.com/hiera/), a tool for defining configuration values, to simplify MySQL setup.

Throughout this guide, you'll deploy Puppet [modules](https://docs.puppet.com/puppet/latest/modules_fundamentals.html) on your server. By the end, you'll have MySQL installed, configured, and primed for use across a range of applications requiring a robust database backend.

Note: This guide is for users without full system access. You'll notice commands with `sudo` at the start, indicating they need extra permissions. If you're unsure about `sudo`, check out our [Users and Groups](/docs/guides/linux-users-and-groups/) guide.

## Before You Begin

1. If you haven't yet, create a Utho account and set up a Compute Instance.
2. Follow our guide to update your system. Additionally, consider setting the timezone, configuring your hostname, creating a limited user account, and strengthening SSH access.

## Install and Configure Puppet

Follow these steps to set up Puppet for a single-host, local-only deployment. If you need to configure more than one server or deploy a Puppet master, refer to our [multi-server Puppet guide](/docs/guides/install-and-configure-puppet/).

### Install the Puppet Package

1. Install the puppetlabs-release-bionic repository to add the Puppet packages:

        wget https://apt.puppet.com/puppet-release-bionic.deb
        sudo dpkg -i puppet-release-bionic.deb

2. Update the apt package index to make the Puppet Labs repository packages available, then install Puppet. This will install the puppet-agent package, which provides the puppet executable within a compatible Ruby environment:

        sudo apt update && sudo apt install puppet-agent

3. Confirm the version of Puppet installed by running the following command:

        puppet --version

At the time of writing, the Puppet version is `6.1.0`.

### Install the Puppet MySQL Module

[Puppet Forge](https://forge.puppet.com/) is a platform that offers a variety of modules to simplify software installation. The [MySQL module](https://forge.puppet.com/puppetlabs/mysql) module is one such module designed to automate the installation and configuration of MySQL, eliminating the need to manually manage configuration files and services.

1. To install the MySQL module, use the following command:

        sudo puppet module install puppetlabs-mysql --version 7.0.0

The command will install the mysql module into the default directory: `/etc/puppetlabs/code/environments/production/modules/`.

### Puppet MySQL Manifest

This guide uses a Puppet manifest to provide installation and configuration instructions. Alternatively, you can configure a [a Puppet master](/docs/guides/install-and-configure-puppet/).

While a Puppet manifest can contain the entire configuration for a host, values for Puppet classes or types can also be defined in a Hiera configuration file to simplify writing Puppet manifests. In this example, we'll define the parameters for the `mysql::server` class in Hiera, but first, we need to apply the class to the host.

To apply the `mysql::server` class to all hosts by default, create the following Puppet manifest:

    {{< file "/etc/puppetlabs/code/environments/production/manifests/site.pp"  puppet >}}
    include ::mysql::server
    {{< /file >}}

Note: `site.pp` is the default manifest file. Without a qualifying `node { .. }` line, this applies the class to any host using the manifest. Puppet now knows to apply the `mysql::server` class, but still needs values for resources like databases, users, and other settings. Next, we'll configure Hiera to provide these values.

## Install and Configure Puppet Hiera

To understand how Hiera works, let's look at an excerpt from the default hiera.yaml file:

    {{< file "/etc/puppetlabs/code/environments/production/hiera.yaml" yaml >}}
    ---
    version: 5
    hierarchy:
    - name: "Per-node data"
    path: "nodes/%{::trusted.certname}.yaml"
    - name: "Common data"
    path: "common.yaml"
    {{< /file >}}

This Hiera configuration tells Puppet to fetch variable values from nodes/`%{::trusted.certname}.yaml`. For instance, if your Utho's hostname is examplehostname, you should create a file named nodes/examplehostname.yaml. Variables found in YAML files higher in the hierarchy take precedence. If a variable isn't found in those files, Puppet will check files lower in the hierarchy (like common.yaml).

Here's an example configuration that defines Puppet variables in common.yaml to inject values into the mysql::server class.

### Initial Hiera Configuration

Hiera configuration files are formatted as yaml, with keys defining the Puppet parameters to inject their associated values. To get started,  set the MySQL root password. The following example of a Puppet manifest is one way to control this password:

       {{< file "example.pp" >}}
       class { '::mysql::server':
       root_password => 'examplepassword',
       }
       {{< /file >}}

We can also define the root password with the following Hiera configuration file. Create the following YAML file and note how the `root_password` parameter is defined as Hiera yaml:

    {{< file "/etc/puppetlabs/code/environments/production/ data/common.yaml" >}}
    mysql::server::root_password: examplepassword
    {{< /file >}}

Replace `examplepassword` with the secure password of your choice. Run Puppet to set up MySQL with default settings and the chosen root password:

    sudo -i puppet apply /etc/puppetlabs/code/environments/production/manifests/site.pp

Puppet will output its progress before completing. To confirm MySQL has been configured properly, run a command:

    mysql -u root -p -e 'select version();'

Enter the password and MySQL returns its version:

    +-------------------------+
    | version()               |
    +-------------------------+
    | 5.7.24-0ubuntu0.18.04.1 |
    +-------------------------+

### Define MySQL Resources

Using Hiera, we can define the rest of the MySQL configuration entirely in yaml. The following steps will create a database and user for use in a WordPress installation.

1.  Create a pre-hashed MySQL password. Replace the password `wordpresspassword` in this example, and when prompted for a the root MySQL password, use the first root password chosen in the previous section to authenticate. Note the string starting with a `*` that the command returns for Step 2:

        mysql -u root -p -NBe 'select password("wordpresspassword")'
        *E62D3F829F44A91CC231C76347712772B3B9DABC

2.  With the MySQL password hash ready, we can define Hiera values. The following YAML defines parameters to create a database called `wordpress` and a user named `wpuser` that has permission to connect from `localhost`. The YAML also defines a `GRANT` allowing `wpuser` to operate on the `wordpress` database with `ALL` permissions:


       {{< file "/etc/puppetlabs/code/environments/production/data/common.yaml" yaml >}}
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

1.  Re-run Puppet:

        sudo -i puppet apply /etc/puppetlabs/code/environments/production/manifests/site.pp

2.  The `wpuser` should now be able to connect to the `wordpress` database. To verify, connect to the MySQL daemon as the user `wpuser` to the `wordpress` database:

        mysql -u wpuser -p wordpress

After you enter the password for `wpuser`, exit the MySQL prompt:

           exit

### Add Hierarchies for Specific Environments

Additional configurations can be added that will only be applied to specific environments. For example, backup jobs may only be applied for hosts in a certain region, or specific databases can be created in a particular deployment.

In the following example, Puppet will configure the MySQL server with one additional database, but only if that server's distribution is Debian-based.

Modify `hiera.yaml` to contain the following:

       {{< file "/etc/puppetlabs/code/environments/production/hiera.yaml" yaml >}}
       ---
      version: 5
      hierarchy:
      - name: "Per OS Family"
      path: "os/%{facts.os.family}.yaml"
      - name: "Other YAML hierarchy levels"
       paths:
      - "common.yaml"
        {{< /file >}}

This change instructs Hiera to look for Puppet parameters first in `"os/%{facts.os.family}.yaml"` and then in `common.yaml`. The first, fact-based element of the hierarchy is dynamic, and dependent upon the host that Puppet and Hiera control. In this Ubuntu-based example, Hiera will look for `Debian.yaml` in the `os` folder, while on a distribution such as CentOS, the file `RedHat.yaml` will automatically be referenced instead.

Create the following YAML file:

    {{< file "/etc/puppetlabs/code/environments/production/data/os/Debian.yaml" yaml >}}
    lookup_options:
    mysql::server::databases:
    merge: deep

    mysql::server::databases:
    ubuntu-backup:
    ensure: present

    {{< /file >}}

Though similar to the `common.yaml` file defined in previous steps, this file will add the `ubuntu-backup` database *only* on Debian-based hosts (like Ubuntu). In addition, the `lookup_options` setting ensures that the `mysql::server:databases` parameter is *merged* between `Debian.yaml` and `common.yaml` so that all databases are managed. Without `lookup_options` set to deeply merge these hashes, only the most specific hierarchy file will be applied to the host, in this case, `Debian.yaml`.

Alternatively, because our Puppet manifest is short, we can test the same command using the `-e` flag to apply an inline manifest:

            sudo -i puppet apply -e 'include ::mysql::server'

Run Puppet and observe the changes:

        sudo -i puppet apply /etc/puppetlabs/code/environments/production/manifests/site.pp

Verify that the new database exists:

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

Congratulations! You can now control your Puppet configuration via highly configurable Hiera definitions.
