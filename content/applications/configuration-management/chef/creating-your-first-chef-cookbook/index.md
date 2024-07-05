---
slug: "Whipping Up Your Debut Chef Cookbook: A Beginner's Culinary Adventure"
description: 'Discover the step-by-step guide to crafting Chef cookbooks, empowering you to automate tasks effortlessly. Dive into the world of Chef as you construct a robust LAMP stack with ease, ensuring seamless deployment and automated updates'
keywords: ["chef", "automation", "cookbooks", "configuration management", "DevOps"]
modified: 2024-05-20
modified_by:
  name: Utho
published: 2024-05-20
title: Creating your First Chef Cookbook
title_meta: How to Create your First Chef Cookbook
tags: ["automation"]
authors: ["Pawan Kumar"]
---

Learn how to harness the power of Chef cookbooks to define and enforce the desired state of your nodes effortlessly. Dive into the creation of a cookbook designed to configure a robust LAMP stack on your utho server.

## Before You Begin

Start by setting up Chef with our comprehensive guide, Setting Up a Chef Server, Workstation, and Node. Ensure you select Ubuntu 16.04 as the Linux image for your Chef node. Note that this guide utilizes the MySQL Chef cookbook, optimized for Ubuntu 16.04.

Once your node is bootstrapped, enhance its security using Chef cookbooks. Explore options like the Users and Firewall cookbooks for added protection. While not mandatory, these steps are recommended.

If you're new to Chef, take a moment to peruse A Beginner's Guide to Chef for a comprehensive overview of Chef concepts.

Please note that the examples in this tutorial assume the use of a user account with sudo privileges. If you're operating with a limited user account, remember to prefix commands with sudo when interacting with the Chef client node. Additionally, replace -x root with -x username, where username denotes your limited user account.

Ensure your workstation's /etc/hosts file contains pertinent IP addresses and fully qualified domain names for seamless interaction with your Chef server and nodes.

          {{< file "/etc/hosts">}}
          127.0.0.2       localhost
          192.0.2.1       workstation
          192.0.1.1       www.example.com
          198.51.100.1    node-hostname
          {{</ file >}}

## Create the Cookbook

Navigate to your chef-repo/cookbooks directory from your workstation.

         cd chef-repo/cookbooks

Create a new cookbook titled lamp_stack.

         chef generate cookbook lamp_stack

Move to the newly created directory for your cookbook.

         cd lamp_stack

Upon issuing the ls command, expect to see the following files and directories:

         {{< output >}}
         Berksfile  CHANGELOG.md  chefignore  LICENSE  metadata.rb  README.md  recipes  spec  test
         {{</ output >}}

### default.rb

Attributes serve as crucial pieces of data aiding the chef-client in determining the current state of a node and any changes between chef-client runs. These attributes are sourced from the node's state, cookbooks, roles, and environments, and are compiled into a list applied to the node during each chef-client run. If a default.rb file exists within a cookbook, it holds the lowest attribute precedence.

The default.rb file located in the recipes directory contains the default recipe resources.

In our example, the default.rb file within the lamp_stack cookbook is utilized to update the distribution software on the node.

Navigate to the recipes folder from within the lamp_stack directory.
        cd recipes

Open the default.rb file and insert the following code:

          {{< file "~/chef-repo/cookbooks/lamp_stack/recipe/default.rb" ruby >}}
#
# Cookbook Name:: lamp_stack
# Recipe:: default
#
#

     execute "update-upgrade" do
     command "sudo apt-get update && sudo DEBIAN_FRONTEND=noninteractive apt-get -y -o Dpkg::Options::='--force-confdef' -o Dpkg::Options::='--force-confold' upgrade"
     action :run
     end

     {{< /file >}}

Recipes consist of a sequence of resources. In this instance, the execute resource is utilized, triggering the execution of a command once. The commands apt-get update && apt-get upgrade -y are specified within the command section, while the action is designated as :run to execute the commands.

Additional variables and flags appended to the upgrade command are intended to suppress the GRUB configuration menu, preventing Chef from stalling due to user input.

This represents a straightforward Chef recipe, ideal for beginners. You can incorporate any other essential startup procedures by following a similar code structure.

Incorporate the LAMP stack cookbook into the Chef server to initiate testing.

        knife cookbook upload lamp_stack

Ensure the successful addition of the recipe to the Chef server by verification.

        knife cookbook list

