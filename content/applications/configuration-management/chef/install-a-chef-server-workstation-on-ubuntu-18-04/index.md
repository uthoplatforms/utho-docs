---
slug: Whip Up Culinary Magic: Set Up Your Chef Server Workstation on Ubuntu 18.04
description: "Unlock the Chef's Kitchen: Learn to Set Up a Chef Server, Virtual Workstation, and Bootstrap Client Node on Ubuntu 18.04!"
keywords: ["chef", "configuration management", "server automation", "chef server", "chef workstation", "chef-client"]
tags: ["ubuntu","automation"]
published: 2024-05-20
modified: 2024-05-20
modified_by:
  name: Utho
title: 'Installing a Chef Server Workstation on Ubuntu 18.04'
title_meta: 'How To Install a Chef Server Workstation on Ubuntu 18.04'
relations:
    platform:
        key: install-chef-workstation
        keywords:
            - distribution: Ubuntu 18.04
authors: ["Utho"]
---

Chef serves as a Ruby-based configuration management tool, empowering users to define infrastructure through code. With Chef, automating management across multiple nodes becomes seamless, ensuring uniformity throughout. Recipes crafted on a user's workstation via the Chef Workstation package establish the desired state for managed nodes. These recipes are then disseminated via a Chef server to the respective nodes, where the Chef client executes them.

This comprehensive guide walks you through setting up and configuring a Chef server and workstation while also bootstrapping a node for Chef management. The process will require three separate Uthos.

