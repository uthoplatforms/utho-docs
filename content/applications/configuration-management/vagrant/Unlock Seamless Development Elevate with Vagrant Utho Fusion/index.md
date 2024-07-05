---
slug: "Unlock Seamless Development: Elevate with Vagrant Utho Fusion"
description: "Dive into Effortless Development: Harness Vagrant's Power to Seamlessly Manage Utho Environments via Utho API Integration"
keywords: ["Utho", "vagrant", "content management", "management", "automation", "development", "ruby", "vagrantfile", "api", "apache"]
tags: ["cms","automation","ruby","apache"]
modified: 2024-04-04
modified_by:
    name: Pawan Kumar
published: 2024-04-04
title: 'Using Vagrant to Manage Utho Environments'
deprecated: true
authors: ["Pawan Kumar"]
---
[Vagrant](http://www.vagrantup.com) simplifies managing work environments by allowing users to create consistent and portable setups. It's great for ensuring all team members have the same development environment. Vagrant is easy to set up, use, and discard when no longer needed. Paired with tools like Puppet, Salt, or Chef, it ensures workflow consistency.

With the vagrant-utho plugin, Vagrant seamlessly integrates with Utho, enabling users to create and remove Utho servers as required. This guide walks you through installing Vagrant, configuring the vagrant-utho plugin, and setting up a basic Apache server for testing.

## Prerequisites

1. Download and  [Install Vagrant](https://www.vagrantup.com/downloads.html) from here on your local computer or workspace.

2. Generate an API Key for creating Uthos based on your Vagrant profile:

- Log in to the Utho Manager and click on **my profile** in the upper right corner.

- Navigate to the API Keys tab.

- Provide a label for your API Key and set an expiration time. Then, click on **Create API Key&**.

- Your API Key will appear in a green box. Remember, it will only be displayed once, so make sure to save it for future use.

## Install the vagrant-utho Plugin

1. Create a directory for your project from your workspace and navigate into it.

        mkdir ~/vagrant-utho
        cd vagrant-utho

2.  Install the plugin:

        vagrant plugin install vagrant-utho

{{< note respectIndent=false >}}
If you're on a Mac, it might ask to install development tools. Choose yes, and then run the command again.
{{< /note >}}

3.  Create the Vagrantfile from within the `vagrant-utho` directory.

        touch Vagrantfile

The *Vagrantfile* serves as a code-based description of the machine Vagrant will create. It specifies the operating system, users, and any necessary initial applications to ensure a consistent work environment

## Configure the Vagrantfile

1. Open the Vagrantfile in your preferred text editor. In Ruby, specify the version of Vagrant you're using. Use `2` for Vagrant versions 1.1.0 and beyond, and `1` for any versions prior to that.

       {{< file "~/vagrant-utho/Vagrantfile" ruby >}}
       Vagrant.configure('2') do |config|

        end

{{< /file >}}

All code should be written between the `Vagrant.configure` and `end` lines.

2. When setting up a guest machine (the server to be created), Vagrant automatically generates a `username`, password, and private key for accessing the machine. By default, the username and password are both vagrant. You can customize these parameters by defining your own username and specifying the path to your private key. If you haven't generated a private and public key yet, you can do so by following the [Securing Your Server](/docs/products/compute/compute-instances/guides/set-up-and-secure/#create-an-authentication-key-pair).

       {{< file "~/vagrant-utho/Vagrantfile" ruby >}}
       Vagrant.configure('2') do |config|

       ## SSH Configuration
       config.ssh.username = 'user'
       config.ssh.private_key_path = '~/.ssh/id_rsa'

       end

{{< /file >}}

You also have the option to set your own password using the `config.ssh.password` setting.

3. Next, specify the Utho provider.

        {{< file "~/vagrant-utho/Vagrantfile" ruby >}}
        Vagrant.configure('2') do |config|

        ...

        # Global Configuration
        config.vm.provider :utho do |provider, override|
        override.vm.box = 'utho'
        override.vm.box_url = "https://github.com/displague/vagrant-utho/raw/master/box/utho.box"
        provider.token = 'API-KEY'
        end

        end

{{< /file >}}

Line 6 specifies the provider, while lines 7 and 8 define the *box*. Boxes are packages containing the fundamental components necessary for a Vagrant environment to operate. The provided box is named `utho`, which is crafted as part of the plugin. Replace `API-KEY` with the key generated [above](#prerequisites).

4.  Customize your Utho's settings:

         {{< file "~/vagrant-utho/Vagrantfile" ruby >}}
         Vagrant.configure('2') do |config|

         ...

         # Global Configuration
         config.vm.provider :utho do |provider, override|

         ...

         #Utho Settings
         provider.distribution = 'Ubuntu 14.04 LTS'
         provider.datacenter = 'newark'
         provider.plan = '2048'
         provider.label = 'vagrant-ubuntu-lts'

         end

         end

{{< /file >}}

In this example, we're creating a 2GB Ubuntu 14.04 LTS Utho in the Newark data center. The `provider.label` determines the name the Utho will appear as in the Utho Manager.

For additional options regarding the vagrant-utho plugin, refer to the documentation available on the plugin's GitHub repository.

## Set Up the Vagrant Box
While the server creation process is successful, various configurations still need to be set up. We'll use shell scripts to accomplish tasks outlined in the Setting Up and Securing a Compute Instance guide. Additionally, Apache installation and configuration will be handled. Furthermore, files will be synced between the workstation and the Utho.

### Configure the Server

1. To start, create a shell script named `setup.sh`. This script will configure the Utho's hostname, set the correct timezone, and update the server. Ensure to replace `vagranttest` with your desired hostname and `EST` with your timezone.

       {{< file "~/vagrant-utho/setup.sh" shell >}}
      #!/bin/bash
       echo "vagranttest" > /etc/hostname
       hostname -F /etc/hostname
       ip=$(ip addr show eth0 | grep -Po 'inet \K[\d.]+')
       echo "$ip   $ip hostname" >> /etc/hosts
       ln -sf /usr/share/zoneinfo/EST /etc/localtime
       apt-get update && apt-get upgrade -y

{{< /file >}}

- Lines 2 and 3: Define the hostname.

- Line 4: Sets the variable ip to the Utho's IP address, as we won't know the IP address until Vagrant launches the Utho.

- Line 5: Inserts the IP address into the `/etc/hosts` file to define the fully-qualified domain name.

- Line 6: Sets the timezone.

- The final line updates the server and server packages.

Now, within the `Vagrantfile`, you'll call the shell script you just created by adding the `config.vm.provision` method.

    {{< file "~/vagrant-utho/Vagrantfile" ruby >}}
    Vagrant.configure('2') do |config|

    ...

    # Shell Scripts
    config.vm.provision :shell, path: "setup.sh"

    end

{{< /file >}}

### Install Apache and Sync Files

1.  Prepare an installation script for Apache named `apache.sh` and include the following commands:

        {{< file "~/vagrant-utho/apache.sh" shell >}}
        #!/bin/bash
        apt-get install apache2 -y
        mv /etc/apache2/ports.conf /etc/apache2/ports.conf.backup
        mv /etc/apache2/ports1.conf /etc/apache2/ports.conf
        a2dissite 000-default.conf
        a2ensite vhost.conf
        service apache2 reload

        {{< /file >}}


- Line 2: Installs Apache.

- Lines 3 & 4: Create a backup of the `ports.conf` file and replace it with a new file, as specified below.

- Lines 5 & 6: Disable the default host file and enable the new one we'll create below. Then, Apache is reloaded to apply the configuration changes.

Next, add the shell script provisioner method to your `Vagrantfile`, below the line that references setup.sh.

    {{< file "~/vagrant-utho/Vagrantfile" ruby >}}
    Vagrant.configure('2') do |config|

    ...

    # Shell Scripts
    config.vm.provision :shell, path: "setup.sh"
    config.vm.provision :shell, path: "apache.sh"

    end

{{< /file >}}

3. Set up a new directory for Apache configuration files:

        mkdir apache2

4.  Since Vagrant is commonly used for development environments, we prefer to host Apache on a port other than 80. Create `ports1.conf`, as mentioned in the shell script earlier. Set the port to **6789**:

        {{< file "~/vagrant-utho/apache2/ports1.conf" aconf >}}
        Listen 6789

        <IfModule ssl_module>
        Listen 443
        </IfModule>

        <IfModule mod_gnutls.c>
        Listen 443
        </IfModule>

{{< /file >}}

5.  Create a new directory named `sites-available` under `apache2`. Within this newly created directory, add a `VirtualHosts` file named `vhost.conf`:

        mkdir sites-available

        {{< file "~/vagrant-utho/apache2/sites-available/vhost.conf" aconf >}}
        <VirtualHost *:6789>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
        </VirtualHost>

{{< /file >}}

6.  Return to the Vagrantfile, and use the `config.vm.synced_folder` method to sync the local directories with directories on the server:

        {{< file "~/vagrant-utho/Vagrantfile" ruby >}}
        Vagrant.configure('2') do |config|

        ...

        # Synced Folders
        config.vm.synced_folder '.', '/vagrant', disabled: true
        config.vm.synced_folder './apache2', '/etc/apache2', disabled: false
        config.vm.synced_folder './webfiles', '/var/www/html'

        end

{{< /file >}}

- Line 5: Disables syncing for the root folders.

- Line 6: Specifies the locally hosted apache2 folder (`'./apache2'`) and connects it to the `/etc/apache2` directory on the Utho. The disabled: false setting ensures that it will synchronize.

- Line 7: Performs the same operation with a directory named `./webfiles`, which is yet to be created. This directory can be used to add any website files before booting the instance.

Now, create the webfiles folder in your `vagrant-utho` directory.

        mkdir ~/vagrant-utho/webfiles

Place any files you want the Utho to serve over HTTP into this directory.

## Boot an Instance

Now that the Vagrantfile is configured and the scripts and files are created, it's time to create the guest machine and verify its functionality.

1.  Boot the instance from your workstation:

        vagrant up

The installation process will commence, directories will be synced, and shell scripts will run.

2. Log into the newly-created Utho:

        vagrant ssh

3. To confirm Apache's proper functioning, check its status:

        service apache2 status

It should display:

         * apache2 is running

4. To ensure accessibility online, retrieve the IP address:

        hostname -i

Visit your chosen web browser and navigate to your IP address with `:6789` appended to the end. You should observe the Apache2 Ubuntu Default Page.

{{< note respectIndent=false >}}

If you want to shut down or remove the Utho from your workspace, you have a couple of options:

- Use `vagrant halt` to power down the Utho via the shutdown mechanism. You can then use vagrant up to power it on again.

- Alternatively, you can use `vagrant destroy` to completely remove the Utho from your account. This action will delete everything created during the Vagrant up process or added later to the server.
{{< /note >}}
