---
slug: "Mastering Chef: Effortless Ubuntu 20.04 Installation"
description: 'Unlock the power of Chef with this comprehensive guide, offering a concise introduction to the renowned configuration management software. Learn the ins and outs of installation and utilization to streamline your workflow effortlessly'
keywords: ['Install Chef','Configure Chef','Chef Ubuntu','Chef Server','Chef Workstation']
published: 2024-05-20
modified: 2024-05-20
modified_by:
  name: Utho
title: "Install Chef on Ubuntu 20.04"
title_meta: "How to Install Chef on Ubuntu 20.04"
authors: ["Pawan Kumar"]
tags: ["saas"]
---

Chef stands as a robust Infrastructure as Code (IaC) solution, empowering administrators to automate infrastructure provisioning and management. This guide delves into Chef's intricacies, elucidating its setup and configuration on Ubuntu 20.04.

### Unveiling Chef

Chef revolutionizes infrastructure management by automating the provisioning, configuration, and deployment of network nodes, fostering continuous deployment and an automated environment. Its flexible architecture, comprising Chef Workstations, a central Chef Server, and managed nodes, orchestrates seamless operations.

### Key Components:

Chef Workstation: Serves as the development hub, enabling the creation and testing of configuration code before pushing it to the Chef Server. Multiple workstations can interface with a single server, each dedicated to its respective server.

Chef Server: The central hub, housing configuration files, scripts, and code. It orchestrates asset distribution to managed nodes and ensures seamless operation.

Chef Node: Managed by the Chef Server, nodes execute configuration instructions received from the server, ensuring alignment with desired configurations.

Architectural Overview: Chef's hub-and-spoke architecture centralizes configuration management, with the Chef Server at its nucleus, orchestrating interactions between workstations and nodes. Configuration assets seamlessly traverse from workstations to the server, and finally to nodes.

### Chef Components

Chef Jargon: Attribute: Defines values for items on a node.

Bookshelf: Stores cookbooks and assets on the Chef Server, facilitating version control.

Chef-client: Executes on nodes, ensuring alignment with server-configured assets.

Chef-repo: Hosts local cookbooks and configuration files on the Chef Workstation.

Cookbook: Primary node management tool, detailing desired node states through recipes, attributes, libraries, templates, and scripts.
Environment: Organizes nodes into groups, streamlining configuration management.

Knife: Command-line tool facilitating communication between Workstations and the Chef Server.

Recipe: Describes resources to add, change, or run on nodes, written in Ruby.

Resource: Recipe component specifying a type, name, and key-value pairs for configuration.

Test Kitchen: Enables recipe testing pre-deployment.

Explore Utho's Beginner's Guide to Chef and the comprehensive Chef documentation for further insights. Dive into the Learn Chef training resource to master Chef's nuances.

## Before You Begin

If you're new to Utho, kickstart your journey by creating a Utho account and Compute Instance. Refer to our Getting Started with Utho and Creating a Compute Instance guides for seamless setup.

Ensure your Compute Instance is up-to-date and fortified with security measures by following our comprehensive Setting Up and Securing a Compute Instance guide. From updating your system to configuring your hostname and hardening SSH access, we've got you covered.

A robust Chef system requires at least three Utho instances running Ubuntu 20.04. Allocate one for the Chef Workstation, another for the Chef Server, and the third as a managed node. Given Chef Server's memory demands, opt for an 8GB Utho, while 2GB Uthos suffice for the remaining servers. Ensure both Chef Server and Workstation are configured per our previous instructions. Chef will orchestrate setup on the target node.

Keep your Utho servers in prime condition by running system updates with a simple command.

    ```command
    sudo apt update && sudo apt upgrade
    ```

Elevate your Chef Server's identity by assigning it a domain name. Refer to our Utho DNS Manager guide for domain name registration and mapping details.

Harmonize your Chef Server's hostname with its designated domain name to streamline SSL certificate allocation. Execute sudo hostnamectl set-hostname <hostname> on your Ubuntu server, replacing <hostname> with your domain's actual name.

Note: This guide operates under a non-root user context. Commands requiring elevated privileges are prefixed with sudo. If you're unfamiliar with sudo, delve into our Users and Groups guide for a primer.

## How to Install and Configure the Chef Server

As the nucleus of the entire system, it's prudent to start with the installation and configuration of the Chef Server. Given its resource-intensive nature, allocate a dedicated Utho with a minimum of 8GB of memory.

### How to Install the Chef Server

