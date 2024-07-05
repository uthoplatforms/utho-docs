---
slug: Using and Customizing Official SaltStack Formulas
description: 'Discover how to effectively utilize and customize official SaltStack formulas for efficient infrastructure management.'
keywords: ['salt', 'formulas', 'git']
published: 2024-06-13
modified: 2024-06-13
modified_by:
  name: Utho
title: "Use and Modify Official SaltStack Formulas"
tags: ["automation","salt"]
authors: ["Pawan Kumar"]
---

## Salt State Files

The SaltStack Platform consists of two core components: a remote execution engine facilitating bi-directional communication across your infrastructure (master and minions), and a configuration management system called the Salt State system. Salt states are defined within Salt State files (SLS) using YAML syntax, specifying how Salt configures minions. A Salt Formula comprises related SLS files aimed at achieving a specific configuration.

On SaltStack's GitHub page, you'll find formulas for common tasks like managing SSL/TLS certificates, configuring the Apache HTTP Server, setting up a WordPress site, and more. These formulas can be easily integrated into your Salt state tree from GitHub.

This guide demonstrates how to fork and customize SaltStack's timezone formula using GitHub, and then utilize it on a Salt master to configure the time zone on multiple minions.

## Before You Begin

If you're new to SaltStack, check out A Beginner's Guide to Salt to get acquainted with fundamental Salt concepts.

Install Git on your local computer by following our How to Install Git on Linux, Mac, or Windows guide.

Get started with Git using our Getting Started with Git guide.

Ensure Git is configured on your local computer.

Use the Getting Started with Salt - Basic Installation and Setup guide to establish a Salt Master and two Salt minions: one on Ubuntu 18.04 and another on CentOS 7.

Follow the sections in our Setting Up and Securing a Compute Instance guide to create a standard user account, enhance SSH security, and eliminate unnecessary network services.

Note: The steps in this guide require root privileges. Please execute the following steps with the `sudo` prefix. For more information on privileges

## Overview of the SaltStack Time Zone Formula

In this section, we'll delve into SaltStack's timezone-formula, designed for configuring a minion's time zone. We'll provide a comprehensive overview of the formula's state files and Jinja templates. Following Salt best practices, formulas should segregate data required by states from the states themselves to enhance flexibility and reusability. We'll explore how this principle is applied in the timezone formula.

Open your browser and go to the timezone-formula on SaltStack's GitHub page. The README file will be displayed, containing essential information about the formula, including:

- Purpose of the formula: To set up the time zone configuration.
- Available state: timezone
- Default values provided: timezone: 'Europe/Berlin' utc: True

The repository's `FORMULA` file contains further information such as supported OS families (Debian, RedHat, SUSE, Arch, FreeBSD), a summary, description, and release number.

Navigate to the timezone-formula repository and open the timezone directory to explore its contents. You'll find the following files:

Examine the contents of the init.sls file defining the timezone state:

    {{< file "timezone/init.sls" >}}

## This state configures the timezone.

    {%- set timezone = salt['pillar.get']('timezone:name', 'Europe/Berlin') %}
    {%- set utc = salt['pillar.get']('timezone:utc', True) %}
    {% from "timezone/map.jinja" import confmap with context %}

     timezone_setting:
      timezone.system:
      - name: {{ timezone }}
      - utc: {{ utc }}

      timezone_packages:
       pkg.installed:
    - name: {{ confmap.pkgname }}

      timezone_symlink:
      file.symlink:
    - name: {{ confmap.path_localtime }}
    - target: {{ confmap.path_zoneinfo }}{{ timezone }}
    - force: true
    - require:
      - pkg: {{ confmap.pkgname }}
    {{</ file >}}

Salt interprets the name of this file as timezone because any init.sls file within a subdirectory is referenced by the directory path.

Within this state file, there are three state declarations: timezone_setting, timezone_packages, and timezone_symlink. Each declaration configures specific settings on a Salt minion as described below.

- timezone.system: This state utilizes Salt's timezone state module to manage the minion's timezone. The name and utc values are obtained from the corresponding Pillar file on the Salt master. These values are set at the beginning of the file using {%- set timezone = salt['pillar.get']('timezone:name', 'Europe/Berlin') %} and {%- set utc = salt['pillar.get']('timezone:utc', True) %}.

