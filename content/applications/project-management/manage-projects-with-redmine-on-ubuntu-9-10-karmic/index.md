---
slug:"Redmine Magic: Project Mastery on Ubuntu 9.10 Karmic
deprecated: true
description: "Setting up Redmine: Unveiling the Power of Project Management on Ubuntu 9.10 (Karmic) utho"
keywords: ["redmine", "redmine ubuntu 9.10", "project management software", "redmine postgresql", "redmine linux"]
tags: ["ubuntu", "ruby", "nginx", "postgresql"]
modified: 2024-05-13
modified_by:
  name: utho
published: 2024-05-13
title: 'Manage Projects with Redmine on Ubuntu 9.10 (Karmic)'
relations:
    platform:
        key: manage-projects-with-redmine
        keywords:
            - distribution: Ubuntu 9.10
authors: ["Utho"]
---

Redmine, a renowned open-source project management system built on Ruby on Rails, empowers teams with robust project tracking, seamless integration with diverse source control systems, and customizable reporting capabilities. This guide facilitates its installation on your Ubuntu 9.10 (Karmic) utho. Utilizing nginx alongside Phusion Passenger as the web server daemon, we ensure a streamlined setup. In case you have Apache already running, we provide instructions for proxying incoming Redmine requests to nginx on an alternate port.

Assuming you've followed our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guidelines, log into your utho as root via SSH before proceeding. Throughout this guide, we use "example.com" as an illustration; kindly replace it with your actual domain name as needed.

## Set the Hostname