You should observe similar output upon completion.

            {{< output >}}
            Uploading lamp_stack   [0.1.0]
            Uploaded 1 cookbook.
            {{</ output >}}

Integrate the recipe into your designated node's run list, replacing nodename with the appropriate node identifier.

        knife node run_list add nodename "recipe[lamp_stack]"

Execute the configurations outlined in the cookbook by initiating the chef-client on your node from your workstation. Remember to substitute nodename with your node's name.

        knife ssh 'name:nodename' 'sudo chef-client' -x root

A successful Chef run should be indicated in the output. In case of any issues, scrutinize your code for errors, usually highlighted in the chef-client run output.

## Apache

### Install and Enable

Create a new file named apache.rb under the ~/chef-repo/cookbooks/lamp_stack/recipes directory on your Chef workstation. This file will contain your Apache configuration details.

Open the apache.rb file and define the package resource to facilitate Apache installation.

         {{< file "~/chef-repo/cookbooks/lamp_stack/apache.rb" ruby >}}
           package "apache2" do
           action :install
           end

           {{< /file >}}

This is a fundamental recipe where the package resource specifies the installation of the apache2 package. Ensure the package name is accurate. The action is set to install to execute the installation without additional parameters.

Configure Apache to enable and start at system boot by appending the following lines of code to the same file:

          {{< file "~/chef-repo/cookbooks/lamp_stack/apache.rb" ruby >}}
           service "apache2" do
           action [:enable, :start]
           end

           {{< /file >}}

Utilizing the service resource, the Apache service is manipulated. The enable action ensures its activation during system startup, while start initiates the Apache service.

          Save and close the `apache.rb` file.

Validate the functionality of the Apache recipe by updating the LAMP Stack recipe on the server.

           knife cookbook upload lamp_stack

Include the recipe in a node's run-list, replacing nodename with the appropriate node's name.

        knife node run_list add nodename "recipe[lamp_stack::apache]"

As this isn't the default.rb recipe, the recipe name, apache, must be appended to the recipe value.

Note: To access a list of all nodes managed by your Chef server, utilize the following command from your workstation:

                knife node list

Execute the defined configurations from your workstation by initiating the chef-client on your node. Remember to substitute nodename with the appropriate node identifier:

        knife ssh 'name:nodename' 'sudo chef-client' -x root

If a syntax error occurs, Chef will highlight it in the output.

Following a successful chef-client run, confirm the status of Apache to ensure it is running:

         knife ssh 'name:nodename' 'systemctl status apache2' -x root

It's advisable to repeat steps 4-7 for each recipe upload to your Chef server as you develop them. Periodically run chef-client on your node throughout this guide to verify the proper functioning of your recipes and to rectify any errors. When adding a new recipe, ensure its correct name is used in the run list.

This workflow isn't recommended for production environments. Consider establishing distinct Chef environments for testing, staging, and production purposes.

### Configure Virtual Hosts

As multiple websites may necessitate configuration, leverage Chef's attributes functionality to define specific aspects of the virtual host file(s). The ChefDK offers a built-in command to generate the attributes directory and default.rb file within a cookbook. Replace ~/chef-repo/cookbooks/lamp_stack with your cookbook's path:

        chef generate attribute ~/chef-repo/cookbooks/lamp_stack default

Establish default values within the new default.rb file for the cookbook:


        {{< file "~/chef-repo/cookbooks/lamp_stack/attributes/default.rb" ruby >}}
        default["lamp_stack"]["sites"]["example.com"] = { "port" => 80, "servername" => "example.com", "serveradmin" => "webmaster@example.com" }
         {{< /file >}}

The default prefix indicates these as the standard values for the lamp_stack, with the site example.com designated. Think of this as a hierarchical structure: within the cookbook, sites are defined by their URLs.

The values enclosed in curly brackets ({}) represent the configuration parameters for the virtual host file. Apache will be configured to listen on port 80 and utilize the specified values for its server name and administrator email.

For additional websites or URLs (e.g., example.org), replicate this syntax accordingly.

       {{< file "~/chef-repo/cookbooks/lamp_stack/attributes/default.rb" ruby >}}
       default["lamp_stack"]["sites"]["example.com"] = { "port" => 80, "servername" => "example.com", "serveradmin" => "webmaster@example.com" }
        default["lamp_stack"]["sites"]["example.org"] = { "port" => 80, "servername" => "example.org", "serveradmin" => "webmaster@example.org" }
        {{< /file >}}