- timezone_packages: Ensures that the necessary package for configuring time zones is installed on the minion. The package name is sourced from the confmap variable imported from the map.jinja file. This import is declared at the file's outset with {% from "timezone/map.jinja" import confmap with context %}. You will review the map.jinja file in the upcoming section.

- timezone_symlink: This state creates a symbolic link from the specified name path to the target location. It executes only if the timezone_packages state succeeds, as indicated by the require statement.

Next, examine the contents of the map.jinja file:

      {{< file "timezone/map.jinja" >}}
     {% import_yaml "timezone/defaults.yaml" as defaults %}
     {% import_yaml "timezone/osfamilymap.yaml" as osfamilymap %}

     {% set osfam = salt['grains.filter_by'](
                                        osfamilymap,
                                        grain='os_family'
                                        ) or {} %}

     {% do salt['defaults.merge'](defaults, osfam) %}

     {%- set confmap = salt['pillar.get'](
                                'timezone:lookup',
                                default=defaults,
                                merge=True,
                                ) %}
      {{</ file >}}

The map.jinja file allows the formula to encapsulate static defaults into a dictionary that includes platform-specific data. The main dictionaries are defined in the repository's timezone/defaults.yaml and timezone/osfamilymap.yaml files.

The defaults.yaml file serves as a foundational dictionary containing values applicable to all operating systems, while the osfamilymap.yaml file contains values that may differ from the base dictionary based on OS family. Any file within the formula can access these dictionary values by importing the map.jinja file. Additionally, these dictionary values can be overridden in a Pillar file, a topic covered in the Modify Your SaltStack Formula section.

Review the contents of the timezone/defaults.yaml and timezone/osfamilymap.yaml files to examine the data stored within them.

      {{< file "timezone/defaults.yaml" >}}
    path_localtime: /etc/localtime
    path_zoneinfo: /usr/share/zoneinfo/
    pkgname: tzdata
      {{</ file >}}

      {{< file "timezone/osfamilymap.yaml" >}}
    Suse:
      pkgname: timezone
    FreeBSD:
      pkgname: zoneinfo
    Gentoo:
      pkgname: sys-libs/timezone-data
      {{</ file >}}

The values specified in these YAML files are utilized within the init.sls file.

Open the pillar.example file to examine what it contains:

      {{< file "pillar.example" >}}
     timezone:
     name: 'Europe/Berlin'
     utc: True
     {{</ file >}}

This file serves as a template for creating your own Pillar file on the Salt master. The init.sls file utilizes the name and utc values within its timezone_setting state declaration. The name value sets the time zone for your minion, while the boolean utc determines whether to configure the minion's hardware clock to UTC.

For a comprehensive list of available time zones, refer to the tz database time zones. Due to the sensitive nature of Pillar files, avoid version controlling this file. In the Create the Pillar section, you will generate a Pillar file directly on your Salt master.

Now that you grasp the structure of the SaltStack time zone formula, the next section will guide you through forking the formula's repository on GitHub and cloning the forked formula to your local computer.

## Fork and Clone the SaltStack TimeZone Formula

In this section, you will fork the timezone-formula from the official SaltStack GitHub page to your own GitHub account and clone it to a local repository.

Open your web browser and go to the timezone-formula repository on SaltStack's GitHub page. If you haven't logged into your GitHub account yet, click on the Sign in link at the top of the page and enter your credentials.

Fork the timezone-formula repository from the SaltStack formulas GitHub page by clicking on the Fork button in the top-right corner of the repository page.

After forking the formula, you will be redirected to your own GitHub account's fork of the timezone formula.

Navigate to your forked timezone formula repository. Click on the Clone or download button, then copy the URL:

On your local computer, clone the timezone formula repository:

Move into the `timezone-formula` directory:

        cd timezone-formula

Navigate into the timezone-formula directory:

        ls

You should observe the following output:

      {{< output >}}
      FORMULA        README.rst     pillar.example timezone
      {{</ output >}}

After cloning a repository, Git automatically sets the origin remote to the location of the forked repository. Verify the configured remotes for your timezone-formula repository:

        git remote -v

The output should resemble the following, but it will point to your own fork of the timezone-formula repository:

      {{< output >}}
      origin	https://github.com/my-github/timezone-formula.git (fetch)
      origin	https://github.com/my-github/timezone-formula.git (push)
      {{</ output >}}

