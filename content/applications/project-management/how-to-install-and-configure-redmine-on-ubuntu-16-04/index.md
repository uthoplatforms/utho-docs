---
slug: "Unleash Productivity: Easy Redmine Setup Guide for Ubuntu 16.04!"
description: '"Unlock the Power of Project Management: Learn to Install and Configure Redmine, the Leading Open-Source Ruby on Rails Software Suite'
keywords: ["nginx", "ubuntu", "redmine"]
tags: ["ubuntu", "nginx", "ruby"]
published: 2024-05-10
modified: 2024-05-10
modified_by:
  name: Utho
title: 'How to Install and Configure Redmine on Ubuntu 16.04'
authors: ["Pawan Kumar"]
---

## What is Redmine?

Experience Project Management Freedom: Dive into Redmine, the Versatile Open-Source Web App! Discover its Flexible Project Management Features, Tracking Tools, and Extensive Plugin Library.

This guide walks you through installing and setting up Redmine on Ubuntu 16.04, leveraging the power of the Passenger application server alongside NGINX.

### Before You Begin

Note: Root privileges are required for the steps below. Ensure you run them as root or with the sudo prefix. For further details on privileges, refer to our [Users and Groups](/docs/guides/linux-users-and-groups/)

## Install Dependencies

      sudo apt install build-essential mysql-server ruby ruby-dev libmysqlclient-dev imagemagick libmagickwand-dev

## Configure MySQL

"Redmine relies on MySQL for data storage. Access your database's root account using the password set during `mysql-server` installation."

             mysql -u root -p

Once logged in, create a new database and user for Redmine."


              {{< highlight sql >}}
              CREATE DATABASE redmine;
              CREATE USER 'redmine'@'localhost' IDENTIFIED BY 'password';
              GRANT ALL PRIVILEGES ON redmine.* TO 'redmine'@'localhost';
              FLUSH PRIVILEGES;
              quit;
              {{< /highlight >}}

### Install Ruby

Redmine operates on Ruby. Utilize the Ruby Version Manager (RVM) to install Ruby 2.2.3."

Fetch the latest version of RVM via curl."

        gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
        curl -sSL https://get.rvm.io | bash -s stable
        source ~/.rvm/scripts/rvm

    <!---
            sudo apt-add-repository -y ppa:rael-gc/rvm
            sudo apt-get update
            sudo apt-get install rvm
    -->

Users of RVM must belong to the `rvm` group. Establish this group, add a user, then log out and log back in."

        sudo groupadd rvm
        sudo usermod -a -G rvm username
        exit

Confirm installation requirements and install Ruby (version 2.2.3)."

        rvm requirements
        rvm install 2.2.3
        rvm use 2.2.3 --default


### Install Passenger and NGINX

