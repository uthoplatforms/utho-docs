---
slug: "Project Mastery: Redmine Unleashed on Debian 6 (Squeeze)"
deprecated: true
description: '"Unlock the Potential: Setting up and Fine-tuning Redmine, the Open-Source Project Management Dynamo, on your Debian 6 (Squeeze) utho, Powered by nginx'
keywords: ["redmine", "redmine debian 6", "redmine linux", "project management software", "redmine postgresql"]
tags: ["debian", "ruby", "nginx", "postgresql"]
modified: 2024-05-13
modified_by:
  name: Utho
published: 2011-05-16
title: 'Manage Projects with Redmine on Debian 6 (Squeeze)'
relations:
    platform:
        key: manage-projects-with-redmine
        keywords:
            - distribution: Debian 6
authors: ["Utho"]
---

Dive into installing Redmine on your Debian 6 (Squeeze) utho with our comprehensive guide. Ensure you've already completed the steps outlined in our Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/). Log in to your utho as root via SSH before proceeding. Throughout this guide, we'll use "example.com" as our domain placeholder, so remember to replace it with your actual domain wherever mentioned.

## Set the Hostname

Before embarking on the installation and configuration journey outlined in this guide, verify that you've [setting your hostname](/docs/products/compute/compute-instances/guides/set-up-and-secure/#configure-a-custom-hostname) correctly by following our instructions for configuring a custom hostname. Run the following commands to ensure it's properly set:

                hostname
                hostname -f

Check your short hostname with the first command and your fully qualified domain name (FQDN) with the second.

## Install Rails Packages and nginx with Phusion Passenger

Update your local package database and install any pending updates with the following commands:


              apt-get update
              apt-get upgrade --show-upgraded

Install the necessary packages for Ruby on Rails and other components using this command:


             apt-get install wget build-essential ruby1.8 ruby1.8-dev irb1.8 rdoc1.8 zlib1g-dev libpcre3-dev libopenssl-ruby1.8 libzlib-ruby libssl-dev libcurl4-openssl-dev libpq-dev postgresql subversion
Create symbolic links to the installed Ruby version:

             ln -s /usr/bin/ruby1.8 /usr/bin/ruby
             ln -s /usr/bin/irb1.8 /usr/bin/irb

Fetch and install RubyGems version 1.5.3 by running these commands:

            wget http://rubyforge.org/frs/download.php/74343/rubygems-1.5.3.tgz
            tar -xf rubygems*tgz
            cd rubygems*
            ruby setup.rb
            ln -s /usr/bin/gem1.8 /usr/bin/gem

Install the required gems to proceed:

           gem install -v=1.1.0 rack
           gem install fastthread
           gem install -v=2.3.5 rails
           gem install -v=0.4.2 i18n
           gem install postgres
           gem install pg

