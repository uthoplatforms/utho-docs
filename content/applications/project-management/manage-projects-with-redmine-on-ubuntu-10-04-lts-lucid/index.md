---
slug: "Effortless Project Management: Redmine on Ubuntu 10.04 LTS (Lucid)"
deprecated: true
description: 'Discover the seamless setup and optimization process for Redmine, an open-source project management system, on an Ubuntu 10.04 LTS (Lucid) Utho powered by nginx'
keywords: ["redmine", "redmine ubuntu 10.04", "redmine linux", "project management software", "redmine postgresql"]
tags: ["ubuntu", "ruby", "nginx", "postgresql"]
modified: 2024-05-13
modified_by:
  name: Utho
published: 2024-05-13
title: 'Manage Projects with Redmine on Ubuntu 10.04 LTS (Lucid)'
relations:
    platform:
        key: manage-projects-with-redmine
        keywords:
            - distribution: Ubuntu 10.04
authors: ["Utho"]
---

This tutorial provides instructions for installing Redmine on your Ubuntu 10.04 LTS (Lucid) Utho. We assume you've already completed the steps detailed in our Setting Up and Securing a Compute Instance. Before proceeding, ensure you're logged into your Utho as root via an SSH session. Throughout this guide, we'll use the example domain "example.com". Remember to replace it with your own domain name whenever necessary.

## Set the Hostname

