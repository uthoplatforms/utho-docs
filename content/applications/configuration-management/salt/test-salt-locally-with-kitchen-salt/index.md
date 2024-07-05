---
slug: Whip Up Your Salt Tests Locally with Kitchen Salt
description: 'This guide offers step-by-step instructions for locally testing Salt states using Kitchen and kitchen-salt. These utilities enable testing without the need for a Salt master or minions'
keywords: ['saltstack','salt','kitchen','kitchen-salt','kitchensalt','salt solo','saltsolo']
published: 2024-04-02
modified: 2024-04-02
modified_by:
  name: Utho
title: "Test Salt States Locally with KitchenSalt"
tags: ["automation","salt"]
authors: ["Utho"]
---
KitchenSalt enables you to employ Test Kitchen for local testing of your Salt configurations without the need for a Salt master or minions. This guide walks you through the installation of KitchenSalt and demonstrates how to use Docker to test a Salt state. The instructions provided here are based on a system running Ubuntu 18.04.

## Before You Begin

- Before proceeding, ensure that you have root access to your computer or a user account with `sudo` privileges. For more details on privileges, refer to our [Users and Groups](/docs/guides/linux-users-and-groups/) guide.
- Ensure that Git is installed on your local computer. If not, you can [Install Git](/docs/guides/how-to-install-git-on-linux-mac-and-windows/) following the provided instructions.
- Lastly, update your system packages to ensure you have the latest updates installed.

## Install rbenv and Ruby

Kitchen relies on Ruby. Follow these commands to install the Ruby version controller rbenv, set rbenv in your PATH, and install Ruby via rbenv:

1. Install the necessary packages for rbenv:

        sudo apt install libssl-dev libreadline-dev zlib1g-dev bzip2 gcc make git ruby-dev

2. Clone the rbenv git repository and set up your PATH:

        sudo git clone git://github.com/rbenv/rbenv.git /usr/local/rbenv
        sudo mkdir /usr/local/rbenv/plugins
        sudo git clone git://github.com/rbenv/ruby-build.git /usr/local/rbenv/plugins/ruby-build
        sudo tee /etc/profile.d/rbenv.sh <<< 'export PATH="/usr/local/rbenv/plugins/ruby-build/bin:/usr/local/rbenv/bin:$PATH"'
        sudo tee -a /etc/profile.d/rbenv.sh <<< 'source <(rbenv init -)'

3.  Reload your system's profile so that the rbenv commands are added to your `PATH`:

        source /etc/profile

Optionally, restart your shell session for the `PATH` changes to take effect.

4.  Install Ruby:

        rbenv install 2.5.1

## Install Docker

    {{< content "installing-docker-shortguide" >}}

## Install KitchenSalt

1. Install the bundler gem:

        sudo gem install bundler

2.  Create a Gemfile in your working directory and include the following `gems: kitchen-salt`, `kitchen-docker`, and `kitchen-sync`.
        {{< file "Gemfile" ruby>}}
#Gemfile
        source 'https://rubygems.org'

        gem 'kitchen-salt'
        gem 'kitchen-docker'
        gem 'kitchen-sync'
        {{< /file >}}

`kitchen-sync` facilitates faster copying of files to Docker containers.

1.  Install the gems with bundler:

        sudo bundle install

## Create a Sample .sls File

For testing purposes, create a Salt state file named `nginx.sls` in your working directory using a text editor. Add the following lines to the file:

    {{< file "nginx.sls" yaml >}}
    nginx:
    pkg:
    - installed
    service.running:
    - enable: True
    - reload: True
    - watch:
    - pkg: nginx
    {{< /file >}}

## Configure kitchen.yml

1. To set up the Kitchen configuration file, start by creating a `kitchen.yml` file in your working directory. Then, paste the following lines into the file:

        {{< file "kitchen.yml" yaml >}}
        provisioner:
        name: salt_solo
        salt_install: bootstrap
        is_file_root: true
        require_chef: false
        state_top:
        base:
        "*":
        - nginx

        ...
        {{< /file >}}

In this configuration:

- The provisioner section specifies `salt_solo` as the provisioner, enabling Kitchen to utilize Salt without a Salt master.
- Salt is installed using the bootstrap script (`salt_install: bootstrap`).
- The Salt file root is set to the directory containing `.kitchen.yml` (`is_file_root: true`).
- Chef is disabled (`require_chef: false`).
- Inline declaration of the top file for Salt states is provided.
- Pillar files for Salt are added under the provisioner block, where nginx is specified with `app_name: my_nginx_app` as an example.
- The configuration is set up to use the Docker driver with the Ubuntu 18.04 platform.
- The suite named `default` is configured to use the specified provisioner and verifier.

        {{< file "kitchen.yml" yaml >}}
        provisioner:
        ...
        pillars:
        top.sls:
        base:
        "*":
        - nginx_pillar
        pillars_from_files:
        nginx_pillar.sls: nginx.pillar
        {{< /file >}}

1. Next, let's configure the **driver** section:

        {{< file "kitchen.yml" yaml >}}
        ...

        driver:
        name: docker
        user_sudo: false
        privileged: true
        forward:
        - 80

        ...
        {{< /file >}}

This section sets up Docker as the driver for Kitchen. It specifies that Kitchen won't require `sudo` `privileged` to build Docker containers (`user_sudo` is set to false). Additionally, it ensures that the containers can run systemd by setting privileged to `true`. The configuration also `forward` traffic from port `80` on the Docker container to the host machine.

1.  Configure the **platforms** section:

        {{< file "kitchen.yml" yaml >}}
        ...

        platforms:
        - name: ubuntu
        driver_config:
        run_command: /lib/systemd/systemd

        ...
        {{< /file >}}

This section specifies the platform configurations for Docker to run. By default, Docker runs the latest version of each platform. Since different platforms may place systemd in different locations, the `driver_config` section is utilized to specify the systemd install path for each platform. You can define multiple platforms in this section.

1. Configure the **suites** section:

        {{< file "kitchen.yml" yaml >}}
        ...

        suites:
        - name: oxygen
        provisioner:
        salt_bootstrap_options: -X -p git stable 2018.3

        ...
        {{< /file >}}

The `suites` section specifies the software suite that Kitchen will test against. In this case, Kitchen will test against the Oxygen release of Salt. You can define multiple suites in this section.

1.  Finally, the **transport** section allows us to specify the use of `kitchen-sync` for transferring files.

        {{< file "kitchen.yml" yaml >}}
        ...

        transport:
        name: sftp
        {{< /file >}}

1.  You can now test your Salt configuration with Kitchen. Run the following command:

        kitchen test

This command will create, converge, and then destroy the test instance. If everything goes well, the final terminal output will confirm the success.

    {{< output >}}
    -----> Kitchen is finished. (13m32.13s)
    {{< /output >}}

For a more detailed approach to running your test, you can use the individual commands in series:

        kitchen list
        kitchen create
        kitchen converge
        kitchen destroy

## Using a Verifier and Next Steps
While this article focuses on testing Salt configurations with Kitchen, it's worth noting that Kitchen offers more comprehensive testing options beyond Salt configuration checks. You can write tests in various languages:

Bash using Bats
Ruby using Minitest, RSpec, Serverspec, and Inspec
Python using pytest
For example, to verify tests using the Inspec gem, you can add the following code to your `kitchen.yml`:

    {{< file "kitchen.yml" yaml >}}
    ...

    verifier:
    name: inspec
    {{< /file >}}