Navigate to the [Phusion Passenger](http://www.modrails.com/install.html) site and locate the link for the current source code tarball. Download it by substituting the link with the current version.

              cd /opt
              wget http://rubyforge.org/frs/download.php/74471/passenger-3.0.5.tar.gz
              tar xzvf passenger*.gz

Execute the Phusion Passenger installer for Nginx:

             cd passenger*/bin
            ./passenger-install-nginx-module

During the Phusion Passenger nginx installer process, press "Enter" to continue. Opt for "1" when prompted for the Nginx installation method, allowing the installer to automatically handle the download, compilation, and installation of Nginx. Please refrain from removing the Passenger files from `opt` post-installation, as they are essential for correct operation.

Lastly, create the file `/etc/init.d/nginx` with the following contents:

              {{< file "/etc/init.d/nginx" bash >}}

               #!/bin/sh

              ### BEGIN INIT INFO
              # Provides:          nginx
              # Required-Start:    $all
              # Required-Stop:     $all
              # Default-Start:     2 3 4 5
              # Default-Stop:      0 1 6
              # Short-Description: starts the nginx web server
              # Description:       starts nginx using start-stop-daemon
              ### END INIT INFO

               PATH=/opt/nginx/sbin:/sbin:/bin:/usr/sbin:/usr/bin
               DAEMON=/opt/nginx/sbin/nginx
               NAME=nginx
               DESC=nginx

               test -x $DAEMON || exit 0

# Include nginx defaults if available

              if [ -f /etc/default/nginx ] ; then
              . /etc/default/nginx
              fi

             set -e

              case "$1" in
              start)
              echo -n "Starting $DESC: "
              start-stop-daemon --start --quiet --pidfile /opt/nginx/logs/$NAME.pid \
              --exec $DAEMON -- $DAEMON_OPTS
              echo "$NAME."
              ;;
              stop)
              echo -n "Stopping $DESC: "
              start-stop-daemon --stop --quiet --pidfile /opt/nginx/logs/$NAME.pid \
              --exec $DAEMON
              echo "$NAME."
              ;;
              restart|force-reload)
              echo -n "Restarting $DESC: "
              start-stop-daemon --stop --quiet --pidfile \
              /opt/nginx/logs/$NAME.pid --exec $DAEMON
              sleep 1
              start-stop-daemon --start --quiet --pidfile \
              /opt/nginx/logs/$NAME.pid --exec $DAEMON -- $DAEMON_OPTS
              echo "$NAME."
              ;;
              reload)
              echo -n "Reloading $DESC configuration: "
              start-stop-daemon --stop --signal HUP --quiet --pidfile     /opt/nginx/logs/$NAME.pid \
              --exec $DAEMON
              echo "$NAME."
              ;;
              *)
              N=/etc/init.d/$NAME
              echo "Usage: $N {start|stop|restart|reload|force-reload}" >&2
              exit 1
              ;;
              esac
              exit 0

              {{< /file >}}

To ensure the script is executable and set to start on boot, execute the following commands:

               chmod +x /etc/init.d/nginx
               update-rc.d -f nginx defaults

## Optional: Proxy Redmine with Apache

If Apache is already running on your utho, you'll need to instruct nginx to operate on a different port and forward requests for your Redmine installation to it. Similar adjustments are necessary for other web servers. This section is entirely optional and specifically addresses Apache users.

Enable proxy support with the following commands:

               a2enmod proxy
               a2enmod proxy_http
               /etc/init.d/apache2 restart

Set up an Apache virtual host for your Redmine installation. The example provided assumes Apache is configured as per our [Debian 6 LAMP guide](/docs/guides/lamp-server-on-debian-6-squeeze/). Remember to substitute "12.34.56.78" with your utho's IP address, `support@example.com` with your admin email, and "redmine.example.com" with your Redmine domain.

            {{< file "/etc/apache2/sites-available/redmine.example.com"  apache >}}
            <VirtualHost *:80>
            ServerAdmin support@example.com
            ServerName redmine.example.com

            ProxyPass / http://localhost:8080/
            ProxyPassReverse / http://localhost:8080/

          # Uncomment the line below if your site uses SSL.
          #SSLProxyEngine On
          </VirtualHost>

          {{< /file >}}

Enable the site and reload Apache using these commands:


            a2ensite redmine.example.com
           /etc/init.d/apache2 reload

