---
slug:"Redmine Mastery: Conquer Project Management on Debian 5 (Lenny)
deprecated: true
description: 'Learn the ropes of setting up and fine-tuning Redmine, the open-source project management powerhouse, on your Debian 5 (Lenny) server powered by nginx.'
keywords: ["redmine", "redmine debian", "redmine linux", "project management software", "redmine postgresql", "redmine linux"]
tags: ["debian", "ruby", "nginx", "postgresql"]
modified: 2024-05-13
modified_by:
  name: utho
published: 2024-05-13
title: 'Manage Projects with Redmine on Debian 5 (Lenny)'
relations:
    platform:
        key: manage-projects-with-redmine
        keywords:
            - distribution: Debian 5
authors: ["Utho"]
---

Redmine stands out as a widely embraced open source solution for project management. Crafted using Ruby on Rails, it empowers teams to monitor project goals efficiently, seamlessly integrates with various source control systems, and offers adaptable reporting features. This walkthrough is designed to assist you in setting it up on your Debian 5 (Lenny) Utho. Our approach involves leveraging nginx alongside Phusion Passenger as the web server daemon for your platform. If you're currently utilizing the Apache web server, fret not, as we'll furnish guidance on routing incoming Redmine requests to nginx, operating on a distinct port.

We presume you've already completed the preliminary steps as detailed in our "Setting Up and Securing a Compute Instance" guide. Ensure you're logged in as root on your Utho via SSH before proceeding. In this guide, we consistently employ the placeholder domain "example.com"; remember to substitute it with your actual domain name at each step.

## Basic System Configuration

Issue the following commands to update your local package database and install any outstanding updates.

    apt-get update
    apt-get upgrade --show-upgraded

Issue the following commands to set your system hostname. This example uses "redmine" as the hostname; feel free to substitute your own name.

    echo "redmine" > /etc/hostname
    hostname -F /etc/hostname

Edit your `/etc/hosts` file to resemble the following, substituting your utho's public IP address for 12.34.56.78, your domain name for "example.com", and your hostname for "redmine".

{{< file "/etc/hosts" >}}
127.0.0.1 localhost.localdomain localhost 12.34.56.78 redmine.example.com redmine

{{< /file >}}


## Nginx Installation and Configuration

### Install Prerequisite Packages

Issue the following command to install packages required for Ruby on Rails and other components.

                              apt-get install wget build-essential ruby1.8 ruby1.8-dev irb1.8 rdoc1.8 zlib1g-dev libpcre3-dev libopenssl-ruby1.8 libzlib-ruby libssl-dev libcurl4-openssl-dev libpq-dev postgresql subversion

Create symbolic links to the installed version of Ruby:

                               ln -s /usr/bin/ruby1.8 /usr/bin/ruby
                               ln -s /usr/bin/irb1.8 /usr/bin/irb