Begin by fetching the Chef Server Core using wget. Below are the steps to procure the latest Chef release tailored for Ubuntu 20.04. For alternative Ubuntu releases, refer to the Chef download page. For a more exhaustive installation guide, consult the Chef Server installation page.

Download the Chef Server core using wget.

    ```command
    wget https://packages.chef.io/files/stable/chef-server/15.1.7/ubuntu/20.04/chef-server-core_15.1.7-1_amd64.deb
    ```

    Install the server core.

    ```command
    sudo dpkg -i chef-server-core_*.deb
    ```

    ```output
    Selecting previously unselected package chef-server-core.
    (Reading database ... 108635 files and directories currently installed.)
    Preparing to unpack chef-server-core_15.1.7-1_amd64.deb ...
    Unpacking chef-server-core (15.1.7-1) ...
    Setting up chef-server-core (15.1.7-1) ...
    Thank you for installing Chef Infra Server!
    ```
    To optimize security and conserve server space, delete the downloaded .deb file.

    ```command
    rm chef-server-core_*.deb
    ```

Initiate the Chef server. When prompted to accept the product licenses, affirm by entering yes.

Note: The installation process may take several minutes. Upon successful installation, expect the message Chef Infra Server Reconfigured!.

    ```command
    sudo chef-server-ctl reconfigure
    ```

### How to Configure a Chef User and Organization

To wield Chef effectively, it's imperative to configure an organization and at least one user on the Chef Server, facilitating server access for workstations and nodes. Follow these steps to create these essential accounts:

Forge a .chef directory within the home directory to house the keys.

    ```command
    mkdir .chef
    ```

Utilize the chef-server-ctl command to craft a user account for the Chef administrator. Additional user accounts can be fashioned subsequently. Replace the placeholders (USER_NAME, FIRST_NAME, LAST_NAME, EMAIL, and PASSWORD) with the pertinent details. For the --filename argument, substitute USER_NAME.pem with the user name designated earlier in the command.

    ```command
    sudo chef-server-ctl user-create USER_NAME FIRST_NAME LAST_NAME EMAIL 'PASSWORD' --filename ~/.chef/USER_NAME.pem
    ```

1. Review the user list and confirm the account now exists.

    ```command
    sudo chef-server-ctl user-list
    ```

    ```output
    USER_NAME
    ```

1. Create a new organization, also using the `chef-server-ctl` command. Replace `ORG_NAME` and `ORG_FULL_NAME` with the actual name of the organization. The `ORG_NAME` field must be all lower case. The value for `USER_NAME` must be the same name used in the `user-create` command. For the `--filename` argument, in `ORG_NAME.pem`, replace `ORG_NAME` with the organization name used elsewhere in the command.

    ```command
    sudo chef-server-ctl org-create ORG_NAME "ORG_FULL_NAME" --association_user USER_NAME --filename ~/.chef/ORG_NAME.pem
    ```

1. List the organizations to confirm the new organization is successfully created.

    ```command
    sudo chef-server-ctl org-list
    ```

    ```output
    ORG_NAME
    ```

## How to Install and Configure a Chef Workstation

A Chef Workstation is where users create and test recipes. Any Utho with at least 2GB of memory is suitable for this task. Unlike the Chef Server, the workstation can perform other tasks. However, in larger organizations with multiple users, centralizing workstation activities on a single server hosting multiple accounts is often more efficient.

### How to Install a Chef Workstation

The steps to install a Chef Workstation are similar to those for installing the Chef Server. Download the appropriate file using wget, then install it. Follow these steps to set up the Chef Workstation:

Download the source files for the Chef Workstation. For different or earlier releases, visit the Chef Workstation Downloads page. For more detailed installation instructions, see the Chef Workstation Installation Documentation.

    ```command
    wget https://packages.chef.io/files/stable/chef-workstation/22.10.1013/ubuntu/20.04/chef-workstation_22.10.1013-1_amd64.deb
    ```

Install the Chef Workstation.

    ```command
    sudo dpkg -i chef-workstation_*.deb
    ```

    ```output
    Thank you for installing Chef Workstation!
    ```

Remove the source file.

    ```command
    rm chef-workstation_*.deb
    ```

Confirm the correct release of the Chef Workstation is installed.

    ```command
    chef -v
    ```

    ```output
    Chef Workstation version: 22.10.1013
    Chef Infra Client version: 17.10.0
    Chef InSpec version: 4.56.20
    Chef CLI version: 5.6.1
    Chef Habitat version: 1.6.521
    Test Kitchen version: 3.3.2
    Cookstyle version: 7.32.1
    ```