Next, configure nginx to run on a different port by editing its configuration file and adjusting the value accordingly:

           {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
           listen 8080;

          {{< /file >}}

## Install and Configure Redmine

### Obtain Redmine
Refer to the [Redmine project site](http://www.redmine.org/wiki/redmine/Download) to find the current version number for the stable branch. Use `svn` to check out the code by executing the following commands, updating the URL on the final line if necessary. Keep it up to date in the future using svn up from the redmine directory.

            mkdir -p /srv/www/redmine.example.com
            cd /srv/www/redmine.example.com/
            svn co http://redmine.rubyforge.org/svn/branches/1.1-stable redmine-1.1
            mv redmine-1.1 redmine

You can use `svn up` from the `redmine` directory to keep it up to date in the future.

### Create and Configure the Database

Switch to the `postgres` user and initiate the `psql` shell with these commands:


           su - postgres
           psql

Configure the database for Redmine within the `psql` shell using the provided commands. Ensure to specify a strong, unique password instead of "changeme".

           CREATE ROLE redmine LOGIN ENCRYPTED PASSWORD 'changeme' NOINHERIT VALID UNTIL 'infinity';
           CREATE DATABASE redmine WITH ENCODING='UTF8' OWNER=redmine TEMPLATE=template0;
           \q
           exit
           cd redmine

Create the file `config/database.yml` with the subsequent contents, replacing "changeme" with the password set in the previous step.

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

Complete the database configuration by executing the following commands:

             chmod 600 config/database.yml
             rake config/initializers/session_store.rb
             RAILS_ENV=production rake db:migrate
             RAILS_ENV=production rake redmine:load_default_data

If you encounter an error message after running the rake `db:migrate` command, edit the `config/environment.rb` file to include the provided excerpt between the bootstrap and initializer sections. After editing, retry the rake db:migrate command.

            {{< file "config/environment.rb" ruby >}}
             if Gem::VERSION >= "1.3.6"
             module Rails
            class GemDependency
            def requirement
            r = super
           (r == Gem::Requirement.default) ? nil : r
                 end
                end
             end
          end

         {{< /file >}}

### Configure Email Service

Install `exim4` and configure it for outgoing Internet email delivery with the following commands. You can skip Exim installation if you already have an SMTP server configured to accept unauthenticated locally sent mail. However, you'll still need to create Redmine's email configuration file as demonstrated at the end of this section.


              apt-get install exim4
              dpkg-reconfigure exim4-config

Choose "internet site" as the type of mail configuration to utilize.

Specify your system's fully qualified domain name as the system mail name.

Input "127.0.0.1" when prompted for the IP address to listen on for SMTP connections. For Redmine mail sending, limit listening to localhost.

Enter "localhost.localdomain" and your fully qualified domain name when prompted for the list of recipient domains. Leave relay domains and machines blank.

Relay domains and machines should be left blank.

Opt for "No" when asked about DNS queries.

When prompted to choose between maildirs and mbox format, feel free to select your preference. However, note that maildirs are increasingly favored by many modern mail tools.

Specify "No" when asked whether to split the configuration into smaller files.

Provide "root" and an email address at your domain for the postmaster mail query.

Create the file `config/email.yml` and paste the following contents. Ensure to replace the domain field with your fully qualified domain name.

             {{< file "config/email.yml" yaml >}}
              production:
              delivery_method: :smtp
              smtp_settings:
              address: 127.0.0.2
              port: 25
              domain: redmine.example.com
              authentication: :none

              {{< /file >}}

Email configuration for your Redmine installation is now complete.

### Final Configuration and Testing

Let's establish a "redmine" user to oversee the installation. Execute the following commands to set ownership and permissions on your Redmine files, ensuring to assign a unique, strong password for the Redmine user.

               adduser redmine
               cd /srv/www/redmine.example.com/
               chown -R redmine:redmine *
              cd redmine
             chmod -R 755 files log tmp public/plugin_assets

Edit the file `/opt/nginx/conf/nginx.conf`, adjusting the "user" parameter to "redmine".

              {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
              user redmine;

               {{< /file >}}

Add a server section after the initial example server. Replace "redmine.example.com" with your Redmine site's domain. If you're proxying to nginx from another web server, modify the listen directive accordingly (e.g., `listen 8080`;).

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

Access your Redmine installation at `http://redmine.example.com`. Should you encounter any issues, consult your log files for error listings. The default login credentials are username "admin" and password "admin". It's recommended to change the admin password immediately. Congratulations, Redmine is now installed on your utho for project management!

## Monitor for Software Updates and Security Notices

When using software compiled or installed directly from upstream sources, it's your responsibility to monitor updates, bug fixes, and security issues. Stay vigilant, update your software promptly upon release, and maintain up-to-date versions for system security and integrity.

Stay informed by monitoring the Redmine project issue queue and news feed. Ensure you're aware of all software updates and upgrade or apply patches accordingly.

-   [Redmine News Feed](http://www.redmine.org/projects/redmine/issues)
-   [Redmine Issue Queue](http://www.redmine.org/projects/redmine/news)
