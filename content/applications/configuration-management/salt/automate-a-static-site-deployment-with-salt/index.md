---
slug: Effortlessly Deploy Your Static Site with Salt Automation
description: "Discover how to leverage Salt to set up a static site webserver and use webhooks for seamless automatic content deployment"
keywords: ['salt','saltstack','github','webhooks','hugo','static site','deployment']
tags: ["web server","automation","salt"]
published: 2024-05-23
modified: 2024-05-23
modified_by:
  name: Utho
title: "Automate Static Site Deployments with Salt, Git, and Webhooks"
title_meta: "Automate Static Site Deployments with Salt and Git"
authors: ["Pawan Kumar"]
---

This guide will demonstrate how to deploy a static site using SaltStack, a flexible configuration management system. The Salt configuration files will be version-controlled with Git. Webhooks will automatically update the production system with changes to your static site's code, providing a seamless deployment process.

Implementing these mechanisms offers numerous benefits:

- Webhooks ensure your production website stays in sync with your development environment without manual intervention.

- SaltStack offers a reliable, extensible method to manage your production systems, reducing the risk of human error.

- Version-controlling your configuration management allows you to track and revert changes, facilitating collaboration on deployments.

## Development and Deployment Workflow

The static site generator featured in this guide is Hugo, a fast framework written in Go. Static site generators convert Markdown or other content files into HTML. This guide can be easily adapted for other frameworks.

Two Git repositories will be set up: one for tracking changes to the Hugo site and another for tracking Salt's configuration files. Remote repositories for both will be created on GitHub.

Two Uthos will be used: one as the Salt master and the other as the Salt minion. This guide was tested on Debian 9, but the instructions may also work on other distributions. The Salt minion will run the production webserver serving the Hugo site, while the master will configure the minion's software. The minion will also run a webhook server to receive code update notifications from GitHub.

Running Salt in masterless mode is possible, but using a Salt master simplifies future deployment expansions.

Note: The workflow described in this guide is similar to how Utho's own Guides & Tutorials website is developed and deployed.

## Before You Begin

### Set Up the Development Environment


Development of your Hugo site and Salt formula will take place on your personal computer. To get started, you’ll need to install some software:

Install Git by following one of the methods in Utho's guide. If you have a Mac, use the Homebrew method, which will also be used to install Hugo.