### How to Configure a Chef Workstation

Before the workstation is operational, you need to perform a few more tasks, including generating a repository, editing the hosts file, and creating a subdirectory. Follow these steps to fully configure the workstation:

Generate the chef-repo repository. This directory will store Chef cookbooks and recipes. Accept the product licenses when prompted

    ```command
    chef generate repo chef-repo
    ```

    ```output
    Your new Chef Infra repo is ready! Type `cd chef-repo` to enter it.
    ```

Edit the /etc/hosts file. This file contains mappings between host names and their IP addresses. Add an entry for the Chef Server, including the server name (also the domain name) and its IP address. For example, add a line like 192.0.1.0 example.com. Also, ensure there is an entry for the local server, such as 192.0.2.0 chefworkstation, containing the local IP address and the hostname of the server hosting the Chef Workstation. The file should resemble the following example:

    ```file {title="/etc/hosts" lang="conf"}
    127.0.0.1 localhost
    192.0.1.0 example.com
    192.0.2.0 chefworkstation
    ```

Create a .chef subdirectory. This is where the knife configuration file, along with encryption and security files, will be stored.

    ```command
    mkdir ~/chef-repo/.chef
    cd chef-repo
    ```

### How to Add RSA Private Keys

To ensure secure communication between the Chef Server and workstations, use RSA private keys for encryption. These keys were previously created on the Chef Server. Copying them to the workstation will enable encrypted communication. Follow these steps to set up RSA private key encryption:

Note:  SSH password authentication must be enabled on the Chef Server to complete the key exchange. If SSH password authentication was disabled for security purposes, temporarily enable it for this process. After the keys have been retrieved and added to the workstation, you can disable SSH password authentication again. For more information on securing your server, refer to the Utho guide on How to Secure Your Server.

On the workstation, generate an RSA key pair. This key will be used to initially access the Chef Server to copy over the private encryption files.

    ```command
    ssh-keygen -b 4096
    ```

    ```output
    Generating public/private rsa key pair.
    Enter file in which to save the key (/home/username/.ssh/id_rsa):
    ```

Press Enter to accept the default file names id_rsa and id_rsa.pub. Ubuntu stores these files in the /home/username/.ssh directory.


    ```output
    Created directory '/home/username/.ssh'.
    Enter passphrase (empty for no passphrase):
    ```

Enter a password when prompted, then enter it again. An identifier and public key will be saved to the directory.


    ```output
    Your identification has been saved in /home/username/.ssh/id_rsa
    Your public key has been saved in /home/username/.ssh/id_rsa.pub
    ```

Copy the new public key from the workstation to the Chef Server. In the following command, use the account name for the Chef Server along with its IP address:

    ```command
    ssh-copy-id username@192.0.1.0
    ```

Use the scp command to copy the .pem files from the Chef Server to the workstation. Replace username with the user account for the Chef Server and 192.0.1.1 with the actual Chef Server IP address:

    ```command
    scp username@192.0.1.0:~/.chef/*.pem ~/chef-repo/.chef/
    ```

    ```output
    Enter passphrase for key '/home/username/.ssh/id_rsa':
    username.pem                                                                       100% 1674     1.7MB/s   00:00
    testcompany.pem                                                                 100% 1678     4.7MB/s   00:00
    ```

List the contents of the .chef subdirectory to ensure the .pem files were successfully copied:

    ```command
    ls ~/chef-repo/.chef
    ```

    ```output
    username.pem  testcompany.pem
    ```

### How to Configure Git on a Chef Workstation

A version control system helps the Chef Workstation track changes to cookbooks and restore earlier versions if necessary. This example uses Git, which is compatible with the Chef system. Follow these steps to configure Git, initialize a Git repository, add new files, and commit them:

Configure Git using the git config command. Replace username and user@email.com with your own values:

    ```command
    git config --global user.name username
    git config --global user.email user@email.com
    ```

Add the .chef directory to the .gitignore file. This ensures system and auto-generated files are not shown in the output of git status and other Git commands:

    ```command
    echo ".chef" > ~/chef-repo/.gitignore
    ```

Ensure the chef-repo directory is the current working directory. Add and commit the existing files using git add and git commit:

    ```command
    cd ~/chef-repo
    git add .
    git commit -m "initial commit"
    ```

    ```output
    [master (root-commit) a3208a3] initial commit
    13 files changed, 343 insertions(+)
    create mode 100644 .chef-repo.txt
    ...
    create mode 100644 policyfiles/README.md
    ```