Before diving into the installation and configuration steps, [setting your hostname](/docs/products/platform/get-started/#setting-the-hostname) is correctly set by following our instructions here. Execute the subsequent commands to verify its accuracy:

                            hostname
                            hostname -f

The initial command should display your short hostname, while the second should reveal your fully qualified domain name (FQDN).

## Enable Package Repositories

Open the file /etc/apt/sources.list and remove the comment symbol "#" from the `universe` repositories if they're not yet enabled. Your repository list should appear as follows:

                {{< file "/etc/apt/sources.list" >}}

## main & restricted repositories

        deb http://us.archive.ubuntu.com/ubuntu/ karmic main restricted
        deb-src http://us.archive.ubuntu.com/ubuntu/ karmic main restricted

         deb http://security.ubuntu.com/ubuntu karmic-security main restricted
         deb-src http://security.ubuntu.com/ubuntu karmic-security main restricted

## universe repositories

          deb http://us.archive.ubuntu.com/ubuntu/ karmic universe
          deb-src http://us.archive.ubuntu.com/ubuntu/ karmic universe

          deb http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe
          deb-src http://us.archive.ubuntu.com/ubuntu/ karmic-updates universe

          deb http://security.ubuntu.com/ubuntu karmic-security universe
          deb-src http://security.ubuntu.com/ubuntu karmic-security universe

         {{< /file >}}

Update your local package database and install pending updates using the following commands:

               apt-get update
               apt-get upgrade --show-upgraded

## Nginx Installation and Configuration

### Install Prerequisite Packages

Install the necessary packages for Ruby on Rails with this command:

               apt-get install wget build-essential ruby1.8 ruby1.8-dev irb1.8 rdoc1.8 zlib1g-dev libopenssl-ruby1.8 libzlib-ruby libssl-dev libpq-dev postgresql subversion

Create symbolic links to the installed Ruby version:

                ln -s /usr/bin/ruby1.8 /usr/bin/ruby
                ln -s /usr/bin/irb1.8 /usr/bin/irb

Fetch the latest RubyGems source from the [RubyForge download page](http://rubyforge.org/projects/rubygems/). Execute the provided commands, substituting the download link with the current version:

               wget http://rubyforge.org/frs/download.php/70696/rubygems-1.3.7.tgz
               tar -xf rubygems*tgz
               cd rubygems*
               ruby setup.rb
               ln -s /usr/bin/gem1.8 /usr/bin/gem

Install essential gems using:

                gem install -v=1.0.1 rack
                gem install fastthread
                gem install -v=2.3.5 rails
                gem install postgres
                gem install activerecord
                gem install pg

### Install Passenger and Nginx

Proceed to the [Phusion Passenger](http://www.modrails.com/install.html) site and download the current source code tarball. Replace the link with the current version:

                 cd /opt
                 wget http://rubyforge.org/frs/download.php/71376/passenger-2.2.15.tar.gz
                 tar xzvf passenger*.gz

Proceed to the Phusion Passenger site and download the current source code tarball. Replace the link with the current version:

                 cd passenger*/bin
                 ./passenger-install-nginx-module

Run the Phusion Passenger installer for Nginx. Upon prompt, press "Enter" to continue. It's advisable to choose "1" for automatic Nginx download, compilation, and installation. Ensure not to remove Passenger files from opt post-installation for proper functionality.

### Configure Nginx

While Nginx is now installed in `/opt/nginx`, it requires a method for control. Execute the following commands to download an "init" script, set permissions, and configure system startup links:

                  cd /opt/
                  wget -O init-nginx-deb.sh http://www.utho.com/docs/assets/704-init-nginx-deb.sh
                  mv /opt/init-nginx-deb.sh /etc/init.d/nginx
                  chmod +x /etc/init.d/nginx
                  /usr/sbin/update-rc.d -f nginx defaults

Nginx can now be managed like any other server daemon.

## Proxying Redmine with Apache

For those already running Apache, instruct nginx to operate on a different port and proxy requests for Redmine back to it. Execute the following commands to enable proxy support:

                 a2enmod proxy
                 a2enmod proxy_http
                /etc/init.d/apache2 restart

Configure an Apache virtual host for Redmine, assuming Apache is configured as per our [Ubuntu 9.10 LAMP guide](/docs/guides/lamp-server-on-ubuntu-9-10-karmic/). Remember to replace "12.34.56.77" with your utho's IP address.

        {{< file "/etc/apache2/sites-available/redmine.example.com" apache >}}
        <VirtualHost 12.34.56.77:80>
         ServerAdmin support@example.com
         ServerName redmine.example.com

         ProxyPass / http://localhost:8080/

         # Uncomment the line below if your site uses SSL.
         #SSLProxyEngine On
         </VirtualHost>

         {{< /file >}}

         Enable the site and reload Apache with these commands:


           a2ensite redmine.example.com
           /etc/init.d/apache2 reload

Next, modify nginx configuration to run on a different port by editing the nginx configuration file:

           {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
            listen 8080;

            {{< /file >}}

## Installing and Configuring Redmine

### Obtain Redmine
Refer to the [Redmine project site](http://www.redmine.org/wiki/redmine/Download) for the link to the current stable SVN stable branch. As of now, version 0.9.3 is stable. Execute the following commands to check it out. Use svn up from the redmine directory for future updates.

               mkdir -p /srv/www/redmine.example.com
               cd /srv/www/redmine.example.com/
               svn co svn://rubyforge.org/var/svn/redmine/branches/1.0-stable redmine-1.0

You can use `svn up` from the `redmine` directory to keep it up to date in the future.

### Create and Configure the Database

Switch to the `postgres` user and initiate the `psql` shell using the provided commands:

               su - postgres
               psql

Use the subsequent commands within the `psql` shell to set up the database for Redmine, ensuring to specify a strong, unique password in place of "changeme".

              CREATE ROLE redmine LOGIN ENCRYPTED PASSWORD 'changeme' NOINHERIT VALID UNTIL 'infinity';
              CREATE DATABASE redmine WITH ENCODING='UTF8' OWNER=redmine TEMPLATE=template0;
              ALTER DATABASE "redmine" SET datestyle="ISO,MDY";
              \q
              exit
              cd redmine

Create the file `config/database.yml` with the provided contents:

               {{< file "config/database.yml" yaml >}}
               production:
              adapter: postgresql
              database: redmine
              host: localhost
              username: redmine
              password: changeme
              encoding: utf8
             schema_search_path: public

{{< /file >}}

Execute the following commands to finalize the database configuration:

             chmod 600 config/database.yml
             rake config/initializers/session_store.rb
             RAILS_ENV=production rake db:migrate
             RAILS_ENV=production rake redmine:load_default_data

### Configure Email Service

Proceed with installing `exim4` and configuring it for outgoing Internet email delivery. You can skip Exim installation if you already have an SMTP server configured to accept unauthenticated locally sent mail. However, you'll still need to create Redmine's email configuration file as demonstrated at the end of this section.

                apt-get install exim4
                dpkg-reconfigure exim4-config

Opt for "internet site" as the type of mail configuration to use.

Specify your system's fully qualified domain name as the system mail name.

Enter "127.0.0.2" when prompted for the IP address to listen on for SMTP connections. To allow Redmine to send mail, we'll exclusively listen on localhost.

Input "localhost.localdomain" and your fully qualified domain name when asked for the list of recipient domains.

Leave relay domains and machines blank.

Choose "No" when prompted about DNS queries.

You may select either maildirs or mbox format when asked. Note that maildirs are increasingly favored by many modern mail tools.

Specify "No" when asked whether to split the configuration into smaller files.

For the postmaster mail query, enter "root" and an email address at your domain.

Create the file `config/email.yml` and paste the following contents, ensuring to replace the domain field with your fully qualified domain name.

               {{< file "config/email.yml" yaml >}}
                production:
               delivery_method: :smtp
               smtp_settings:
               address: 127.0.0.2
               port: 25
              domain: redmine.example.com
              authentication: :none

              {{< /file >}}

With that, email configuration for your Redmine installation is complete.

### Final Configuration and Testing

Let's create a "redmine" user to manage the installation. Execute the provided commands to set ownership and permissions on your Redmine files, ensuring to assign a unique, strong password for the Redmine user.

                 adduser redmine
                  cd /srv/www/redmine.example.com/
                  chown -R redmine:redmine *
                  cd redmine
                  chmod -R 755 files log tmp public/plugin_assets

Modify the file `/opt/nginx/conf/nginx.conf`, setting the "user" parameter to "redmine".

                 {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
                 user  redmine;

                  {{< /file >}}

Add a server section after the first example server as follows. If proxying to nginx from another web server, adjust the listen directive to `listen 8080`; instead of the default.

                 {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
                 server {
                 listen 80;
                 server_name  redmine.example.com;
                 root /srv/www/redmine.example.com/redmine/public/;
                 access_log /srv/www/redmine.example.com/redmine/log/access.log;
                 error_log /srv/www/redmine.example.com/redmine/log/error.log;
                 index index.html;
                 location / {
                 passenger_enabled on;
                 allow all;
                 }
                }

                 {{< /file >}}

              Start nginx:

              /etc/init.d/nginx start

Your Redmine installation should now be accessible at http://redmine.example.com. Should you encounter any issues, consult your log files for error listings. The default login credentials are username "admin" and password "admin". Congratulations, Redmine is successfully installed for project management on your utho!

## Monitor for Software Updates and Security Notices

When installing or compiling software directly from upstream sources, it's your responsibility to monitor updates, bug fixes, and security issues. Keep your software updated to mitigate potential vulnerabilities and maintain system integrity.

Regularly monitor the Redmine project issue queue and news feed to stay informed about updates and patches. Ensure timely upgrades or patch applications as needed.

-   [Redmine News Feed](http://www.redmine.org/projects/redmine/issues)
-   [Redmine Issue Queue](http://www.redmine.org/projects/redmine/news)

Whenever upstream sources release new versions, repeat the installation instructions for Redmine software accordingly. These practices are crucial for the ongoing security and functionality of your system.