See [A Beginner's Guide to Chef](/docs/guides/beginners-guide-chef/) for an introduction to Chef concepts.

Please note: This guide is tailored for non-root users. Commands necessitating elevated privileges are preceded by sudo. If you're unfamiliar with the sudo command, refer to our Users and Groups guide for clarification.

## Prerequisites

One 8GB Utho running Ubuntu 18.04 will serve as the Chef server.

Assign a Domain to the Chef server, ensuring proper configuration of domain zone, NS record, and A/AAA record. Check out Create a Domain for detailed instructions.

Make sure the Chef server's hostname matches its Domain name to facilitate automatic SSL certificate creation based on the Utho's hostname.
Two 2 GB Uthos, both running Ubuntu 18.04. One Utho will host a workstation, and the other will serve as a node managed by Chef.

Configure the workstation and Chef server following the guidelines in Setting Up and Securing a Compute Instance guide. Once the node is bootstrapped, leverage a Chef cookbook to secure it. Consider employing the Users cookbook and the Firewall cookbook for this task.

Ensure all servers are kept up-to-date.

             sudo apt update && sudo apt upgrade

## The Chef Server

The Chef server acts as the central hub facilitating communication among all workstations and nodes managed by Chef. Any alterations made to configuration code on workstations are first transmitted to the Chef server. Subsequently, a node's chef-client pulls these configurations from the server and applies them accordingly.

### Install the Chef Server

Download the [latest Chef server core](https://downloads.chef.io/chef-server/#ubuntu).

        wget https://packages.chef.io/files/stable/chef-server/13.1.13/ubuntu/18.04/chef-server-core_13.1.13-1_amd64.deb

Install the server:

        sudo dpkg -i chef-server-core_*.deb

Remove the downloaded file once the installation is complete.

        rm chef-server-core_*.deb

The Chef server incorporates a command-line utility named chef-server-ctl. Initiate the Chef server services by running chef-server-ctl.

        sudo chef-server-ctl reconfigure

### Create a Chef User and Organization

To establish a connection between workstations, nodes, and the Chef server, it's essential to create an administrator and organization alongside their corresponding RSA private keys.

Begin by creating a .chef directory within the home directory to store these keys.

        mkdir .chef

Utilize chef-server-ctl to generate a user. Customize the placeholders USER_NAME, FIRST_NAME, LAST_NAME, EMAIL, and PASSWORD as per your requirements. Remember to adjust the filename USER_NAME.pem, ensuring to retain the .pem extension.

        sudo chef-server-ctl user-create USER_NAME FIRST_NAME LAST_NAME EMAIL 'PASSWORD' --filename ~/.chef/USER_NAME.pem

To list all users on your Chef server, execute the following command:

        sudo chef-server-ctl user-list

Next, establish an organization and assign the previously created user to both the admins and billing admins security groups. Replace ORG_NAME with a brief identifier for the organization, ORG_FULL_NAME with its complete name, USER_NAME with the username generated earlier, and ORG_NAME.pem with the organization's identifier followed by .pem.

        sudo chef-server-ctl org-create ORG_NAME "ORG_FULL_NAME" --association_user USER_NAME --filename ~/.chef/ORG_NAME.pem

Note: Ensure ORG_NAME is entirely in lowercase.

To view all organizations on your Chef server, use the following command:

        sudo chef-server-ctl org-list

With the Chef server set up and the RSA keys in place, you're ready to configure your workstation. The workstation serves as the primary location for creating configurations for your nodes.

## Chef Workstations

The Chef workstation is where you design and set up recipes, cookbooks, attributes, and other essential changes required to manage your nodes. While it's possible to use a local machine running any operating system, having a remote server as your workstation offers the advantage of accessibility from anywhere.

In this section, you'll download and install the Chef Workstation package, providing all the necessary tools included in the ChefDK, Chef's development kit.

### Setting Up a Workstation

[Download the latest Chef Workstation](https://downloads.chef.io/chef-workstation/0.2.43#ubuntu) here.

        wget  https://packages.chef.io/files/stable/chef-workstation/0.2.43/ubuntu/18.04/chef-workstation_0.2.43-1_amd64.deb

Install Chef Workstation.

        sudo dpkg -i chef-workstation_*.deb

Remove the installation file once installation is complete.

        rm chef-workstation_*.deb

Create your Chef repository. The chef-repo directory serves as the storage space for your Chef cookbooks and related files.

        chef generate repo chef-repo

Ensure that your workstation's /etc/hosts file maps its IP address to your Chef server's fully qualified domain name and workstation hostnames. For example:

              {{< file "/etc/hosts">}}
              127.0.0.2 localhost
              192.0.1.1 example.com
              192.0.2.1 workstation
               ...
               {{</ file >}}

Establish a .chef subdirectory within the chef-repo directory. This subdirectory will house your Knife configuration file and .pem files used for RSA key pair authentication with the Chef server.

               mkdir ~/chef-repo/.chef
               cd chef-repo

### Add the RSA Private Keys

Authentication between the Chef server, workstation, and/or nodes relies on public key encryption. In this section, the RSA private keys, generated during the Chef server setup, will be copied to the workstation to facilitate communication between them.

If you haven't already, generate an RSA key pair on your workstation. This pair will grant access to the Chef server and allow for the transfer of .pem files.

              ssh-keygen -b 4096

Press Enter to accept the default names id_rsa and id_rsa.pub in /home/your_username/.ssh, then enter your passphrase.

Note: If SSH password authentication is disabled on your Chef server's Utho, as recommended in the How to Secure Your Server guide, temporarily re-enable it before proceeding with these steps. Remember to disable it again once you've added your workstation's public SSH key to the Chef server's Utho.

Upload your workstation's public key to the Utho hosting the Chef server. Replace example_user with the Chef server's user account and 192.0.2.1 with its IP address.

        ssh-copy-id example_user@192.0.2.0

Copy the .pem files from your Chef server to your workstation using the scp command. Replace user with the appropriate username and 192.0.2.1 with your Chef server's IP.

        scp example_user@192.0.2.0:~/.chef/*.pem ~/chef-repo/.chef/

Verify successful file transfer by listing the contents of the .chef directory.

        ls ~/chef-repo/.chef

Your .pem files should be displayed.

### Add Version Control

The workstation serves as the platform for creating, downloading, and editing cookbooks and related files. It's crucial to track any changes made to these files using version control software like Git. The Chef Workstation incorporates Git and initializes a Git repository in the directory where the chef-repo was generated. Configure Git by adding your username and email, then add and commit any new files created in the preceding steps.

Configure Git by providing your username and email, replacing the necessary values:

        git config --global user.name yourname
        git config --global user.email user@email.com

Include the .chef directory in the .gitignore file:

        echo ".chef" > ~/chef-repo/.gitignore

Navigate to the ~/chef-repo directory, if not already there, and add and commit all existing files:

        cd ~/chef-repo
        git add .
        git commit -m "initial commit"

Ensure the directory is clean:

         git status

The output should confirm:

                     {{< output >}}
                     On branch master
                     nothing to commit, working directory clean
                     {{</ output >}}

## Generate your First Cookbook

Generate a new Chef cookbook:

             chef generate cookbook my_cookbook

### Configure Knife

Create a knife configuration file by accessing your ~/chef-repo/.chef directory and creating a file named config.rb using your preferred text editor.

Insert the following configuration into the config.rb file:

              {{< file "~/chef-repo/.chef/config.rb" ruby >}}
                 current_dir = File.dirname(__FILE__)
                 log_level                :info
                 log_location             STDOUT
                 node_name                'node_name'
                 client_key               "USER.pem"
                 validation_client_name   'ORG_NAME-validator'
                 validation_key           "ORGANIZATION-validator.pem"
                 chef_server_url          'https://example.com/organizations/ORG_NAME'
                 cache_type               'BasicFile'
                 cache_options( :path => "#{ENV['HOME']}/.chef/checksums" )
                 cookbook_path            ["#{current_dir}/../cookbooks"]
                 {{< /file >}}

Customize the following:

- Replace the value for node_name with the username created on the Chef server.
- Update USER.pem under client_key to match your user's .pem file.
- Adjust validation_client_name to reflect your organization's ORG_NAME followed by -validator.
- Set ORGANIZATION-validator.pem in the validation_key path to ORG_NAME followed by -validator.pem.
- Lastly, update chef_server_url to the Chef server's domain appended with /organizations/ORG_NAME. Replace ORG_NAME with your organization's name.
- Navigate to the chef-repo directory and copy the necessary SSL certificates from the server:

        cd ..
        knife ssl fetch

Note: SSL certificates are automatically generated during the installation of the Chef server. As these certificates are self-signed, there isn't a signing certificate authority (CA) for verification. It's crucial that the Chef server's hostname and Fully Qualified Domain Name (FQDN) match, ensuring the workstation can fetch and validate the SSL certificates. You can confirm the Chef server's hostname and FQDN by executing hostname and hostname -f commands respectively. Refer to the Chef documentation for details on regenerating SSL certificates.

Validate the correct setup of config.rb by executing the client list command:

        knife client list

This command should display the validator name.

Now that your Chef server and workstation are configured, you can proceed to bootstrap your first node.

## Bootstrap a Node

Bootstrapping a node involves installing the Chef client on the node and verifying its identity. This enables the node to retrieve and apply any necessary configuration updates detected by the chef-client.

Note: In case of encountering any 401 Unauthorized errors, ensure that your ORGANIZATION.pem file has 700 permissions. Refer to Chef's troubleshooting guide for further assistance in diagnosing authentication errors.

Update the /etc/hosts file on the node to specify the node, Chef server's domain name, and the workstation.

            {{< file "/etc/hosts">}}
            127.0.0.2 localhost
            198.51.100.1 node-hostname
            192.0.2.1 workstation
            192.0.1.1 example.com
             ...
             {{</ file >}}

Navigate to your ~/chef-repo/.chef directory from your workstation.

        cd ~/chef-repo/.chef

Bootstrap the client node either using the node's root user or a user with elevated privileges:

- For the node's root user: Replace password with your root password and nodename with the desired name for your client node. You can omit this if you prefer the name to default to your node's hostname.

            knife bootstrap 192.0.2.0 -x root -P password --node-name nodename

- For a user with sudo privileges: Replace username with a node user, password with the user's password, and nodename with the desired name for the client node. You can omit this if you prefer the name to default to your node's hostname.

            knife bootstrap 192.0.2.0 -x username -P password --use-sudo-password --node-name nodename

- For a user with key-pair authentication: Replace username with a node user and nodename with the desired name for the client node. You can omit this if you prefer the name to default to your client node's hostname.

            knife bootstrap 192.0.2.0 --ssh-user username --sudo --identity-file ~/.ssh/id_rsa.pub --node-name hostname

Confirm the successful bootstrapping of the node by listing the clients:

        knife client list

Your new client node should be listed.

    Add the bootstrapped node to your workstation's /etc/hosts file. Replace node-hostname with the hostname you assigned to the node during bootstrap.

              {{< file "/etc/hosts">}}
               127.0.0.2 localhost
               192.0.1.1 example.com
               192.0.2.1 workstation
               198.51.100.1 node-hostname
               ...
               {{</ file >}}

## Download a Cookbook (Optional)

When employing Chef, the Chef client should periodically run on your nodes to fetch any changes pushed to the Chef server from your workstation. Additionally, it's advisable to delete the validation.pem file uploaded to your node upon bootstrap for security reasons. While these tasks can be executed manually, setting them up as a cookbook often proves easier and more efficient.

This section, though optional, offers guidance on downloading a cookbook to your workstation, pushing it to a server, and provides a basic cookbook skeleton for further customization and experimentation.

Navigate to your ~/chef-repo/.chef directory from your workstation.

        cd ~/chef-repo/.chef

Download the cookbook and its dependencies.

        knife cookbook site install cron-delvalidate

Open the default.rb file to examine the default cookbook recipe.

                 {{< file "~/chef-repo/cookbooks/cron-delvalidate/recipes/default.rb" ruby >}}
#
# Cookbook Name:: cron-delvalidate
# Recipe:: Chef-Client Cron & Delete Validation.pem
#
#

                cron "clientrun" do
                minute '0'
                hour '*/1'
                command "/usr/bin/chef-client"
                action :create
              end

                file "/etc/chef/validation.pem" do
                action :delete
             end
                {{< /file >}}

The cron "clientrun" resource defines a cron action, scheduling the execution of the chef-client action (/usr/bin/chef-client) every hour (*/1). The */ prefix indicates it's set to run every hour, not just at 1 AM daily. The action code signifies that Chef is creating a new cronjob.

The file "/etc/chef/validation.pem" resource refers to the validation.pem file. The action specifies that the file should be removed (:delete).

These are basic Ruby code snippets providing examples of the code structure used in Chef cookbooks. They can be customized and expanded as needed.

Add the recipe to your node's run list, replacing nodename with your node's name:

              knife node run_list add nodename 'recipe[cron-delvalidate::default]'

Push the cookbook to the Chef server:

               knife cookbook upload cron-delvalidate

This command is also used when updating cookbooks.

Use knife-ssh to execute the chef-client command on your node. Replace nodename with your node's name. If your node is configured with a limited user account, replace -x root with the correct username, e.g., -x username.

              knife ssh 'name:nodename' 'sudo chef-client' -x root

The recipes in the run list will be retrieved from the server and executed on the node. In this scenario, it will be the cron-delvalidate recipe. This recipe ensures that any cookbooks pushed to the Chef Server and added to the node's run list will be fetched by bootstrapped nodes every hour. This automated process eliminates the need to manually connect to the node in the future to retrieve changes.