Run the git status command to ensure all files have been committed:

    ```command
    git status
    ```

    ```output
    On branch master
    nothing to commit, working tree clean
    ```

## How to Generate a Chef Cookbook

To generate a new Chef cookbook, use the chef generate command:

```command
chef generate cookbook my_cookbook
```

## How to Configure the Knife Utility

The Chef Knife utility helps a Chef workstation communicate with the server, allowing for the management of cookbooks, nodes, and the Chef environment. Chef uses the config.rb file in the .chef subdirectory to store the Knife configuration. To configure Knife, follow these steps:

Create a config.rb file in the ~/chef-repo/.chef directory. This example uses vi, but any text editor can be used:


    ```command
    cd ~/chef-repo/.chef
    vi config.rb
    ```

Use the following config.rb file as an example of how to configure Knife. Copy this sample configuration to the file:

    ```file {title="~/chef-repo/.chef/config.rb" lang="ruby"}
    current_dir = File.dirname(__FILE__)
    log_level                :info
    log_location             STDOUT
    node_name                'node_name'
    client_key               "USER.pem"
    validation_client_name   'ORG_NAME-validator'
    validation_key           "ORG_NAME-validator.pem"
    chef_server_url          'https://example.com/organizations/ORG_NAME'
    cache_type               'BasicFile'
    cache_options( :path => "#{ENV['HOME']}/.chef/checksums" )
    cookbook_path            ["#{current_dir}/../cookbooks"]
    ```

Make the following changes:

Replace node_name with the name of the user account created when configuring the Chef Server.

For client_key, replace USER with the user name associated with the .pem file, followed by .pem.

Set validation_client_name to the same ORG_NAME used when creating the organization, followed by -validator.

In the validation_key field, use the ORG_NAME used when the organization was created, followed by -validator.pem.

For chef_server_url, replace example.com with the domain name, followed by /organizations/ and the ORG_NAME used when creating the organization.

Leave the remaining fields unchanged.

1. Move back to the `chef-repo` directory and fetch the necessary SSL certificates from the server using the `knife fetch` command.

Note: The SSL certificates were generated when the Chef server was installed. The certificates are self-signed. This means a certificate authority has not verified them. Before fetching the certificates, log in to the Chef server and ensure the hostname and fully qualified domain name (FQDN) are the same. These values can be confirmed using the commands `hostname` and `hostname -f`.

    ```command
    cd ..
    knife ssl fetch
    ```

    ```output
    Knife has no means to verify these are the correct certificates. You should verify the authenticity of these certificates after downloading.
    Adding certificate for example.com in /home/username/chef-repo/.chef/trusted_certs/example.com.crt
    ```

1. To confirm the `config.rb` file is correct, run the `knife client list` command. The relevant validator name should be displayed.

    ```command
    knife client list
    ```

    ```output
    testcompany-validator
    ```

## How to Bootstrap a Node

At this point, both the Chef Server and Chef Workstation are configured. They can now be used to bootstrap the node. The bootstrap process installs the Chef client on the node and performs validation. The node can then retrieve any necessary updates from the Chef Server. To bootstrap the node, follow these steps:

Log in to the target node (the node to be bootstrapped) and edit the /etc/hosts file. Add entries for the node, the Chef server domain name, and the workstation. The file should resemble the following example, using the actual names of the Chef Server, workstation, and the target node, along with their IP addresses.

    ```file {title="/etc/hosts" lang="conf"}
    127.0.0.1 localhost
    192.0.100.0 targetnode
    192.0.2.0 chefworkstation
    192.0.1.0 example.com
    ```

Return to the Utho hosting the Chef Workstation and change the working directory to ~/chef-repo/.chef.

    ```command
    cd ~/chef-repo/.chef
    ```

Bootstrap the node using the knife bootstrap command. Specify the IP address of the target node for node_ip_address. Use the actual user name and password for the account in place of username and password, and enter the name of the node in place of nodename. Answer Y when asked "Are you sure you want to continue connecting".

    Note: The option to bootstrap using key-pair authentication is no longer supported.


    ```command
    knife bootstrap node_ip_address -U username -P password --sudo --use-sudo-password --node-name nodename
    ```

Confirm the node has been successfully bootstrapped. List the client nodes using the knife client list command. All bootstrapped nodes should be listed.

    ```command
    knife client list
    ```

    ```output
    target-node
    testcompany-validator
    ```