Install Hugo. Refer to the Hugo documentation for a complete list of installation methods. Instructions for some popular platforms include:

    -   Debian/Ubuntu:

                 sudo apt-get install hugo

    -   Fedora, Red Hat and CentOS:

                sudo dnf install hugo

    -   Mac, using [Homebrew](https://brew.sh):

                brew install hugo

    -   Windows, using [Chocolatey](https://chocolatey.org)

               choco install hugo -confirm

### Deploy the Uthos

Follow the Creating a Compute Instance guide to deploy two Uthos running Debian 9.

In the settings tab of your Uthos' dashboards, label one Utho as salt-master and the other as salt-minion. While not required, this will help you keep track of their roles.

Complete the Setting Up and Securing a Compute Instance guide on each Utho to create a limited Linux user account with sudo privileges, secure SSH access, and remove unnecessary network services.

                   {{< content "limited-user-note-shortguide" >}}

Configure DNS for your site by adding a domain zone and setting up reverse DNS on the IP address of your Salt minion.

## Set Up the Salt Master and Salt Minion

Before setting up the Salt formulas for the minion, you need to install the Salt software on both the master and the minion and establish communication between them.

Log into the Salt master Utho via SSH and run the Salt installation bootstrap script:

                  wget -O bootstrap-salt.sh https://bootstrap.saltproject.io
                  sudo sh bootstrap-salt.sh -M -N

Note: The -M option instructs the script to install the Salt master software, while the -N option ensures the minion software is not installed.

Log into the Salt minion Utho via SSH and set the hostname. For this guide, we'll use hugo-webserver as the example hostname:

                  sudo hostnamectl set-hostname hugo-webserver

Note: Complete this step before installing Salt on the minion, as Salt uses the hostname to generate the minion's Salt ID.

Edit the minion's /etc/hosts file and add a new line for your hostname after the localhost line, replacing 192.0.2.1 with your minion's public IP address:

                   {{< file "/etc/hosts" >}}
                   127.0.0.2       localhost
                   192.0.2.1       hugo-webserver
                   # [...]
                    {{</ file >}}

Run the bootstrap script on the minion:


                 wget -O bootstrap-salt.sh https://bootstrap.saltproject.io
                 sudo sh bootstrap-salt.sh

Edit /etc/salt/minion on the Salt minion. Uncomment the line starting with #master: and input your Salt master's IP after the colon (replace 192.0.2.1):

                  {{< file "/etc/salt/minion" >}}
                  # [...]
                  master: 192.0.2.4
                  # [...]
                   {{</ file >}}

Note: Utho does not charge for traffic within a data center across private IP addresses. If your Salt master and minion are in the same data center and both have private IP addresses, you can use your Salt master's private IP address here to avoid data traffic charges.

Restart Salt on the minion:

                 sudo systemctl restart salt-minion

### Salt Minion Authentication

The minion should now be able to find the master, but it has not yet been authenticated to communicate with the master. Salt uses public-private keypairs to authenticate minions to masters.

On the master, list fingerprints for all the master's local keys, accepted minion keys, and unaccepted keys:

                 sudo salt-key --finger-all

 The output should resemble:

                 {{< output >}}
                 Local Keys:
                 master.pem:  fe:1f:e8:3d:26:83:1c:...
                 master.pub:  2b:93:72:b3:3a:ae:cb:...
                 Unaccepted Keys:
                  hugo-webserver:  29:d8:f3:ed:91:9b:51:...
                   {{< /output >}}

Note: The example fingerprints in this section have been truncated for brevity.

Copy the fingerprint for master.pub from the output of salt-key --finger-all. On your Salt minion, open /etc/salt/minion in a text editor. Uncomment the line starting with #master_finger: and input the value for your master.pub after the colon in single quotes:

                 {{< file "/etc/salt/minion" >}}
                  # [...]
                  master_finger: '0f:d6:5f:5e:f3:4f:d3:...'
                   # [...]
                  {{</ file >}}

Restart Salt on the minion:

                   sudo systemctl restart salt-minion

View the minion's local key fingerprint:

                   sudo salt-call key.finger --local

                      {{< output >}}
                     local:
                     29:d8:f3:ed:91:9b:51:...
                     {{< /output >}}

Compare the output's listed fingerprint to the fingerprints listed by the Salt master for any Unaccepted Keys. This is the output of salt-key --finger-all run on the master at the beginning of this section.

After verifying that the minion's fingerprint matches the fingerprint detected by the Salt master, run the following command on the master to accept the minion's key:

                        sudo salt-key -a hugo-webserver

From the master, verify that the minion is running:

                        sudo salt-run manage.up

You can also run a Salt test ping from the master to the minion:

                        sudo salt 'hugo-webserver' test.ping

                        {{< output >}}
                        hugo-webserver:
                        True
                         {{< /output >}}

## Initialize the Salt Minion's Formula

The Salt minion is now ready to be configured by the master. These configurations will be written in a Salt formula, which will be hosted on GitHub.

On your computer, create a new directory to store your minion's formula and navigate to that directory:

                                   mkdir hugo-webserver-salt-formula
                                   cd hugo-webserver-salt-formula

Within the formula directory, create a new hugo directory to hold your webserver's configuration:

                                   mkdir hugo

Inside the hugo directory, create a new install.sls file:

                      {{< file "hugo-webserver-salt-formula/hugo/install.sls" >}}
                      nginx_pkg:
                      pkg.installed:
                     - name: nginx
                     {{< /file >}}

Note: Salt configurations are written in YAML, a markup language that uses whitespace/indentation. Ensure your indentation matches the snippets in this guide.

A .sls file is a SaLt State file. Salt states describe the desired state of a minion after the state is applied: for example, specifying which software should be installed or which services should be running.

The snippet instructs Salt to install the NGINX web server package (nginx) using the distribution's package manager. Salt can manage software installation across various distributions and package managers, including NPM.

The nginx_pkg string is the ID for the state component, pkg is the name of the Salt module used, and pkg.installed is a function declaration. The component ID is arbitrary and can be named according to your preference.

Note: If you name the ID the same as the installed package, you don't need to specify the - name option, as it will be inferred from the ID. For example, this snippet also installs NGINX:

                    {{< file "hugo-webserver-salt-formula/hugo/install.sls" >}}
                      nginx:
                      pkg.installed
                      {{< /file >}}

Note: The same name/ID convention applies to other Salt modules.

Inside the hugo directory, create a new service.sls file:


                     {{< file "hugo-webserver-salt-formula/hugo/service.sls" >}}
                       nginx_service:
                        service.running:
                       - name: nginx
                       - enable: True
                       - require:
                       - pkg: nginx_pkg
                        {{< /file >}}

This state ensures that the nginx service is immediately started and enabled to run at boot. For a Debian 9 system, Salt configures the appropriate systemd settings to enable the service. Salt also supports other init systems.

The require lines specify that this state component should only be applied after the nginx_pkg component has been applied.

Note: Unless specified by a require declaration, Salt does not guarantee the order in which different components are applied. The order in which components are listed in a state file does not necessarily correspond with the order in which they are applied.

Inside the hugo directory, create a new init.sls file with the following contents:

                          {{< file "hugo-webserver-salt-formula/hugo/init.sls" >}}
                             include:
                           - hugo.install
                           - hugo.service
                             {{< /file >}}

Using the include declaration in this manner concatenates the install.sls and service.sls files into a single combined state file.

Currently, these state files only install and enable NGINX. Additional functionality will be added later in this guide.

The install and service states will not be applied individually to the minion. Instead, only the combined init state will be applied. In Salt, when a file named init.sls exists within a directory, Salt references that state by the name of the directory it belongs to (i.e., hugo in our example).

Note: The organization of the state files used here is not mandated by Salt. Salt does not impose restrictions on how you organize your states. This specific structure is presented as an example of a best practice.

### Push the Salt Formula to GitHub

Inside your hugo-webserver-salt-formula directory on your computer, initialize a new Git repository:

                     cd ~/hugo-webserver-salt-formula
                     git init

Stage the files you just created:

                          git add .

Review the staged files:

                         git status

                      {{< output >}}
                     On branch master
                     No commits yet
                     Changes to be committed:
                     (use "git rm --cached <file>..." to unstage)

                       new file:   hugo/init.sls
                       new file:   hugo/install.sls
                       new file:   hugo/service.sls
                       {{< /output >}}

Commit the files:

                      git commit -m "Initial commit"

Log into the GitHub website in your browser and go to the Create a New Repository page.

Create a new public repository named hugo-webserver-salt-formula:

Copy the HTTPS URL for your new repository:

In your local Salt formula repository, add the GitHub repository as the origin remote and push your new files to it. Replace github-username with your GitHub username:

                        git remote add origin https://github.com/github-username/hugo-webserver-salt-formula.git
                        git push -u origin master

Note: If this is your first time pushing to GitHub from the command line, you may need to authenticate with GitHub. If you have two-factor authentication enabled, you'll need to create and use a personal access token.

If you visit your hugo-webserver-salt-formula repository on GitHub and refresh the page, you should see your new files there.

### Enable GitFS on the Salt Master

Update your Salt master to serve the new formula from GitHub:

Salt requires a Python interface to Git to use GitFS. On the Salt master Utho:

                      sudo apt-get install python-git

Open /etc/salt/master in a text editor. Uncomment the fileserver_backend declaration and add roots and gitfs to the declaration list:

                    {{< file "/etc/salt/master" >}}
                   fileserver_backend:
                  - roots
                   - gitfs
                    {{< /file >}}

roots refers to Salt files stored on the master's filesystem. While the Hugo webserver Salt formula is stored on GitHub, the Salt Top file will be stored on the master. The Top file is how Salt maps states to the minions they will be applied to.

In the same file, uncomment the gitfs_remotes declaration and add your Salt formula's repository URL:

                  {{< file "/etc/salt/master" >}}
                   gitfs_remotes:
                    - https://github.com/your_github_user/hugo-webserver-salt-formula.git
                    {{< /file >}}

                    Uncomment the gitfs_provider declaration in /etc/salt/master and set its value to gitpython.


                     {{< file "/etc/salt/master" >}}
                     gitfs_provider: gitpython
                      {{< /file >}}

### Apply the Formula's State to the Minion

In the same file, locate the file_roots declaration and uncomment it. Set the following values:


            {{< file "/etc/salt/master" >}}
             file_roots:
              base:
              - /srv/salt/
              {{< /file >}}

The file_roots directive defines the location of state files on the master's filesystem. It is referenced when - roots is declared in the fileserver_backend section. The base environment, which is the default environment, contains the tree of state files applicable to minions. While this guide focuses on the base environment, you can create additional environments for purposes like development or QA.

Restart Salt on the master to apply the changes made in /etc/salt/master.

                          sudo systemctl restart salt-master

Create the /srv/salt directory on the Salt master.

                          sudo mkdir /srv/salt

Inside the /srv/salt directory on the Salt master, generate a new file named top.sls:

                 {{< file "/srv/salt/top.sls" >}}
                base:
                 'hugo-webserver':
                  - hugo
                 {{< /file >}}

This file serves as Salt's Top file, outlining which states should be applied to specific minions. In this snippet, it specifies that the init.sls state from the hugo directory (hosted on GitHub as part of your Salt formula) should be sent to the hugo-webserver minion.

Instruct Salt to apply the states specified in the Top file to the minion.

                               sudo salt 'hugo-webserver' state.apply

This command, known as a highstate in Salt, may take some time to execute. The output will detail the actions performed on the minion and any encountered errors. If you encounter an error resembling the following:

If you see an error similar to:

{{< output >}}
No matching sls found for 'hugo' in env 'base'
{{< /output >}}

Try running this command to manually fetch the Salt formula from GitHub, then run the `state.apply` command again:

    sudo salt-run fileserver.update

Salt's GitFS periodically retrieves files from remote repositories, and this frequency can be adjusted as needed. You can configure this update interval using the settings detailed here.

Once this setup is complete, you can check if everything is working by visiting your domain name in a web browser. If everything is configured correctly, you should see NGINX's default test page served by the Salt minion.

## Initialize the Hugo Site

Create a new Hugo site on your computer, ensuring that you're not executing this command within your hugo-webserver-salt-formula directory:

                       hugo new site example-hugo-site

Navigate to the newly created Hugo site directory and initialize a Git repository:

                       cd example-hugo-site
                       git init

Install a theme into the themes/ directory. For instance, let's use the Cactus theme:

                      git submodule add https://github.com/digitalcraftsman/hugo-cactus-theme.git themes/hugo-cactus-theme

As the theme includes example content, copy it into the root of your site for viewing purposes:

                        cp -r themes/hugo-cactus-theme/exampleSite/ .

Modify the config.toml file as follows, updating the baseurl, themesDir, and name options with your own domain and name:

                  {{< file "example-hugo-site/config.toml" >}}
                 # [...]
                   baseURL = "http://example.com"
                 # [...]
                   themesDir = "themes"
                 # [...]
                  name = "Your Name"
                 {{< /file >}}

Start the Hugo development server on your computer:

                   hugo server

Upon running this command, the output will conclude with a line similar to:

                        {{< output >}}
                        Web Server is available at http://localhost:1313/ (bind address 127.0.0.2)
                        {{< /output >}}

If you navigate to the URL provided in the output via a web browser, you'll be able to view your newly created Hugo site.

To stop the Hugo development server, press CTRL-C in the terminal session on your computer. Ensure that the .gitignore file includes public/. Typically, the default .gitignore from the Cactus theme includes:

                      {{< file "example-hugo-site/config.toml" >}}
                      public/
                      themes
                     {{< /file >}}

The public directory is generated by Hugo compiling Markdown content files into HTML. As these files can be regenerated by anyone who downloads your site code, they are excluded from version control.

### Push the Hugo Site to GitHub

Commit the new site files in your Hugo site directory:

                        git add .
                        git commit -m "Initial commit"

Create a new public repository named example-hugo-site on GitHub and copy its HTTPS URL.

In the site directory, set the GitHub repository as the origin remote and push your new files to it; replacing github-username with your GitHub username:

                        git remote add origin https://github.com/github-username/example-hugo-site.git
                        git push -u origin master

## Deploy the Hugo Site

The Salt minion's formula requires updating to serve the Hugo site. Specifically, the formula needs states to:

- Install Git and clone the Hugo site repository from GitHub.

- Install Hugo and generate HTML files from the Markdown content.

- Update the NGINX configuration to serve the built site.

Some of these new state components will reference data stored in Salt Pillar. Pillar is a Salt system designed to store private data and other parameters not intended for inclusion in formulas. This data remains on the Salt master and is not included in version control.

Note: There are methods for securely checking this data into version control or using other backends to host the data, but those strategies are beyond the scope of this guide.

Pillar data is injected into state files using Salt's Jinja templating feature. State files are initially evaluated as Jinja templates and then as YAML afterward.

### Install Git and Hugo

Within your local Salt formula's repository, navigate to the install.sls file and append the git_pkg and hugo_pkg states:

                    {{< file "hugo-webserver-salt-formula/hugo/install.sls" >}}
                     # [...]

                    git_pkg:
                    pkg.installed:
                      - name: git

                       hugo_pkg:
                       pkg.installed:
                      - name: hugo
                     - sources:
                     - hugo: https://github.com/gohugoio/hugo/releases/download/v{{ pillar['hugo_deployment_data']['hugo_version'] }}/hugo_{{ pillar['hugo_deployment_data']['hugo_version'] }}_Linux-64bit.deb
                      {{< /file >}}

The first state installs Git, while the second installs Hugo. Notably, the sources declaration in the second state specifies that Hugo should be downloaded from its GitHub repository instead of relying on the distribution package manager.

The {{ }} syntax within {{ pillar['hugo_deployment_data']['hugo_version'] }} signifies a Jinja substitution statement. This retrieves the value of the hugo_version key from a Pillar dictionary named hugo_deployment_data. Storing the Hugo version in Pillar allows for easy updating without the need to modify formulas.

### Clone the Hugo Site Git Repository

Now, create a new config.sls file within the hugo directory of your local Salt formula repository:

                       {{< file "hugo-webserver-salt-formula/hugo/config.sls" >}}
                      hugo_group:
                    group.present:
                       - name: {{ pillar['hugo_deployment_data']['group'] }}

                    hugo_user:
                  user.present:
                          - name: {{ pillar['hugo_deployment_data']['user'] }}
                           - gid: {{ pillar['hugo_deployment_data']['group'] }}
                       - home: {{ pillar['hugo_deployment_data']['home_dir'] }}
                                                            - createhome: True
                                                                     - require:
                                                             - group: hugo_group

                                                                hugo_site_repo:
                                                                       cmd.run:
                                   - name: git clone --recurse-submodules https://github.com/{{ pillar['hugo_deployment_data']['github_account'] }}/{{ pillar['hugo_deployment_data']['site_repo_name'] }}.git
                                    - cwd: {{ pillar['hugo_deployment_data']['home_dir'] }}
                                   - runas: {{ pillar['hugo_deployment_data']['user'] }}
                                   - creates: {{ pillar['hugo_deployment_data']['home_dir'] }}/{{ pillar['hugo_deployment_data']['site_repo_name'] }}
                                    - require:
                                    - pkg: git_pkg
                                    - user: hugo_user
                                     {{< /file >}}

The final hugo_site_repo component within this snippet handles cloning the example Hugo site repository from GitHub. The cloned repository is placed in the home directory of a system user created in preceding components. Additionally, the clone command recursively downloads the Cactus theme submodule.

Note: The - creates declaration informs Salt that running the cmd command module will result in the creation of the specified file. Upon subsequent applications of the state, Salt checks if the file already exists and refrains from rerunning the module if it does.

The require declarations within each component ensure that:

- The clone operation is delayed until after creating the system user and home directory, as well as installing the Git software package.

- User creation is contingent upon the creation of the group to which it belongs.

Instead of directly specifying parameters for the user, group, home directory, GitHub account, and repository name, these values are fetched from Pillar.

### Configure NGINX

Next, append the following states to your config.sls:

                     {{< file "hugo-webserver-salt-formula/hugo/config.sls" >}}
                          nginx_default:
                            file.absent:
                            - name: '/etc/nginx/sites-enabled/default'
                            - require:
                             - pkg: nginx_pkg

                              nginx_config:
                            file.managed:
                            - name: /etc/nginx/sites-available/hugo_site
                             - source: salt://hugo/files/hugo_site
                             - user: root
                            - group: root
                            - mode: 0644
                          - template: jinja
                          - require:
                        - pkg: nginx_pkg

                         nginx_symlink:
                          file.symlink:
                        - name: /etc/nginx/sites-enabled/hugo_site
                        - target: /etc/nginx/sites-available/hugo_site
                         - user: root
                          - group: root
                          - require:
                        - file: nginx_config

                            nginx_document_root:
                            file.directory:
                      - name: {{ pillar['hugo_deployment_data']['nginx_document_root'] }}/{{ pillar['hugo_deployment_data']['site_repo_name'] }}
                      - user: {{ pillar['hugo_deployment_data']['user'] }}
                      - group: {{ pillar['hugo_deployment_data']['group'] }}
                      - dir_mode: 0755
                      - require:
                      - user: hugo_user
                      {{< /file >}}

nginx_config and nginx_symlink then create a new configuration file in sites-available and a symlink to it in sites-enabled.

The nginx_document_root component establishes the directory from which NGINX will serve your Hugo site files. When filled in with Pillar data, this directory will resemble /var/www/example-hugo-site.

Within nginx_config, notice the - source: salt://hugo/files/hugo_site declaration. This points to an NGINX configuration file not yet present in your repository. Begin by creating the files/ directory:

                         cd ~/hugo-webserver-salt-formula/hugo
                          mkdir files

                            Now, create the hugo_site file within files/:


                             {{< file "hugo-webserver-salt-formula/hugo/files/hugo_site" >}}
                             server {
                              listen 80;
                          listen [::]:80;
                           server_name {{ pillar['hugo_deployment_data']['domain_name'] }};

                         root {{ pillar['hugo_deployment_data']['nginx_document_root'] }}/{{ pillar['hugo_deployment_data']['site_repo_name'] }};

                             index index.html index.htm index.nginx-debian.html;

                              location / {
                              try_files $uri $uri/ = /404.html;
                                }
                                }
                              {{< /file >}}

The nginx_config component that manages this file also specifies the - template: jinja declaration, allowing the source file to be interpreted as a Jinja template. This enables the source file to substitute values from Pillar using Jinja substitution syntax.

Replace the content of your service.sls with this updated snippet:

                        {{< file "hugo-webserver-salt-formula/hugo/service.sls" >}}
                          nginx_service:
                         service.running:
                         - name: nginx
                         - enable: True
                         - require:
                         - file: nginx_symlink
                          - watch:
                         - file: nginx_config
                            {{< /file >}}

The nginx_service component now requires nginx_symlink instead of nginx_pkg. This change prevents the service from being enabled and run before the new NGINX configuration is set up. Additionally, the - watch declaration instructs NGINX to restart whenever a change to nginx_config occurs.

### Build Hugo

Finally, append a build_script state to config.sls:

                          {{< file "hugo-webserver-salt-formula/hugo/config.sls" >}}
                            build_script:
                             file.managed:
                          - name: {{ pillar['hugo_deployment_data']['home_dir'] }}/deploy.sh
                          - source: salt://hugo/files/deploy.sh
                          - user: {{ pillar['hugo_deployment_data']['user'] }}
                           - group: {{ pillar['hugo_deployment_data']['group'] }}
                          - mode: 0755
                          - template: jinja
                          - require:
                          - user: hugo_user
                             cmd.run:
                           - name: ./deploy.sh
                           - cwd: {{ pillar['hugo_deployment_data']['home_dir'] }}
                             - runas: {{ pillar['hugo_deployment_data']['user'] }}
                         - creates: {{ pillar['hugo_deployment_data']['nginx_document_root'] }}/{{ pillar['hugo_deployment_data']['site_repo_name'] }}/index.html
                            - require:
                          - file: build_script
                           - cmd: hugo_site_repo
                            - file: nginx_document_root
                            {{< /file >}}

This state utilizes multiple modules. The first module downloads the deploy.sh file from the Salt master and transfers it to the minion. This script is responsible for compiling your Hugo site files. The second module executes that script. The first module is listed as a requirement of the second module, alongside the Git clone command and the creation of the document root folder.

Note: The - creates option in the second module ensures that Salt doesn't rebuild Hugo if the state is reapplied to the minion.

Create the deploy.sh script within files/:

                   {{< file "hugo-webserver-salt-formula/hugo/files/deploy.sh" >}}

#!/bin/bash

                         cd {{ pillar['hugo_deployment_data']['site_repo_name'] }}
                      hugo --destination={{ pillar['hugo_deployment_data']['nginx_document_root'] }}/{{ pillar['hugo_deployment_data']['site_repo_name'] }}
                      {{< /file >}}

Hugo's build function is invoked with NGINX's document root as the destination for the built files.

Update init.sls to include the new config.sls file:

                    {{< file "hugo-webserver-salt-formula/hugo/init.sls" >}}
                     include:
                    - hugo.install
                    - hugo.config
                    - hugo.service
                     {{< /file >}}

### Push the Salt Formula Updates to GitHub

Your state files should now have these contents: init.sls, install.sls, config.sls, service.sls.

Ensure the following files are present in your Salt formula repository:

{{< output >}}
hugo
├── config.sls
├── files
│   ├── deploy.sh
│   └── hugo_site
├── init.sls
├── install.sls
└── service.sls
{{< /output >}}

Stage all the changes you made to your local Salt formula files in the previous steps and then commit the changes:

                      cd ~/hugo-webserver-salt-formula
                       git add .
                      git commit -m "Deploy the Hugo site"

                      Push the commit to your GitHub repository:

                      git push origin master

### Create the Salt Pillar File

Open /etc/salt/master on the Salt master in a text editor. Uncomment the pillar_roots section:

                  {{< file "/etc/salt/master" >}}
                    pillar_roots:
                      base:
                    - /srv/pillar
                   {{< /file >}}

pillar_roots serves a similar function to file_roots: it specifies where Pillar data is stored on the master's filesystem.

Restart Salt on the master to enable the changes in /etc/salt/master:

                       sudo systemctl restart salt-master

Create the /srv/pillar directory on the Salt master:

                      sudo mkdir /srv/pillar

Create an example-hugo-site.sls file in /srv/pillar to contain the Pillar data for the minion. This file utilizes the same YAML syntax as other state files. Replace the values for github_account and domain_name with your GitHub account and your site's domain name:

                    {{< file "/srv/pillar/example-hugo-site.sls" >}}
                   hugo_deployment_data:
                  hugo_version: 0.49
                 group: hugo
                user: hugo
                  home_dir: /home/hugo
                github_account: your_github_user
                site_repo_name: example-hugo-site
                  nginx_document_root: /var/www
                 domain_name: yourdomain.com
                  {{< /file >}}

Create a top.sls file in /srv/pillar. Similar to the Top file in your state tree, the Pillar's Top file maps Pillar data to minions:

            {{< file "/srv/pillar/top.sls" >}}
              base:
               'hugo-webserver':
                - example-hugo-site
                {{< /file >}}

### Apply State Updates to the Minion

On the Salt master, apply the new states to all minions:

                            sudo salt '*' state.apply

Note: Although this guide assumes only one minion, Salt can utilize shell-style globbing and regular expressions to match against minion IDs when managing multiple minions. For instance, this command would execute a highstate on all minions whose IDs start with hugo:

                   sudo salt 'hugo*' state.apply

If no changes are made, try manually fetching the Salt formula updates from GitHub and then run the state.apply command again:

                     sudo salt-run fileserver.update

When the operation finishes, your Hugo site should now be visible at your domain.

## Deploy Site Updates with Webhooks

Your site is now deployed to production, but there is no automatic mechanism in place yet for updating the production server when you update your Hugo site's content. To update the production server, your minion will need to:

- Pull the latest changes pushed to the master branch of your Hugo site repository on GitHub.

- Run the Hugo build process with the new content.

The deploy.sh script can be altered to pull changes from GitHub. These script changes will be made in the Salt formula repository. Then, we'll set up webhooks to notify the Salt minion that updates have been made to the Hugo site.

Webhooks are HTTP POST requests specifically designed and sent by systems to communicate some kind of significant event. A webhook server listens for these requests and then takes some action when it receives one. For example, a GitHub repository can be configured to send webhook notifications whenever a push is made to the repository. This is the kind of notification we'll configure, and the Salt minion will run a webhook server to receive them. Other event notifications can also be set up on GitHub.

### Set Up a Webhook Server on the Salt Minion

In your local Salt formula repository, append a new webhook_pkg state to your install.sls that installs the webhook server package by adnanh:

                   {{< file "hugo-webserver-salt-formula/hugo/install.sls"  >}}
                    webhook_pkg:
                   pkg.installed:
                - name: webhook
                    {{< /file >}}

Note: The webhook server written in Go by adnanh is a popular implementation of the concept, but it's possible to write other HTTP servers that parse webhook payloads.

Append two new components to your config.sls:

                             {{< file "hugo-webserver-salt-formula/hugo/config.sls"  >}}
                           webhook_systemd_unit:
                            file.managed:
                           - name: '/etc/systemd/system/webhook.service'
                           - source: salt://hugo/files/webhook.service
                           - user: root
                            - group: root
                          - mode: 0644
                           - template: jinja
                                 - require:
                            - pkg: webhook_pkg
                                   module.run:
                             - name: service.systemctl_reload
                             - onchanges:
                            - file: webhook_systemd_unit

                              webhook_config:
                                  file.managed:
                                 - name: '/etc/webhook.conf'
                                - source: salt://hugo/files/webhook.conf
                               - user: root
                                - group: {{ pillar['hugo_deployment_data']['group'] }}
                                - mode: 0640
                                 - template: jinja
                                   - require:
                                 - pkg: webhook_pkg
                                    - group: hugo_group
                                   {{< /file >}}

The first state creates a systemd unit file for the webhook service. The second state creates a webhook configuration. The webhook server reads the configuration and generates a webhook URL from it.

Create a `webhook.service` file in your repository's `files/` directory:

                    {{< file "hugo-webserver-salt-formula/hugo/files/webhook.service"  >}}
                       [Unit]
                      Description=Small server for creating HTTP endpoints (hooks)
                        Documentation=https://github.com/adnanh/webhook/

                          [Service]
                            User={{ pillar['hugo_deployment_data']['user'] }}
                            ExecStart=/usr/bin/webhook -nopanic -hooks /etc/webhook.conf

                                  [Install]
                                WantedBy=multi-user.target
                                 {{< /file >}}

  Create a webhook.service file in your repository's files/ directory:

                        {{< file "hugo-webserver-salt-formula/hugo/files/webhook.conf"  >}}
                            [
                              {
                                "id": "github_push",
                                 "execute-command": "{{ pillar['hugo_deployment_data']['home_dir'] }}/deploy.sh",
                                  "command-working-directory": "{{ pillar['hugo_deployment_data']['home_dir'] }}",
                                   "trigger-rule":
                                    {
                                        "and":
                                          [
                                             {
                                               "match":
                                             {
                                               "type": "payload-hash-sha1",
                                              "secret": "{{ pillar['hugo_deployment_data']['webhook_secret'] }}",
                                                 "parameter":
                                                     {
                                                "source": "header",
                                                "name": "X-Hub-Signature"
                                                 }
                                                  }
                                                  },
                                                 {
                                             "match":
                                              {
                                             "type": "value",
                                              "value": "refs/heads/master",
                                               "parameter":
                                                {
                                                "source": "payload",
                                                "name": "ref"
                                               }
                                              }
                                             }
                                            ]
                                           }
                                          }
                                         ]
                               {{< /file >}}

This configuration sets up a URL named http://example.com:9000/hooks/github_push, where the last component of the URL is derived from the value of the configuration's id.

Note: The webhook server runs on port 9000 and places your webhooks inside a hooks/ directory by default.

When a POST request is sent to the URL:

The webhook server validates whether the header and payload data from the request meet the criteria outlined in the trigger-rule dictionary, which are as follows:

- Verifying that the SHA1 hash of the server's webhook secret matches the secret in the request headers. This ensures that only authorized users who possess the webhook secret can trigger the webhook's action.
- Checking if the ref parameter in the payload matches refs/heads/master, ensuring that only pushes to the master branch trigger the action.
- If these rules are satisfied, the command specified in execute-command is executed, which in this case is the deploy.sh script.

Note: Further details on the webhook configuration options can be found in the project's GitHub repository.

Append a new webhook_service state to your service.sls file to enable and start the webhook server.

                         {{< file "hugo-webserver-salt-formula/hugo/service.sls"  >}}
                         webhook_service:
                         service.running:
                        - name: webhook
                        - enable: True
                         - watch:
                         - file: webhook_config
                        - module: webhook_systemd_unit
                         {{< /file >}}

Update the deploy.sh script to pull changes from the master branch before building the site.

                       {{< file "hugo-webserver-salt-formula/hugo/files/deploy.sh" >}}

#!/bin/bash

                          cd {{ pillar['hugo_deployment_data']['site_repo_name'] }}
                             git pull origin master
                      hugo --destination={{ pillar['hugo_deployment_data']['nginx_document_root'] }}//{{ pillar['hugo_deployment_data']['site_repo_name'] }}
                           {{< /file >}}

Ensure that your state files now contain these contents: init.sls (unchanged), install.sls, config.sls, service.sls. Save the modifications to your Salt files, then commit and push them to GitHub.

                       cd ~/hugo-webserver-salt-formula
                          git add .
                          git commit -m "Webhook server states"
                           git push origin master

On the Salt master, add a webhook_secret to the example-hugo-site.sls Pillar. Generate a complex, random alphanumeric string for the secret.

                       {{< file "/srv/pillar/example-hugo-site.sls" >}}
                        hugo_deployment_data:
                           # [...]
                           webhook_secret: your_webhook_secret
                                    {{</ file >}}

Apply the formula updates to the minion from the Salt master.

                       sudo salt-run fileserver.update
                       sudo salt 'hugo-webserver' state.apply

Verify that your webhook server is operational on the minion by using curl against it.

                       curl http://example.com:9000/hooks/github_push

                   {{< output >}}
                   Hook rules were not satisfied.⏎
                   {{< /output >}}

### Configure a Webhook on GitHub

Navigate to your example Hugo site repository on GitHub, then go to the Settings tab and select the Webhooks section. Click on the Add webhook button.

Fill in the form:

- Enter http://example.com:9000/hooks/github_push for the payload URL (replace example.com with your own domain).

- Choose application/json for the content type.

- Paste the webhook secret that you previously added to Salt Pillar into the appropriate field.

- Ensure that the webhook is configured to notify on push events by keeping this option selected.

Ensure that the webhook is configured to notify on push events by keeping this option selected.

### Update the Hugo Site

Create a new post in your local Hugo site repository using Hugo's archetypes feature.

                            hugo new post/test-post.md

Use the hugo new command to generate a new Markdown document in content/post/. Remove the draft: true line from the frontmatter and add some body text to the file.

                       {{< file "example-hugo-site/content/post/test-post.md" >}}
                         ---
                       title: "Test Post"
                       date: 2018-10-19T11:39:15-04:00
                         ---

                        Test post body text
                         {{< /file >}}

 Running hugo server in the repository directory allows you to preview the new post.

Commit and push the new post to GitHub.

                    cd ~/example-hugo-site
                    git add .
                      git commit -m "Test post"
                       git push origin master

Visit your domain in your browser; your test post should automatically appear.

Note: If your post does not appear, review the Recent Deliveries section at the bottom of your webhook configuration page on GitHub.

Note: Clicking on a delivery provides detailed information about the request headers, payload, and server response, which may aid in troubleshooting. Starting the webhook.service file in verbose mode could be beneficial.

## Next Steps

Extend the setup to host multiple Hugo sites by adding additional GitHub repositories to the Pillar data.

Adapt the Salt formula to support hosting different types of static sites, allowing for a more diverse range of web projects.

Scale your site's infrastructure by provisioning additional minions and applying the same Pillar data and Salt states to them. Implement a NodeBalancer to distribute incoming traffic across these minions effectively.

Utilize Salt's environments feature to establish a dedicated development branch and server, facilitating seamless testing and development workflows.