You have the option to add the official SaltStack timezone formula as the upstream remote. This allows you to effortlessly pull any updates made by the repository maintainers or contribute to the project. This step is optional.

        git remote add upstream https://github.com/saltstack-formulas/timezone-formula

Now that you have a local copy of your forked timezone-formula, the next step involves modifying the formula to update the init.sls file.

## Modify Your SaltStack Formula

In this section, you will enhance the time zone formula to align with SaltStack's best practices regarding lookup dictionaries. This approach can similarly be applied to modify any SaltStack formula to suit specific infrastructure requirements.

As discussed in the Overview of the SaltStack Time Zone Formula section, the timezone/defaults.yaml and timezone/osfamily.map files contain dictionaries of values used within the init.sls state. These YAML dictionary values can be overridden in a Pillar file, which also handles any sensitive data required by the init.sls state.

According to Salt's official documentation, a recommended practice is to structure formula-related parameters under a second-level lookup key within Pillar data. Currently, the init.sls file's timezone and utc variables are structured differently. You will update these variable statements to expect a second-level lookup key.

Start by creating a new branch in your local repository to initiate modifications to the timezone-formula:

        git checkout -b update-variable-statements

Open the init.sls file in a text editor and adjust the timezone and utc variable declarations to match the provided example file.

      {{< file "timezone/init.sls">}}

## This state configures the timezone.

    {%- set timezone = salt['pillar.get']('timezone:lookup:name', 'Europe/Berlin') %}
    {%- set utc = salt['pillar.get']('timezone:lookup:utc', True) %}
    {% from "timezone/map.jinja" import confmap with context %}

    timezone_setting:
     timezone.system:
    - name: {{ timezone }}
    - utc: {{ utc }}

      timezone_packages:
      pkg.installed:
    - name: {{ confmap.pkgname }}

    timezone_symlink:
    file.symlink:
    - name: {{ confmap.path_localtime }}
    - target: {{ confmap.path_zoneinfo }}{{ timezone }}
    - force: true
    - require:
      - pkg: {{ confmap.pkgname }}
      {{</ file >}}

The init.sls file now anticipates a second-level lookup key for retrieving specified Pillar values. Adhering to this convention simplifies the process of overriding dictionary values in your Pillar file, which you will create in the Installing a Salt Formula section of this guide.

Utilize Git to review which files have been modified before staging them:

        git status

Your output should look like this:

    {{< output >}}
    On branch update-variable-statements
    Changes not staged for commit:
    &nbsp&nbsp(use "git add &ltfile&gt..." to update what will be committed)
    &nbsp&nbsp(use "git checkout -- &ltfile&gt..." to discard changes in working directory)

    &nbsp&nbspmodified:   timezone/init.sls
    no changes added to commit (use "git add" and/or "git commit -a")
    {{</ output >}}

Stage and commit the changes made to the init.sls file.

        git add -A
        git commit -m 'My commit message'

Push your changes to your forked repository:

        git push origin update-variable-statements

Navigate to your timezone formula's remote GitHub repository and create a pull request against your fork's master branch.

Ensure you select your own fork of the timezone formula as the base fork. Otherwise, you might submit a pull request to the official SaltStack timezone formula repository, which is not the intended behavior for this example.

If you are satisfied with the changes in the pull request, merge it into your master branch.

In the next section, you will add your forked timezone-formula to your Salt master, create a Pillar file for the timezone-formula, and apply the changes to your minions.

## Install a Salt Formula

There are two ways to use a Salt Formula: you can add the formula as a GitFS Remote, which allows you to directly serve the files hosted on your GitHub account, or you can clone the formula directly to the Salt master using Git. This section will cover both methods for using Salt formulas.

### Manually Add a Salt Formula to your Master

Navigate to your fork of the timezone-formula, click on the Clone or download button, and copy the repository URL to your clipboard.

SSH into your Salt master. Replace username with your user account and 198.51.102.0 with your Utho's's IP address:

        ssh username@198.51.100.0

Create a directory for formulas and navigate to it:

        mkdir -p /srv/formulas
        cd /srv/formulas

If Git is not already installed on your Salt master, install it using your system's package manager:

    **Ubuntu/Debian**

        apt-get update
        apt-get install git

    **Centos**

        yum update
        yum install git

Clone the repository into the /srv/formulas directory, replacing git-username with your own username:

        git clone https://github.com/git-username/timezone-formula.git