Add the bootstrapped node to the workstation /etc/hosts file. Replace 192.0.100.1 targetnode with the IP address and name of the bootstrapped node.

    ```file {title="/etc/hosts" lang="conf"}
    127.0.0.1 localhost
    192.0.1.0 example.com
    192.0.2.0 chefworkstation
    192.0.100.0 targetnode
    ```

## How to Download and Apply a Cookbook (Optional)

A cookbook is the most efficient way of keeping target nodes up to date. Additionally, a cookbook can delete the validation.pem file that was created on the node when it was bootstrapped. Deleting this file is important for security reasons.

It is not mandatory to download or create cookbooks to use Chef, but this section provides a brief example of how to download a cookbook and apply it to a node.

On the Chef workstation, change to the ~/chef-repo/.chef directory.

    ```command
    cd ~/chef-repo/.chef
    ```

    ```command
    knife supermarket download cron-delvalidate
    ```

    ```output
    Downloading cron-delvalidate from Supermarket at version 0.1.3 to /home/username/chef-repo/.chef/cron-delvalidate-0.1.3.tar.gz
    Cookbook saved: /home/username/chef-repo/.chef/cron-delvalidate-0.1.3.tar.gz
    ```

If the cookbook is downloaded as a .tar.gz file, use the tar command to extract it. Move the extracted directory to the cookbooks directory.


    ```command
    tar -xf cron-delvalidate-0.1.3.tar.gz
    cp -r  cron-delvalidate ~/chef-repo/cookbooks/
    ```

Review the cookbook's default.rb file to see the recipe. This recipe is written in Ruby and demonstrates how a typical recipe is structured. It contains a cron job named clientrun. This job creates a new cron job to run the chef-client command on an hourly basis. It also removes the extraneous validation.pem file.


    ```file {title="~/chef-repo/cookbooks/cron-delvalidate/recipes/default.rb" lang="ruby"}
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
    ```

Add the recipe to the run list for the node. In the following command, replace nodename with the name of the node:

    ```command
    knife node run_list add nodename 'recipe[cron-delvalidate::default]'
    ```

    ```output
    nodename:
      run_list: recipe[cron-delvalidate::default]
    ```

Upload the cookbook and its recipes to the Chef Server:

    ```command
    knife cookbook upload cron-delvalidate
    ```

    ```output
    Uploading cron-delvalidate [0.1.3]
    Uploaded 1 cookbook.
    ```

Run the chef-client command on the node using the knife ssh utility. This command causes the node to pull the recipes in its run list from the server and check for updates. The Chef Server transmits the recipes to the target node. When the recipe runs, it deletes the validation.pem file and installs a cron job to keep the node up to date in the future. In the following command, replace nodename with the actual name of the target node and username with the name of a user account with sudo access. Enter the password for the account when prompted:
    
    ```command
    knife ssh 'name:nodename' 'sudo chef-client' -x username
    ```

    ```output
    nodename Chef Infra Client, version 17.10.3
    nodename Patents: https://www.chef.io/patents
    nodename Infra Phase starting
    nodename Resolving cookbooks for run list: ["cron-delvalidate::default"]
    nodename Synchronizing cookbooks:
    nodename   - cron-delvalidate (0.1.3)
    nodename Installing cookbook gem dependencies:
    nodename Compiling cookbooks...
    nodename Loading Chef InSpec profile files:
    nodename Loading Chef InSpec input files:
    nodename Loading Chef InSpec waiver files:
    nodename Converging 2 resources
    nodename Recipe: cron-delvalidate::default
    nodename   * cron[clientrun] action create
    nodename     - add crontab entry for cron[clientrun]
    nodename   * file[/etc/chef/validation.pem] action delete (up to date)
    nodename
    nodename Running handlers:
    nodename Running handlers complete
    nodename Infra Phase complete, 1/2 resources updated in 03 seconds
    ```

## Conclusion

Chef is an infrastructure as code (IaC) application designed for automating the deployment and management of infrastructure nodes. The Chef architecture consists of the Chef Server, which stores all the configurations and procedures, and a Chef Workstation, where the infrastructure code is developed. Managed nodes communicate with the server to receive updates.

To use Chef, follow these steps:

Install the Chef Server and Chef Workstation software.
Share RSA keys between the server and workstation for secure communication.

Install version control and the Chef Knife utility on the workstation.
Bootstrap the target nodes using the knife bootstrap utility.
Once a node is bootstrapped, you can download and apply cookbooks and recipes using the node's run list.