Fetch the newest version of the RubyGems source from the [RubyForge download page](http://rubyforge.org/projects/rubygems/). Issue the following commands, substituting the download link for the current version:

                       wget http://production.cf.rubygems.org/rubygems/rubygems-1.3.7.tgz
                       tar -xf rubygems*tgz
                       cd rubygems*
                       ruby setup.rb
                       ln -s /usr/bin/gem1.8 /usr/bin/gem

Install some required gems:

                                    gem install -v=1.0.1 rack
                                    gem install fastthread
                                    gem install -v=2.3.5 rails
                                    gem install postgres
                                    gem install activerecord
                                    gem install pg
                                    gem install i18n -v=0.4.2
                                    gem uninstall i18n -v0.5.0

### Install Passenger and Nginx
Navigate to the [Phusion Passenger](http://www.modrails.com/install.html) website and locate the link for the latest source code tarball. Download it using the following command (replace the link with the current version):

                         cd /opt
                         wget http://rubyforge.org/frs/download.php/73563/passenger-3.0.1.tar.gz
                         tar xzvf passenger*.gz

Run the Phusion Passenger installer for Nginx:

                         cd passenger*/bin
                         ./passenger-install-nginx-module

During the Phusion Passenger Nginx installer process, you'll encounter prompts. Press "Enter" to proceed with the installation. When prompted for the Nginx installation method, opt for "1" to allow the installer to automatically handle the download, compilation, and installation of Nginx. This is the recommended approach unless you have specific requirements necessitating custom Nginx options during compilation.

Remember **not** to delete the Passenger files from `opt` post-installation. They must remain in place for your installation to operate correctly.

### Configure Nginx

Nginx has been successfully installed in `/opt/nginx`, but for effective management, we need an "init" script. Execute the subsequent commands to download the script, set appropriate permissions, and configure startup links for the system:

                       cd /opt/
                       wget -O init-nginx-deb.sh http://www.utho.com/docs/assets/705-init-nginx-deb.sh
                       mv /opt/init-nginx-deb.sh /etc/init.d/nginx
                       chmod +x /etc/init.d/nginx
                       /usr/sbin/update-rc.d -f nginx defaults

With this setup, you can control Nginx just like any other server daemon, allowing you to start, stop, and restart it as needed.

## Proxying Redmine with Apache

If Apache is already running on your utho, you'll need to instruct nginx to operate on an alternate port and forward requests for your Redmine installation to it. Similar adjustments are required for other web servers. This section is entirely optional and pertains specifically to Apache users.

To enable proxy support, run the following commands:

                        a2enmod proxy
                        a2enmod proxy_http
                        /etc/init.d/apache2 restart

Create an Apache virtualhost tailored for your Redmine deployment. The example provided below assumes Apache is configured according to our [Ubuntu 10.04 LAMP guide](/docs/guides/apache-2-web-server-on-ubuntu-10-04-lts-lucid/). Ensure to substitute "12.34.56.78" with your utho's IP address, `support@example.com` with your admin email, and "redmine.example.com" with your Redmine domain.

                        {{< file "/etc/apache2/sites-available/redmine.example.com" apache >}}
                        <VirtualHost 12.34.56.78:80>
                         ServerAdmin support@example.com
                         ServerName redmine.example.com

                         ProxyPass / http://localhost:8080/
                         ProxyPassReverse / http://localhost:8080/

                        # Uncomment the line below if your site uses SSL.
                        #SSLProxyEngine On
                       </VirtualHost>

                        {{< /file >}}

Activate the site and reload Apache using the following commands:

                        a2ensite redmine.example.com
                        /etc/init.d/apache2 reload

Now, adjust nginx to operate on a different port by editing its configuration file and setting the specified value:

                        {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
                        listen 8080;

                        {{< /file >}}

## Installing and Configuring Redmine

### Obtain Redmine

Refer to the [Redmine project site](http://www.redmine.org/wiki/redmine/Download) to acquire the current stable branch version. Employ `svn` to check out the code by executing the following commands, updating the URL on the final line if necessary.

                       mkdir -p /srv/www/redmine.example.com
                       cd /srv/www/redmine.example.com/
                       svn co http://redmine.rubyforge.org/svn/branches/1.0-stable redmine-1.0
                       mv redmine-1.0 redmine

For future updates, utilize `svn up` from the `redmine` directory to keep it current.

### Create and Configure the Database

Switch to the `postgres` user and initiate the `psql` shell with these commands:

                      su - postgres
                      psql

Within the `psql` shell, execute the following commands to configure the database for Redmine. Ensure to replace "changeme" with a strong, unique password.

                      CREATE ROLE redmine LOGIN ENCRYPTED PASSWORD 'changeme' NOINHERIT VALID UNTIL 'infinity';
                      CREATE DATABASE redmine WITH ENCODING='UTF8' OWNER=redmine TEMPLATE=template0;
                      \q
                      exit
                      cd redmine

Generate the file `config/database.yml` with the subsequent contents, substituting "changeme" with the password set in the previous step.

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

To finalize the database setup, execute the following commands:

                     chmod 600 config/database.yml
                     rake config/initializers/session_store.rb
                     RAILS_ENV=production rake db:migrate
                     RAILS_ENV=production rake redmine:load_default_data

### Configure Email Service

Execute the following commands to install `exim4` and configure it for outgoing Internet email delivery. However, if you already have an SMTP server configured that accepts unauthenticated locally sent mail, you can skip the Exim installation. Note that you'll still need to create Redmine's email configuration file, as demonstrated at the end of this section.

                    apt-get install exim4
                    dpkg-reconfigure exim4-config

Choose "internet site" as the type of mail configuration to utilize.

Specify your system's fully qualified domain name as the system mail name.

Input "127.0.0.1" when prompted for the IP address to listen on for SMTP connections. This restricts SMTP connections to localhost, ensuring security for Redmine's mail sending capability.

Provide "localhost.localdomain" and your fully qualified domain name when prompted for the list of recipient domains.

Leave relay domains and machines blank.

Opt for "No" when queried about DNS queries.

When prompted for maildirs versus mbox format, you may select either. Maildirs are increasingly favored by modern mail tools.

Choose "No" when asked whether to split the configuration into smaller files.

Input "root" and an email address at your domain for the postmaster mail query.

Create the file `config/email.yml` with the following content. Ensure to replace the domain field with your fully qualified domain name.

                        {{< file "config/email.yml" yaml >}}
                         production:
                         delivery_method: :smtp
                         smtp_settings:
                         address: 127.0.0.1
                         port: 25
                         domain: redmine.example.com
                         authentication: :none

                         {{< /file >}}

This concludes the email configuration for your Redmine setup.

### Final Configuration and Testing

Let's establish a "redmine" user to oversee the installation. Execute the ensuing commands to set ownership and permissions on your Redmine files. Remember to assign a unique, robust password for the Redmine user.

                         adduser redmine
                         cd /srv/www/redmine.example.com/
                         chown -R redmine:redmine *
                         cd redmine
                         chmod -R 755 files log tmp public/plugin_assets

Edit the file `/opt/nginx/conf/nginx.conf`, adjusting the "user" parameter to "redmine".

                         {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
                         user  redmine;

                         {{< /file >}}

 Append a server section after the initial example server, as depicted below. If you're proxying to nginx from another web server, modify the `listen` directive to `listen 8080`; instead of the default. Substituting "redmine.example.com" with your Redmine site's domain is essential.

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

Your Redmine installation should now be accessible at http://redmine.example.com. If you encounter any issues, refer to your log files for a record of any errors. The default login credentials are username "admin" and password "admin". It's advisable to change the admin password immediately. Congratulations on successfully installing Redmine for project management on your utho!

## Monitor for Software Updates and Security Notices

When utilizing software compiled or installed directly from upstream sources, it's crucial to monitor updates, bug fixes, and security issues. Stay informed about releases and potential issues, and promptly update your software to address flaws and prevent system compromise. Regularly monitoring releases and maintaining up-to-date software versions are paramount for system security and integrity.

Stay vigilant by monitoring the Redmine project issue queue and news feed to ensure you're informed about all software updates. This ensures you can upgrade appropriately or apply patches and recompile as necessary.

-   [Redmine News Feed](http://www.redmine.org/projects/redmine/issues)
-   [Redmine Issue Queue](http://www.redmine.org/projects/redmine/news)

When upstream sources offer new releases, repeat the instructions for installing Redmine software as needed. These practices are crucial for the ongoing security and functioning of your system.
