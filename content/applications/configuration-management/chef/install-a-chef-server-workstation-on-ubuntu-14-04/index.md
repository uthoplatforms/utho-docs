---
slug: set-up-chef-server-and-workstation-on-ubuntu-14-04
deprecated: true
description: "Guide to Configuring a Chef Server and Virtual Workstation, and Bootstrapping a Node on Ubuntu 14.04"
keywords: ["chef", "chef installation", "configuration change management", "server automation", "chef server", "chef workstation", "chef-client", "knife.rb", "version control"]
tags: ["ubuntu","automation"]
modified: 2024-05-20
modified_by:
  name: Pawan Kumar
published: 2024-05-20
title: 'Install a Chef Server Workstation on Ubuntu 14.04'
relations:
    platform:
        key: install-chef-workstation
        keywords:
            - distribution: Ubuntu 14.04
authors: ["Pawan Kumar"]
---

Chef is an automation platform that transforms infrastructure management into code, enabling users to deploy and manage resources across multiple servers, or nodes. With Chef, users can create and download recipes (stored in cookbooks) to automate configurations and policies on these nodes.

Chef consists of a Chef server, one or more workstations, and multiple nodes managed by the chef-client installed on each node.

This guide will walk users through creating and configuring a Chef server, a virtual workstation, and bootstrapping a node to run the chef-client, all using individual uthos.

Note: This guide is intended for a non-root user. Commands requiring elevated privileges are prefixed with sudo. If you're not familiar with the sudo command, refer to our Users and Groups guide.

## Prerequisites

- One 4GB utho to host the Chef server, running Ubuntu 14.04
- Two additional uthos (of any size) to host a workstation and a node, each running Ubuntu 14.04
- Follow the Setting Up and Securing a Compute Instance guide to configure each utho
- Ensure each utho has a valid FQDN
- Make sure all servers are up-to-date:

        sudo apt-get update && sudo apt-get upgrade


## The Chef Server

The Chef server acts as the central hub for communication between all workstations and nodes using Chef. Changes made through workstations are uploaded to the Chef server, which the chef-client then accesses to configure each individual node.

### Install the Chef Server