### Add a Salt Formula as a GitFS Remote

GitFS allows Salt to serve files directly from remote Git repositories, providing a convenient way to use Salt formulas while leveraging the flexibility and power of version control systems. This includes collaboration and easy rollback to previous versions of your formulas.

On the Salt master, install the Python interface for Git:

        sudo apt-get install python-git

Edit the Salt master configuration file to use GitFS as a fileserver backend. Ensure the following lines are uncommented in your master configuration file:

        {{< file "/etc/salt/master" >}}
         fileserver_backend:
        - gitfs
        - roots
        {{</ file >}}

When using multiple backends, list all backends in the order you want them to be searched. The roots backend serves files from the master's directories specified in the file_roots configuration.

In the same Salt master configuration file, add the URL of your timezone formula's GitHub repository. Ensure the gitfs_remotes line is uncommented:

      {{< file "/etc/salt/master" >}}
      gitfs_remotes:
      - https://github.com/git-username/timezone-formula.git
      {{</ file >}}

Uncomment the gitfs_provider declaration and set its value to gitpython:

       {{< file "/etc/salt/master" >}}
        gitfs_provider: gitpython
        {{</ file >}}

Restart the Salt master to apply the updated configurations:

        sudo systemctl restart salt-master

### Add a Salt Formula to the Top File

To incorporate your timezone formula into your Salt state tree, you need to add it to your top file.

Create the /srv/salt directory if it doesn't already exist:

              mkdir /srv/salt

Include the timezone state from the timezone-formula in your top file:

           {{< file "/srv/salt/top.sls" >}}
        base:
         '*':
         - timezone
         {{</ file >}}

The example Top file defines one environment, named base, which targets all minions and applies the timezone state to them. This top file can include multiple existing states from your state tree, such as apache, wordpress, and others, across different environments that target various minions. Any Salt formula can seamlessly integrate into the top file and will be applied to the designated minions during the next highstate execution.

### Create the Pillar

To store your formula's Pillar data, create a directory:

        mkdir -p /srv/pillar

Create a Pillar file (timezone.sls) to store data used by your timezone formula:

         {{< file "/srv/pillar/timezone.sls" >}}
          timezone:
         lookup:
         {%- if grains['os_family'] == 'Debian' %}
         name: America/New_York
         {%- else %}
         name: 'Europe/Berlin'
         {%- endif %}
         utc: True
         {{</ file >}}

The timezone.sls Pillar file is derived from the pillar.example file provided in the SaltStack timezone formula. It has been modified to incorporate Jinja control statements that assign a different timezone to any minion running a Debian family OS. You can customize the name values to set your preferred timezone or add additional Jinja logic as needed. For an introduction to Jinja, refer to the Introduction to Jinja Templates for Salt.

You can override dictionary values defined in timezone/defaults.yaml or timezone/osfamilymap.yaml using Salt's lookup dictionary convention. For instance, to override the pkgname value from timezone/defaults.yaml, your Pillar file might resemble the following example:

        {{< file "/srv/pillar/timezone.sls" >}}
         timezone:
          lookup:
          {%- if grains['os_family'] == 'Debian' %}
           name: America/New_York
           {%- else %}
           name: 'Europe/Berlin'
           {%- endif %}
           utc: True
           pkgname: timezone
           {{</ file >}}

If you cloned the timezone-formula directly to your Salt master instead of adding it as a GitFS remote, add the timezone-formula's directory to the Salt master's file_roots configuration:

           {{< file "/etc/salt/master">}}
          file_roots:
               base:
           - /srv/salt/
           - /srv/formulas/timezone-formula
            {{</ file >}}

Incorporate the Pillar into the top file of Pillar:

         {{< file "/srv/pillar/top.sls" >}}
           base:
            '*':
         - timezone
        {{</ file >}}

Configure the location of the Pillar file:

         {{< file "/etc/salt/master">}}
          pillar_roots:  
                  base:
          - /srv/pillar
           {{</ file >}}

Restart the Salt master to activate the new configurations:

           sudo systemctl restart salt-master

Execute a highstate on your minion to enforce the state specified in the timezone formula:

          sudo salt '*' state.apply

## Next Steps

To learn how to create your own Salt formulas and organize your formula's states in a structured and modular manner, refer to our guide on Automating Static Site Deployments with Salt, Git, and Webhooks.
