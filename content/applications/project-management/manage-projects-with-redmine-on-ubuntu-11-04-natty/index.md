---
slug: "Supercharge Your Project Management: Redmine Mastery on Ubuntu 11.04 'Natty"
description: 'Embark on the journey of setting up and customizing Redmine, a powerful open-source project management solution, on your Ubuntu 11.04 LTS (Natty) Utho'
keywords: ["redmine", "redmine ubuntu 11.04", "redmine linux", "project management software", "redmine postgresql"]
tags: ["ubuntu", "ruby", "nginx", "postgresql"]
modified: 2024-05-13
modified_by:
  name: Utho
published: 2024-05-13
title: 'Manage Projects with Redmine on Ubuntu 11.04 (Natty)'
deprecated: true
relations:
    platform:
        key: manage-projects-with-redmine
        keywords:
            - distribution: Ubuntu 11.04
authors: ["Utho"]
---

Welcome to your comprehensive guide for installing Redmine on your Ubuntu 11.04 (Natty) Utho. We assume you've already completed the steps outlined in our Setting Up and Securing a Compute Instance. Before proceeding, ensure you're logged into your Utho as root via an SSH session. Throughout this guide, we'll use the example domain "example.com". Remember to replace it with your own domain name whenever necessary.

## Set the Hostname