Revisit your apache.rb file within the recipes directory to incorporate the attributes previously defined. Utilize the node resource for this purpose:
          {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}

#Install & enable Apache

           package "apache2" do
           action :install
            end

           service "apache2" do
           action [:enable, :start]
           end

# Virtual Host Files

            node["lamp_stack"]["sites"].each do |sitename, data|
            end
            {{< /file >}}

This action retrieves the values stored under ["lamp_stack"]["sites"]. Each value, identified by the sitename, will generate corresponding code within this block. The data value retrieves the attributes listed in the array for each sitename.

Inside the node resource, specify a document root. This root will serve as the location for public HTML files and any associated log files:

           {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}
           node["lamp_stack"]["sites"].each do |sitename, data|
           document_root = "/var/www/html/#{sitename}"
           end
           {{< /file >}}

Establish the document_root directory. Utilize a directory resource with a recursive value of true to ensure the creation of all directories leading up to the sitename. Permissions are set to 0755, granting the file owner full access while providing read and execute privileges to group and regular users:

        {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}
         node["lamp_stack"]["sites"].each do |sitename, data|
         document_root = "/var/www/html/#{sitename}"

          directory document_root do
          mode "0755"
          recursive true
          end

          end
          {{< /file >}}

Utilize the template feature to generate necessary virtual host files. From the chef-repo directory, execute the chef generate template command, specifying the path to your cookbook and the template file name:

        chef generate template ~/chef-repo/cookbooks/lamp_stack virtualhosts

Open and modify the virtualhosts.erb file. Instead of hardcoding the true values for each VirtualHost parameter, incorporate Ruby variables. These variables are denoted by the <%= @variable_name %> syntax. Ensure that the variable names used here were defined in the recipe file:

               {{< file "~/chef-repo/cookbooks/lamp_stack/templates/virtualhosts.erb" ruby >}}
               <VirtualHost *:<%= @port %>>
                ServerAdmin <%= @serveradmin %>
                ServerName <%= @servername %>
                ServerAlias www.<%= @servername %>
                DocumentRoot <%= @document_root %>/public_html
                ErrorLog <%= @document_root %>/logs/error.log
                <Directory <%= @document_root %>/public_html>
                Require all granted
                </Directory>
                </VirtualHost>

                {{< /file >}}

Some variables may appear familiar, as they were introduced in Step 2 when defining default attributes.

Revisit the apache.rb recipe. Following the directory resource, employ the template resource to reference the recently created template file:

        {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}
          # [...]

#Virtual Host Files

            node["lamp_stack"]["sites"].each do |sitename, data|
             document_root = "/var/www/html/#{sitename}"

             directory document_root do
             mode "0755"
             recursive true
             end

           template "/etc/apache2/sites-available/#{sitename}.conf" do
           source "virtualhosts.erb"
            mode "0644"
             variables(
             :document_root => document_root,
             :port => data["port"],
             :serveradmin => data["serveradmin"],
             :servername => data["servername"]
             )
            end

            end
            {{< /file >}}

The template resource's name should reflect the location where the virtual host file resides on the nodes. The source parameter specifies the name of the template file. With a mode of 0644, the file owner gains read and write privileges, while others are granted read privileges. The values defined in the variables section are extracted from the attributes file and correspond to those referenced in the template.

To ensure that sites are enabled in Apache and the server is restarted only when there are changes to the virtual hosts, incorporate the notifies parameter into the template resource. This command alerts Chef to execute actions only upon detected changes:

       {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}
       template "/etc/apache2/sites-available/#{sitename}.conf" do
       source "virtualhosts.erb"
        mode "0644"
        variables(
        :document_root => document_root,
        :port => data["port"],
        :serveradmin => data["serveradmin"],
        :servername => data["servername"]
        )
       notifies :restart, "service[apache2]"  
       end

       {{< /file >}}

The notifies command specifies the :action to be executed, followed by the resource type and resource name within square brackets.

Additionally, notifies can trigger execute commands, such as a2ensite, to enable sites with corresponding virtual host files. Insert the following execute command above the template resource code to generate the a2ensite script:

         {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}
         # [...]

             directory document_root do
             mode "0755"
             recursive true
             end

            execute "enable-sites" do
            command "a2ensite #{sitename}"
            action :nothing
            end

            template "/etc/apache2/sites-available/#{sitename}.conf" do

            # [...]