Prior to commencing the installation and configuration procedures outlined in this guide, ensure you have followed our guidelines for [setting your hostname](/docs/products/platform/get-started/#setting-the-hostname). Execute the following commands to verify that your hostname is correctly configured:

                     hostname
                     hostname -f

The initial command will display your short hostname, while the subsequent one will reveal your fully qualified domain name (FQDN).

## Install Rails Packages and nginx with Phusion Passenger

Update your local package database and install any pending updates using the following commands.

                   apt-get update
                   apt-get upgrade --show-upgraded

Execute the following command to install packages essential for Ruby on Rails and other components.

                    apt-get install wget build-essential ruby1.8 ruby1.8-dev irb1.8 rdoc1.8 zlib1g-dev libpcre3-dev libopenssl-ruby1.8 libzlib-ruby libssl-dev libcurl4-openssl-dev libpq-dev postgresql subversion

Create symbolic links to the installed Ruby version.


                     ln -s /usr/bin/ruby1.8 /usr/bin/ruby
                     ln -s /usr/bin/irb1.8 /usr/bin/irb

Fetch and install RubyGems version 1.5.3 with the provided commands:

                 wget http://rubyforge.org/frs/download.php/74343/rubygems-1.5.3.tgz
                 tar -xf rubygems*tgz
                 cd rubygems*
                  ruby setup.rb
                  ln -s /usr/bin/gem1.8 /usr/bin/gem

Install necessary gems by executing the following commands:

                gem install -v=1.1.0 rack
                gem install fastthread
                gem install -v=2.3.5 rails
                gem install -v=0.4.2 i18n
                gem install postgres
                 gem install pg

Visit the [Phusion Passenger](http://www.modrails.com/install.html) site and find the link for the latest source code tarball. Download it using the following instructions (replace the link with the current version):

                  cd /opt
                  wget http://rubyforge.org/frs/download.php/74471/passenger-3.0.5.tar.gz
                  tar xzvf passenger*.gz

Execute the Phusion Passenger installer specifically designed for Nginx:

                   cd passenger*/bin
                   ./passenger-install-nginx-module

Upon initiating the Phusion Passenger nginx installer program, press "Enter" to proceed with the installation. Opt for option "1" when prompted for the Nginx installation method, enabling the installer to automatically download, compile, and install nginx. This method is recommended unless you have specific requirements mandating custom options during nginx compilation. It's crucial not to remove the Passenger files from `opt` after installation as they are essential for proper functionality.

Subsequently, create the file `/etc/init.d/nginx` with the provided contents:

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

Execute the subsequent commands to grant executable permissions to the script and configure it to initiate on boot:

            chmod +x /etc/init.d/nginx
            update-rc.d -f nginx defaults

## Optional: Proxy Redmine with Apache

For those who already have Apache running on their Utho, it's necessary to configure nginx to operate on a distinct port and route requests for your Redmine installation back to Apache. If you're utilizing a different web server, similar adjustments to its configuration are essential. This section is entirely discretionary and pertains exclusively to Apache users.

Execute the following commands to activate proxy support:

                 a2enmod proxy
                 a2enmod proxy_http
                 /etc/init.d/apache2 restart

Set up an Apache virtual host for your Redmine installation, following the example below assuming Apache adheres to our recommended configuration in the [Ubuntu 10.04 LAMP guide](/docs/guides/apache-2-web-server-on-ubuntu-10-04-lts-lucid/). Remember to substitute "12.34.56.78" with your Utho's IP address, `support@example.com` with your administrative email address, and "redmine.example.com" with your Redmine domain.

       {{< file "/etc/apache2/sites-available/redmine.example.com" apache >}}
           <VirtualHost *:80>
           ServerAdmin support@example.com
           ServerName redmine.example.com

           ProxyPass / http://localhost:8080/
           ProxyPassReverse / http://localhost:8080/

           # Uncomment the line below if your site uses SSL.
           #SSLProxyEngine On
           </VirtualHost>

           {{< /file >}}

Execute the subsequent commands to activate the site and reload Apache:

              a2ensite redmine.example.com
             /etc/init.d/apache2 reload

Afterward, configure nginx to operate on a different port by editing your nginx configuration file and adjusting the specified value:

             {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
              listen 8080;

              {{< /file >}}

## Install and Configure Redmine

### Obtain Redmine

Visit the [Redmine project site](http://www.redmine.org/wiki/redmine/Download) to ascertain the latest version number for the stable branch. Execute the subsequent commands to utilize `svn` for code checkout, ensuring to substitute the URL on the final line if required.

                mkdir -p /srv/www/redmine.example.com
                cd /srv/www/redmine.example.com/
                svn co http://redmine.rubyforge.org/svn/branches/1.1-stable redmine-1.1
                mv redmine-1.1 redmine

In the future, to ensure the Redmine directory stays updated, utilize `svn up` while positioned within the redmine directory.

### Create and Configure the Database

Transition to the `postgres` user and initiate the `psql` shell with the subsequent commands:

                        su - postgres
                        psql

Execute these commands within the `psql shell` to configure the database for Redmine. Ensure to specify a robust, unique password instead of "changeme".

             CREATE ROLE redmine LOGIN ENCRYPTED PASSWORD 'changeme' NOINHERIT VALID UNTIL 'infinity';
             CREATE DATABASE redmine WITH ENCODING='UTF8' OWNER=redmine TEMPLATE=template0;
             \q
              exit
             cd redmine

Generate the `config/database.yml` file with the provided content, substituting "changeme" with the password you set in the previous step.

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

Execute the subsequent commands to finalize the database setup:

                chmod 600 config/database.yml
                rake config/initializers/session_store.rb
                RAILS_ENV=production rake db:migrate
                RAILS_ENV=production rake redmine:load_default_data

If an error occurs following the `rake db:migrate` command, open the `config/environment.rb` file. Insert the following excerpt between the bootstrap and initializer sections. Save the file, then rerun the `rake db:migrate` command.

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

Execute the following commands to install `exim4` and configure it for outgoing Internet email delivery. If you already have an SMTP server configured that accepts unauthenticated locally sent mail, you may skip the Exim installation. However, you'll still need to create Redmine's email configuration file as detailed at the end of this section.

                apt-get install exim4
                dpkg-reconfigure exim4-config

Choose "internet site" as the type of mail configuration to use.

Specify your system's fully qualified domain name as the system mail name.

Enter "127.0.0.2" as the IP address to listen on for SMTP connections. This restricts mail delivery to localhost for Redmine purposes.

For the list of recipient domains, input "localhost.localdomain" and your fully qualified domain name.

Leave relay domains and machines blank.

Select "No" when asked about DNS queries.

Regarding maildirs versus mbox format, either option is acceptable. Many modern mail tools prefer Maildirs.

Choose "No" when prompted whether to split the configuration into smaller files.

Provide "root" as the username and an email address from your domain for the postmaster mail query.

Generate the file `config/email.yml` and paste the following contents. Remember to substitute the domain field with your fully qualified domain name.

             {{< file "config/email.yml" yaml >}}
             production:
             delivery_method: :smtp
             smtp_settings:
             address: 127.0.0.2
             port: 25
             domain: redmine.example.com
             authentication: :none

          {{< /file >}}

Email configuration for your Redmine installation is now finalized.

### Final Configuration and Testing

Let's establish a "redmine" user to oversee the installation. Execute the subsequent commands to configure ownership and permissions for your Redmine files, ensuring to assign a robust, unique password for the Redmine user.

              adduser redmine
              cd /srv/www/redmine.example.com/
              chown -R redmine:redmine *
              cd redmine
              chmod -R 755 files log tmp public/plugin_assets

Modify the file `/opt/nginx/conf/nginx.conf`, updating the "user" parameter to "redmine":

            {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
            user redmine;

            {{< /file >}}

Append a server section after the initial example server as shown below. If you're proxying to nginx from another web server, ensure to modify the listen directive to `listen 8080`; instead of the default. Don't forget to substitute "redmine.example.com" with your Redmine site's domain.

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

Your Redmine installation should be accessible at `http://redmine.example.com`. Should you encounter any issues, refer to your log files for a list of potential errors. The default login credentials are username "admin" and password "admin". It's highly recommended to change the admin password promptly. Congratulations! You've successfully installed Redmine for project management on your Utho!

## Monitor for Software Updates and Security Notices

When utilizing software compiled or installed directly from sources provided by upstream developers, it's your responsibility to monitor updates, bug fixes, and security vulnerabilities. Stay informed about releases and potential issues, and promptly update your software to address flaws and mitigate potential system compromises. Consistently monitoring releases and ensuring all software is up to date is essential for maintaining system security and integrity.

Please stay vigilant by monitoring the Redmine project issue queue and news feed. This ensures you're aware of all software updates and can upgrade or apply patches and recompile as necessary.

-   [Redmine News Feed](http://www.redmine.org/projects/redmine/issues)
-   [Redmine Issue Queue](http://www.redmine.org/projects/redmine/news)