[Passenger](https://github.com/phusion/passenger) serves as an application server, orchestrating web application execution and communication with the web server. The project offers comprehensive [documentation](https://www.phusionpassenger.com/library/install/nginx/install/oss/xenial/) on installing Passenger and NGINX on Ubuntu 16.04 through an apt repository."

Import the Passenger PGP key and enable HTTPS support for the package manager."

        sudo apt install -y dirmngr gnupg
        sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 561F9B9CAC40B2F7
        sudo apt install -y apt-transport-https ca-certificates

Add the Passenger APT repository:

        sudo sh -c 'echo deb https://oss-binaries.phusionpassenger.com/apt/passenger xenial main > /etc/apt/sources.list.d/passenger.list'
        sudo apt update

Install Passenger and NGINX

        sudo apt install -y nginx-extras passenger

### Configure NGINX

After installing Passenger, NGINX with Passenger integration is ready. Configure NGINX to ensure proper utilization of Passenger."

Uncomment the include `/etc/nginx/passenger.conf`; line in `/etc/nginx/nginx.conf` and tailor your config file accordingly."

                      {{< file "/etc/nginx/nginx.conf" aconf >}}

### Phusion Passenger config

### Uncomment it if you installed passenger or passenger-enterprise
##

                      include /etc/nginx/passenger.conf;

### Virtual Host Configs

                     include /etc/nginx/conf.d/*.conf;

                     {{< /file >}}

Duplicate the default NGINX site configuration file. The designated working configuration file in this guide is `/etc/nginx/sites-available/default`.

              cp /etc/nginx/sites-available/default /etc/nginx/sites-available/default.orig

Modify the `root` directory for the website and integrate additional Passenger configurations. Within the `server{}` block of the file, insert the following lines:

                 {{< file "/etc/nginx/sites-available/default" aconf >}}
                  root /data/redmine/redmine/public;
                  passenger_enabled on;
                  client_max_body_size 10m;

                  {{< /file >}}

Within the same file, deactivate the `#location` section by commenting it out.

                       {{< file "/etc/ningx/site-available/default" aconf >}}

                        #location / {

#### First attempt to serve request as file, then

#### as directory, then fall back to displaying a 404.

                       #try_files $uri $uri/ =404;
                       #}

                      {{< /file >}}

Adjust the permissions for `/var/www`.

                     sudo mkdir /var/www
                     sudo chown -R www-data /var/www

Restart the `nginx` service

                    sudo systemctl restart nginx

Confirm the successful installation of Passenger and NGINX:

                    sudo /usr/bin/passenger-config validate-install

Press **Enter** when prompted after selecting the first option.

                    {{< output >}}
                    If the menu doesn't display correctly, press '!'     

                     ⬢  Passenger itself
                     ⬡  Apache

  -------------------------------------------------------------------------

             * Checking whether this Passenger install is in PATH... ✓
             * Checking whether there are no other Passenger installations... ✓
             Everything looks good. :-()
             {{< /output >}}

Verify if NGINX has initiated the Passenger core process:

             sudo /usr/sbin/passenger-memory-stats

Upon correct installation, the output should resemble:

                 {{< output >}}
                      --------- NGINX processes ----------
                      PID   PPID  VMSize    Private  Name
                      ------------------------------------
                      6399  1     174.9 MB  0.6 MB   nginx: master process /usr/sbin/nginx -g daemon on; master_process on;
                      6404  6399  174.9 MB  0.7 MB   nginx: worker process

### Processes: 2

#### Total private dirty RSS: 1.23 MB

                      ---- Passenger processes -----
                      PID   VMSize    Private  Name
                      ------------------------------
                      6379  441.3 MB  1.2 MB   Passenger watchdog
                      6382  660.4 MB  2.9 MB   Passenger core
                      6388  449.5 MB  1.4 MB   Passenger ust-router

### Processes: 3
                     {{< /output >}}

### Install Redmine

Create a new user named `redmine` and include them in the `sudo` group.

                     sudo adduser --system --shell /bin/bash --gecos 'Redmine Administrator' --group --home /data/redmine redmine; sudo usermod -a -G rvm redmine
                     sudo adduser redmine sudo

Log in as the `redmine` user:

                        su -
                        passwd redmine
                        su redmine
                        cd

Download the Redmine tarball as the new user, extract it, and rename the directory to `redmine` for convenience.

                     wget https://www.redmine.org/releases/redmine-3.4.4.tar.gz
                     tar -zxvf redmine-3.4.4.tar.gz
                     mv redmine-3.4.4 redmine

Incorporate the previously created database information into Redmine's configuration file. Only complete the "Production" section, disregarding the development or test environments.

                    cd redmine
                    cp -pR config/database.yml.example config/database.yml
                    emacs config/database.yml

Inside the `redmine` directory, install the necessary Ruby dependencies.

                          sudo gem install bundler
                          sudo bundle install --without development test

Upon completion of the installation, initiate the server using Rake.

                     bundle exec rake generate_secret_token
                     RAILS_ENV=production bundle exec rake db:migrate
                     RAILS_ENV=production bundle exec rake redmine:load_default_data

Restart NGINX, then access your server's IP address to access the Redmine application.

                    sudo systemctl restart nginx

## Redmine

The default login credentials for Redmine are:

                       Login: admin
                       Password: admin

Upon initial login, you'll be prompted to update your credentials for security reasons.

#### Install a Plug-in
Redmine is designed to accommodate plugins. These are installed in `redmine/plugins`. Below illustrates the installation process using [scrum2b](https://github.com/scrum2b/scrum2b), a plugin tailored for managing Scrum/Agile workflows.

If not already installed, acquire git or download the plugin directly from the Github repository.

                      sudo apt install git

Navigate to `redmine/plugins` and clone the desired plugin:

                              cd plugins
                              git clone https://github.com/scrum2b/scrum2b

Utilize Bundle to install the plugin, followed by restarting NGINX:

                              bundle install
                              sudo systemctl restart nginx

Access Redmine in your web browser. After logging in, navigate to **admin** and then click **plugins**.

Congratulations! You've successfully set up Redmine on your utho. If you intend to use it in production environment, consider exploring plugins that align with your team's needs. Refer to the guides below to customize Redmine for optimal team collaboration.