{{< /file >}}

The directive action :nothing indicates that the resource awaits activation. Introduce a new notifies line before the previous notifies line within the template resource code to activate it:

         {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}
         # [...]

         template "/etc/apache2/sites-available/#{sitename}.conf" do
         # [...]
         notifies :run, "execute[enable-sites]"
         notifies :restart, "service[apache2]"
          end

             # [...]
             {{< /file >}}

Ensure that the paths referenced in the virtual host files are established using the directory resource. This should be implemented before the final end tag:

         {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}
          # [...]

          node["lamp_stack"]["sites"].each do |sitename, data|
           # [...]

           directory "/var/www/html/#{sitename}/public_html" do
           action :create
           end

            directory "/var/www/html/#{sitename}/logs" do
            action :create
            end
            end

           {{< /file >}}

### Apache Configuration

After configuring the virtual host files and enabling your website, optimize Apache's performance on your servers by enabling and configuring a multi-processing module (MPM) and editing apache2.conf.

MPMs are located in Apache's mods_available directory. In this scenario, the prefork MPM will be utilized, located at /etc/apache2/mods-available/mpm_prefork.conf. If deploying to nodes of varying sizes, creating a template file to replace the original allows for more customization of specific variables. However, a cookbook file will be employed in this instance.

Cookbook files are static documents executed against the document in the same locale on your servers. Any modifications prompt the cookbook file to create a backup of the original file before replacing it with the new one.

To create a cookbook file, navigate to files/default from your cookbook's main directory. If the directories are absent, create them.

            mkdir -p ~/chef-repo/cookbooks/lamp_stack/files/default/
            cd ~/chef-repo/cookbooks/lamp_stack/files/default/

Generate a file named mpm_prefork.conf and duplicate the MPM event configuration into it, adjusting any necessary values as follows:

           {{< file "~/chef-repo/cookbooks/lamp_stack/files/default/mpm_prefork.conf" aconf >}}
               <IfModule mpm_prefork_module>
                StartServers            4
                MinSpareServers         3
               MaxSpareServers         40
              MaxRequestWorkers       200
            MaxConnectionsPerChild  10000
            </IfModule>
             {{< /file >}}

Return to apache.rb, and utilize the cookbook_file resource to call the newly created file. As the MPM needs to be enabled, employ the notifies command again, this time to execute a2enmod mpm_event. Integrate the execute and cookbook_file resources into the apache.rb file before the final end tag.

         {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}
          # [...]

          node["lamp_stack"]["sites"].each do |sitename, data|
          # [...]

           execute "enable-prefork" do
           command "a2enmod mpm_prefork"
           action :nothing
           end

           cookbook_file "/etc/apache2/mods-available/mpm_prefork.conf" do
           source "mpm_prefork.conf"
           mode "0644"
            notifies :run, "execute[enable-prefork]"
         end
       end
              {{< /file >}}