[Download](https://downloads.chef.io/chef-server/#ubuntu) the latest Chef server core (12.0.8 at the time of writing):

        wget https://web-dl.packagecloud.io/chef/stable/packages/ubuntu/trusty/chef-server-core_12.0.8-1_amd64.deb

2.	Install the server:

        sudo dpkg -i chef-server-core_*.deb

3.	Remove the download file:

        rm chef-server-core_*.deb

4.	Run the `chef-server-ctl` command to start the Chef server services:

        sudo chef-server-ctl reconfigure


### Create a User and Organization

1.	In order to link workstations and nodes to the Chef server, an administrator and an organization need to be created with associated RSA private keys. From the home directory, create a `.chef` directory to store the keys:

        mkdir .chef

2.	Create an administrator. Change `username` to your desired username, `firstname` and `lastname` to your first and last name, `email` to your email, `password` to a secure password, and `username.pem` to your username followed by `.pem`:

        sudo chef-server-ctl user-create username firstname lastname email password --filename ~/.chef/username.pem

2.	Create an organization. The `shortname` value should be a basic identifier for your organization with no spaces, whereas the `fullname` can be the full, proper name of the organization. The `association_user`  value `username` refers to the username made in the step above:

        sudo chef-server-ctl org-create shortname fullname --association_user username --filename ~/.chef/shortname.pem

    With the Chef server installed and the needed RSA keys generated, you can move on to configuring your workstation, where all major work will be performed for your Chef's nodes.

## Workstations

Your Chef workstation will be where you create and configure any recipes, cookbooks, attributes, and other changes made to your Chef configurations. Although this can be a local machine of any OS, there is some benefit to keeping a remote server as your workstation since it can be accessed from anywhere.

### Setting Up a Workstation

[Download](https://downloads.chef.io/chef-dk/ubuntu/) the latest Chef Development Kit (0.5.1 at the time of writing):

        wget https://opscode-omnibus-packages.s3.amazonaws.com/ubuntu/12.04/x86_64/chefdk_0.5.1-1_amd64.deb

Install ChefDK:

        sudo dpkg -i chefdk_*.deb

Remove the installation file:

        rm chefdk_*.deb

Verify the components of the development kit:

        chef verify

It should output:

        Running verification for component 'berkshelf'
        Running verification for component 'test-kitchen'
        Running verification for component 'chef-client'
        Running verification for component 'chef-dk'
        Running verification for component 'chefspec'
        Running verification for component 'rubocop'
        Running verification for component 'fauxhai'
        Running verification for component 'knife-spork'
        Running verification for component 'kitchen-vagrant'
        Running verification for component 'package installation'
        ........................
        ---------------------------------------------
        Verification of component 'rubocop' succeeded.
        Verification of component 'kitchen-vagrant' succeeded.
        Verification of component 'fauxhai' succeeded.
        Verification of component 'berkshelf' succeeded.
        Verification of component 'knife-spork' succeeded.
        Verification of component 'test-kitchen' succeeded.
        Verification of component 'chef-dk' succeeded.
        Verification of component 'chef-client' succeeded.
        Verification of component 'chefspec' succeeded.
        Verification of component 'package installation' succeeded.

Generate the chef-repo and move into the newly created directory:

        chef generate repo chef-repo
        cd chef-repo

Create the .chef directory:

         mkdir .chef

### Add the RSA Private Keys

The RSA private keys generated during the Chef server setup need to be placed on the workstation. The process varies depending on whether you use SSH key pair authentication to log into your uthos.

If you are not using key pair authentication, copy the file directly from the Chef Server. Replace user with your username on the server and 123.45.67.88 with the URL or IP of your Chef Server:

            scp user@123.45.67.88:~/.chef/*.pem ~/chef-repo/.chef/

If you are using key pair authentication, from your local terminal, copy the .pem files from your server to your workstation using the scp command. Replace user with the appropriate username, 123.45.67.88 with the URL or IP for your Chef Server, and 987.65.43.22 with the URL or IP for your workstation:

            scp -3 user@123.45.67.88:~/.chef/*.pem user@987.65.43.21:~/chef-repo/.chef/

Confirm that the files have been copied successfully by listing the contents of the .chef directory:

            ls ~/chef-repo/.chef

Your .pem files should be listed.

### Add Version Control

Using Git for version control is beneficial when managing and editing cookbooks and other configuration files.

Download Git:

        sudo apt-get install git

Configure Git by adding your username and email, replacing the needed values:

        git config --global user.name yourname
        git config --global user.email user@email.com

From the chef-repo, initialize the repository:

        git init

Add the .chef directory to the .gitignore file:

        echo ".chef" > .gitignore

Add and commit all existing files:

        git add .
        git commit -m "initial commit"

Ensure the directory is clean:

        git status

It should output:

        nothing to commit, working directory clean

### Generate knife.rb

Create Knife Configuration File: Navigate to your ~/chef-repo/.chef directory and open a file named knife.rb in your preferred text editor to create a knife configuration file.

Copy Configuration: Copy the following configuration into the knife.rb file.

           {{< file "~/chef-repo/.chef/knife.rb" >}}
          log_level                :info
          log_location             STDOUT
          node_name                'username'
          client_key               '~/chef-repo/.chef/username.pem'
          validation_client_name   'shortname-validator'
          validation_key           '~/chef-repo/.chef/shortname.pem'
           chef_server_url          'https://123.45.67.88/organizations/shortname'
           syntax_check_cache_path  '~/chef-repo/.chef/syntax_check_cache'
           cookbook_path [ '~/chef-repo/cookbooks' ]

           {{< /file >}}

Make Changes:

- Update the node_name value with the username created earlier.
- Modify username.pem under client_key to match your user's .pem file.
Set validation_client_name to your organization's shortname followed by -validator.
- Adjust the path for shortname.pem in the validation_key to reflect the shortname defined earlier.
- Ensure the chef_server-url contains the IP address or URL of your Chef server, replacing the shortname in the file path with the defined shortname.

Move and Copy SSL Certificates: Navigate to the chef-repo directory and copy the necessary SSL certificates from the server.

        cd ..
        knife ssl fetch

Confirm Configuration: Ensure the knife.rb file is set up correctly by running the client list command, which should output the validator name.

        knife client list

This command should output the validator name.

Bootstrap a Node: With both the server and workstation configured, you can bootstrap your first node to install the chef-client and validate the node for future configuration changes.

## Bootstrap a Node

Bootstrapping a node installs the chef-client and validates the node, allowing it to read from the Chef server and make any needed configuration changes picked up by the chef-client in the future.

1.	From your *workstation*, bootstrap the node either by using the node's root user, or a user with elevated privileges:

- As the node's root user, changing `password` to your root password and `nodename` to the desired name for your node. You can leave this off it you would like the name to default to your node's hostname:

            knife bootstrap 123.45.67.89 -x root -P password --node-name nodename

For user with sudo privileges: Replace username with the user's username, password with the user's password, and nodename with the desired node name.

            knife bootstrap 123.45.67.89 -x username -P password --sudo --node-name nodename

Confirm Bootstrapping: Verify that the node has been bootstrapped by listing the nodes, which should include the new node.

        knife node list

Your newly added node should appear in the list.

## Download a Cookbook (Optional)

To streamline operations, you can set up periodic chef-client runs and automate the deletion of the validation.pem file by creating a cookbook. Below are instructions for downloading a cookbook to your workstation.

Download Cookbook: From your workstation, download the cookbook and its dependencies.

        knife cookbook site install cron-delvalidate

Open the default.rb file to examine the default cookbook recipe:

In the default.rb file, you'll find the following code:

       {{< file "~/chef-repo/cookbooks/cron-delvalidate/recipies/default.rb" >}}
#

         ### Cookbook Name:: cron-delvalidate
         ### Recipe:: Chef-Client Cron & Delete Validation.pem
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

The cron job named "clientrun" is defined with the resource cron. This job is configured to execute the chef-client action (/usr/bin/chef-client) every hour (*/1, indicating hourly execution). The use of */ specifies that it should run every hour rather than at 1 AM daily. The action parameter signifies that Chef is initiating the creation of a new cron job.

Similarly, the resource file targets the file "/etc/chef/validation.pem". The action specified (:delete) indicates that the file should be removed.

These basic code examples illustrate the Ruby code structure used in Chef cookbooks. You can edit and expand these examples as needed.

Add the recipe to your node's run list, replacing nodename with your node's name:

        knife node run_list add nodename 'recipe[cron-delvalidate::default]'

Push the cookbook to the Chef server:

        knife cookbook upload cron-delvalidate

This command is also used for updating cookbooks.

Switch to your bootstrapped node(s) and run the initial chef-client command:

        chef-client

If running the node as a non-root user, append the above command with sudo.

The recipes in the run list will be pulled from the server and executed. In this instance, it will be the cron-delvalidate recipe. This automated step ensures that any cookbooks made, pushed to the Chef Server, and added to the node's run list will be pulled down to bootstrapped nodes once an hour, eliminating the need to manually connect to the node in the future to pull down changes.