Prior to diving into the installation and configuration steps detailed in this guide, it's essential to verify that you've followed our instructions for [setting your hostname](/docs/products/platform/get-started/#setting-the-hostname). Execute the provided commands to ensure your hostname is properly configured.

                     hostname
                     hostname -f

The first command will display your short hostname, while the second will reveal your fully qualified domain name (FQDN).

## Install Rails Packages and nginx with Phusion Passenger

Execute the subsequent commands to refresh your local package database and install any pending updates.

                 apt-get update
                 apt-get upgrade --show-upgraded

Utilize the following command to install essential packages for Ruby on Rails and associated components.

                 apt-get install wget build-essential ruby1.8 ruby1.8-dev irb1.8 rdoc1.8 zlib1g-dev libpcre3-dev libopenssl-ruby1.8 libzlib-ruby libssl-dev libcurl4-openssl-dev libpq-dev postgresql subversion

Establish symbolic links to the installed Ruby version.

                 ln -s /usr/bin/ruby1.8 /usr/bin/ruby
                 ln -s /usr/bin/irb1.8 /usr/bin/irb

Fetch and install RubyGems version 1.5.3 by executing the provided commands.

                 wget http://rubyforge.org/frs/download.php/74343/rubygems-1.5.3.tgz
                 tar -xf rubygems*tgz
                 cd rubygems*
                 ruby setup.rb
                 ln -s /usr/bin/gem1.8 /usr/bin/gem

Install the necessary gems by following these instructions.         

                  gem install -v=1.1.0 rack
                  gem install fastthread
                  gem install -v=2.3.5 rails
                  gem install -v=0.4.2 i18n
                  gem install postgres
                  gem install pg

Navigate to the [Phusion Passenger](http://www.modrails.com/install.html) site and find the link for the current source code tarball. Download it using the instructions provided (remember to substitute the link with the current version).

                cd /opt
                wget http://rubyforge.org/frs/download.php/74471/passenger-3.0.5.tar.gz
                tar xzvf passenger*.gz

Execute the Phusion Passenger installer for Nginx.

                cd passenger*/bin
                ./passenger-install-nginx-module

Upon running the Phusion Passenger nginx installer program, press "Enter" to proceed with the installation. When prompted for the Nginx installation method, we recommend selecting "1" to allow the installer to automatically download, compile, and install nginx. This is the safest approach unless you have specific requirements necessitating custom options. It's crucial not to remove the Passenger files from `opt` after installation as they're necessary for proper functionality.

Create the file `/etc/init.d/nginx` with the specified contents.

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

Issue the provided commands to make the script executable and configure it to start on boot.

             chmod +x /etc/init.d/nginx
             update-rc.d -f nginx defaults

## Optional: Proxy Redmine with Apache

For those already running Apache on their Utho, adjust nginx to operate on a different port and proxy requests for the Redmine installation back to Apache. Similar adjustments may be necessary for other web servers. This section is optional and relevant only to Apache users.

Enable proxy support by executing the given commands.

                a2enmod proxy
                a2enmod proxy_http
                /etc/init.d/apache2 restart

Configure an Apache virtual host for your Redmine installation, ensuring to replace placeholders such as "12.34.56.77" with your Utho's IP address, `support@example.com` with your administrative email address, and "redmine.example.com" with your Redmine domain.

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

Enable the site and reload Apache with the provided commands.   

             a2ensite redmine.example.com
            /etc/init.d/apache2 reload

To instruct nginx to operate on a different port, modify your nginx configuration file and adjust the specified value accordingly.

            {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
            listen 8080;

{{< /file >}}

## Install and Configure Redmine

### Obtain Redmine

Visit the [Redmine project site](http://www.redmine.org/wiki/redmine/Download) to determine the current version number for the stable branch. Then, utilize the provided commands to employ `svn` for checking out the code. If necessary, update the URL on the last line.

              mkdir -p /srv/www/redmine.example.com
              cd /srv/www/redmine.example.com/
              svn co http://redmine.rubyforge.org/svn/branches/1.1-stable redmine-1.1
              mv redmine-1.1 redmine

To maintain the redmine directory up to date in the future, utilize `svn up` from within the redmine directory.

### Create and Configure the Database

Switch to the `postgres` user and initiate the `psql` shell by executing the provided commands.

                su - postgres
                psql

Within the `psql` shell, execute the provided commands to establish the database for Redmine. Ensure to replace "changeme" with a strong, unique password.

             CREATE ROLE redmine LOGIN ENCRYPTED PASSWORD 'changeme' NOINHERIT VALID UNTIL 'infinity';
             CREATE DATABASE redmine WITH ENCODING='UTF8' OWNER=redmine TEMPLATE=template0;
             \q
             exit
            cd redmine

Create the file `config/database.yml` with the provided contents, substituting "changeme" with the password assigned in the previous step.

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

Complete the database configuration by executing the provided commands.

             chmod 600 config/database.yml
             rake config/initializers/session_store.rb
             RAILS_ENV=production rake db:migrate
             RAILS_ENV=production rake redmine:load_default_data

If you encounter an error message following the `rake db:migrate` command, modify the `config/environment.rb` file as instructed. After editing, retry the rake db:migrate command.


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

Execute the specified commands to install `exim4` and configure it for outgoing Internet email delivery. You may skip Exim installation if you already have an SMTP server configured to accept unauthenticated locally sent mail. However, you'll still need to create Redmine's email configuration file as outlined at the end of this section.

             apt-get install exim4
             dpkg-reconfigure exim4-config

Choose "internet site" as the type of mail configuration to use.

Input your system's fully qualified domain name as the system mail name.

Specify "127.0.0.2" for the IP address to listen on for SMTP connections. This restricts mail delivery to localhost for Redmine's use.

When prompted for the list of recipient domains, provide "localhost.localdomain" and your fully qualified domain name.

Leave relay domains and machines blank.

Choose "No" for DNS queries.

Regarding maildirs versus mbox format, you have the flexibility to select either. However, maildirs are increasingly favored by many modern mail tools.

Opt for "No" if asked whether to split the configuration into smaller files.

For the postmaster mail query, input "root" as the username and an email address from your domain.

Create the file `config/email.yml` and paste the provided contents. Ensure to replace the domain field with your fully qualified domain name.

            {{< file "config/email.yml" yaml >}}
             production:
             delivery_method: :smtp
             smtp_settings:
             address: 127.0.0.1
              port: 25
             domain: redmine.example.com
             authentication: :none

             {{< /file >}}

This concludes the email configuration process for your Redmine installation.

### Final Configuration and Testing

We'll establish a "redmine" user to oversee the installation. Execute the provided commands to set ownership and permissions on your Redmine files, ensuring to assign a strong, unique password for the Redmine user.

              adduser redmine
              cd /srv/www/redmine.example.com/
              chown -R redmine:redmine *
              cd redmine
               chmod -R 755 files log tmp public/plugin_assets

Modify the file `/opt/nginx/conf/nginx.conf`, updating the "user" parameter to "redmine".

             {{< file "/opt/nginx/conf/nginx.conf" nginx >}}
             user redmine;

               {{< /file >}}

 Add a server section after the initial example server. If proxying to nginx from another web server, adjust the `listen` directive to `listen 8080`; instead of the default. Remember to replace "redmine.example.com" with your Redmine site's domain.

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

Your Redmine project management system is now accessible at http://redmine.example.com. In case of any issues, consult your log files for a detailed listing of errors. The default login credentials are username "admin" and password "admin". It's highly recommended to change the admin password immediately. Congratulations on successfully installing Redmine on your Utho!

## Monitor for Software Updates and Security Notices

When utilizing software directly from upstream developers, it's your responsibility to stay vigilant and monitor updates, bug fixes, and security issues. Promptly updating your software upon release of patches or fixes is essential to prevent potential system compromises. Maintaining up-to-date versions of all software is paramount for ensuring system security and integrity.

Stay informed about updates to the Redmine software by monitoring the project issue queue and news feed. This ensures you're aware of all updates and can upgrade or apply patches as necessary to maintain the security and functionality of your Redmine installation.

-   [Redmine News Feed](http://www.redmine.org/projects/redmine/issues)
-   [Redmine Issue Queue](http://www.redmine.org/projects/redmine/news)