Within apache2.conf, set the KeepAlive value to off, the sole modification within the file. While this adjustment can be made through templates or cookbook files, a simple sed command paired with the execute resource will suffice in this instance. Update apache.rb with the new execute resource.

        {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/apache.rb" ruby >}}
       # [...]

         directory "/var/www/html/#{sitename}/logs" do
         action :create
         end

          execute "keepalive" do
          command "sed -i 's/KeepAlive On/KeepAlive Off/g' /etc/apache2/apache2.conf"
          action :run
          end

          execute "enable-prefork" do

          # [...]
          {{< /file >}}

Your apache.rb file is now complete. An example of the final file can be found here.

## MySQL

### Download the MySQL Library

Explore the OpsCode-maintained MySQL cookbook available on the Chef Supermarket, which facilitates the setup of MySQL lightweight resources/providers (LWRPs). Download and install the cookbook from your workstation:

             knife cookbook site install mysql

This process also handles the installation of any necessary dependencies. Notably, it includes the smf and yum-mysql-community cookbooks, which rely on the rbac and yum cookbooks.

Navigate to the main directory of your LAMP stack cookbook and access the metadata.rb file. Integrate a dependency on the MySQL cookbook:


           {{< file "~/chef-repo/cookbooks/lamp_stack/metadata.rb" >}}
           depends          'mysql', '~> 8.6.0'

           {{< /file >}}

Note: Verify the latest version of the MySQL Cookbook on its Supermarket page. Notably, the MySQL Cookbook does not yet support Ubuntu 18.04.

Proceed to upload these cookbooks to the server.


             knife cookbook upload mysql --include-dependencies

### Create and Encrypt Your MySQL Password

Chef incorporates a feature known as data bags, which store information and can be encrypted to safeguard passwords and other sensitive data.

On your workstation, generate a secret key.

        openssl rand -base64 512 > ~/chef-repo/.chef/encrypted_data_bag_secret

Upload this key to your node's /etc/chef directory, either manually via scp from the node (refer to the Setting Up Chef guide for an example) or through the utilization of a recipe and cookbook file.

On your workstation, establish a mysql data bag housing the rtpass.json file for the root password.

        knife data bag create mysql rtpass.json --secret-file ~/chef-repo/.chef/encrypted_data_bag_secret

Note: Certain knife commands necessitate editing information as JSON data using a text editor. Ensure your config.rb file contains a configuration for the text editor to use for such commands. If this configuration is absent, append knife[:editor] = "/usr/bin/vim" to the end of the file to designate vim as the default text editor.

You'll be prompted to edit the rtpass.json file:


          {{< file "~/chef-repo/data_bags/mysql/rtpass.json" json >}}
          {
          "id": "rtpass.json",
          "password": "password123"
          }
          {{< /file >}}

Substitute password123 with a robust password.

Validate the creation of the rtpass.json file:

        knife data bag show mysql

Executing this command should display rtpass.json. To confirm encryption, execute:

        knife data bag show mysql rtpass.json

The resulting output will be indecipherable due to encryption, resembling:

           {{< output >}}
         WARNING: Encrypted data bag detected, but no secret provided for decoding.  Displaying encrypted data.
          id:       rtpass.json
          password:
          cipher:         aes-256-cbc
           encrypted_data: wpEAb7TGUqBmdB1TJA/5vyiAo2qaRSIF1dRAc+vkBhQ=

            iv:             E5TbF+9thH9amU3QmGxWmw==

            version:        1
            user:
            cipher:         aes-256-cbc
            encrypted_data: VLA00Wrnh9DrZqDcytvo0HQUG0oqI6+6BkQjHXp6c0c=

            iv:             6V+3ROpW9RG+/honbf/RUw==

                version:        1
            {{</ output >}}

### Set Up MySQL

With the MySQL library acquired and an encrypted root password prepared, proceed to configure the recipe for MySQL download and setup.


Create a new file named mysql.rb in the recipes directory and define the data bag to be utilized:

        {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/mysql.rb" ruby >}}
         mysqlpass = data_bag_item("mysql", "rtpass.json")

         {{< /file >}}

Utilizing the LWRPs provided by the MySQL cookbook streamlines the initial installation and database creation process for MySQL:

          {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/mysql.rb" ruby >}}
          mysqlpass = data_bag_item("mysql", "rtpass.json")

          mysql_service "mysqldefault" do
          version '5.7'
          initial_root_password mysqlpass["password"]
          action [:create, :start]
          end

          {{< /file >}}

mysqldefault denotes the MySQL service name for this container. The inital_root_password refers to the value defined earlier, while the action initiates database creation and MySQL service startup.

Due to the MySQL version installed by the mysql cookbook utilizing a sock file at a non-standard location, you must specify this location to interact with MySQL from the command line. Create a cookbook file named my.cnf with the subsequent configuration:

       {{< file "~/chef-repo/cookbooks/lamp_stack/files/default/my.cnf" >}}
       [client]
        socket=/run/mysql-mysqldefault/mysqld.sock
         {{</ file >}}

Revisit mysql.rb and append the following lines to the end of the file:

        {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/mysql.rb" ruby >}}
        # [...]

       cookbook_file "/etc/my.cnf" do
       source "my.cnf"
       mode "0644"
        end
        {{< /file >}}

## PHP

Within the recipes directory, create a new file named php.rb. The following commands install PHP and all essential packages for Apache and MySQL compatibility:

         {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/php.rb" ruby >}}
          package "php" do
          action :install
           end

           package "php-pear" do
           action :install
            end

           package "php-mysql" do
           action :install
            end

            package "libapache2-mod-php" do
            action :install
            end
            {{< /file >}}

For streamlined configuration, the php.ini file will be generated and utilized as a cookbook file, similar to the MPM module setup above. You can either:

Implement the PHP recipe, execute chef-client, and retrieve the file from a node (located at /etc/php/7.0/cli/php.ini), or:

Copy it from the provided chef-php.ini sample. Move the file to the chef-repo/cookbooks/lamp_stack/files/default/ directory. Alternatively, this file can be converted into a template, depending on your configuration preferences.

The php.ini file is extensive. Locate and adjust the subsequent values to best match your uthos:

The suggested values provided below are tailored for 2GB uthos:

        {{< file "~/chef-repo/cookbooks/lamp_stack/files/default/php.ini" php >}}
         max_execution_time = 30
         memory_limit = 128M
         error_reporting = E_COMPILE_ERROR|E_RECOVERABLE_ERROR|E_ERROR|E_CORE_ERROR
         display_errors = Off
         log_errors = On
         error_log = /var/log/php/error.log
         max_input_time = 30
         {{< /file >}}

Proceed to php.rb and include the cookbook_file resource at the end of the recipe.

        {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/php.rb" ruby >}}
        cookbook_file "/etc/php/7.0/cli/php.ini" do
         source "php.ini"
         mode "0644"
         notifies :restart, "service[apache2]"
          end
          {{< /file >}}

As alterations have been made to php.ini, it necessitates the creation of a /var/log/php directory, with ownership set to the Apache user. This can be achieved through a notifies command paired with an execute resource, similar to previous procedures. Append these resources to the end of php.rb:

          {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/php.rb" ruby >}}
           execute "chownlog" do
           command "chown www-data /var/log/php"
           action :nothing
          end

          directory "/var/log/php" do
          action :create
          notifies :run, "execute[chownlog]"
           end
          {{< /file >}}

With these additions, the PHP recipe is now complete! You can review an example of the php.rb file here.

Verify that your Chef server incorporates the updated cookbook and confirm that your node's run list is current. Replace nodename with the name of your Chef node:

             knife cookbook upload lamp_stack
             knife node run_list add nodename "recipe[lamp_stack],recipe[lamp_stack::apache],recipe[lamp_stack::mysql],recipe[lamp_stack::php]"

## Testing Your Installation

To confirm the successful installation and operation of the Apache service, execute the following command, substituting node_name with your node's name:

        knife ssh 'name:node_name' 'systemctl status apache2' -x root

Additionally, you can access your server's domain name in your browser. If everything is functioning properly, you should encounter a Chef server page guiding you on setting up the Management Console, considering you haven't uploaded any files to your server yet.

To assess PHP's status, you'll need to upload a file to your server to ensure it's being rendered correctly. A straightforward PHP file you can create is a PHP info file. Establish a file named info.php in the same directory as the other cookbook files you've generated:

            {{< file "~/chef-repo/cookbooks/lamp_stack/files/default/info.php" php >}}
            <?php phpinfo(); ?>
            {{</ file >}}

Amend your php.rb file and append the following to the end, replacing example.com with your website's domain name:

        {{< file "~/chef-repo/cookbooks/lamp_stack/recipes/php.rb" ruby >}}
         cookbook_file "/var/www/html/example.com/public_html/info.php" do
        source "info.php"
         end
         {{</ file >}}

Upload your cookbook to your Chef server, then execute chef-client on your node, substituting node_name with your node's name:

        knife cookbook upload lamp_stack
        knife ssh 'name:node_name' 'sudo chef-client' -x root

Visit example.com/info.php in your browser. You should encounter a page displaying information about your PHP settings.

To evaluate your MySQL installation, verify the status of MySQL using systemctl. Issue the following command to ensure the MySQL service is functioning properly:

             knife ssh 'name:node_name' 'systemctl status mysql-mysqldefault' -u root

Note that chef-client isn't designed to accept user input. Consequently, utilizing commands like mysqladmin status that necessitate a password can cause Chef to hang. If you require direct interaction with the MySQL client, consider logging in to your server directly.

By following this guide, you've created a LAMP Stack cookbook. Throughout this process, you should have become familiar with utilizing various resources within a recipe, such as execute, package, service, node, directory, template, cookbook_file, and mysql_service. Additionally, you've learned to download and utilize LWRPs, create encrypted data bags, upload/update your cookbooks on the server, and leverage attributes, templates, and cookbook files. This provides you with a solid foundation in Chef and cookbook creation for future endeavors.
