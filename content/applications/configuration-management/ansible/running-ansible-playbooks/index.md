---
slug: Running-ansible-playbooks-guide
description: 'An overview of configuration management using the Ansible IT automation platform, covering installation, configuration, and playbook setup.'
keywords: ["ansible", "ansible configuration", "ansible provisioning", "ansible infrastructure", "ansible automation", "ansible configuration", "ansible configuration change management", "ansible server automation"]
published: 2024-05-16
modified: 2024-05-16
modified_by:
    name: Utho
title: Automate Server Configuration with Ansible Playbooks
tags: ["automation"]
authors: ["Pawan Kumar"]
---

**Playbooks** delineate a series of tasks to be executed by Ansible across a group of managed nodes. Unlike executing individual tasks via the command line, Playbooks offer reusability, facilitate collaboration among teams, enable version control, and accommodate intricate deployment and rollout needs. Leveraging features such as handlers, variables, templates, error handling, and control logic within Playbooks enhances the automation of IT processes across multiple hosts.

## Scope of this Guide

This guide serves as an introduction to Ansible Playbook concepts, encompassing tasks, plays, variables, and Jinja templating. Through illustrative examples, you'll craft Playbooks to automate tasks like:

- Creating a restricted user account on a utho.
- Executing common server setup operations, such as configuring a hostname, timezone, and updating system software.
- Installing a LAMP (Linux, Apache, MySQL, PHP) stack.

## Before You Begin

If you're new to Ansible, familiarize yourself with the Ansible Definitions section of the Getting Started With Ansible guide.

Install Ansible on your local machine or a utho by following the steps outlined in the Set up the Control Node section of our Getting Started With Ansible guide.

Deploy a utho running Debian 9 to be managed with Ansible. Throughout this guide, all Playbooks will be executed on this utho. Refer to the Getting Started With Ansible - Basic Installation and Setup for instructions on establishing a connection between the Ansible control node and your utho.

Note: When following the Getting Started with Ansible guide to deploy a utho, there's no need to add your Ansible control node's SSH key-pair to your managed utho. This step will be completed using a Playbook later in this guide.

## Playbook Basics

Ansible Playbooks utilize YAML syntax, a declarative language, to outline tasks or actions to be executed on a group of managed nodes. Tasks within a Playbook are executed sequentially, and Playbooks should ideally be designed to be idempotent, ensuring consistent results regardless of the number of times they are run. For instance, a Playbook might include a task to configure a server file using a template and inject variable values into it. Ansible will compare the template configuration file with the actual file on the server and create or update it only if necessary.

### Anatomy of a Playbook

Below is the basic structure of a Playbook. At its core, a Playbook defines a group of target hosts, variables specific to the Playbook, the remote user to execute tasks as, and a series of named tasks utilizing various [Ansible modules](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html). These grouped tasks are referred to as a **play**, and a single Playbook can contain multiple plays.

Note:

| **Module** | **Usage** |
| ---------------- | ------------- |
| [command](http://docs.ansible.com/ansible/command_module.html) | Executes a command on a remote node. |
| [script](http://docs.ansible.com/ansible/script_module.html) | Transfers a local script to a managed node and then runs the script on the remote node. |
| [shell](http://docs.ansible.com/ansible/command_module.html) | Executes a command through a shell (`/bin/sh`) on a remote node. |
| [template](http://docs.ansible.com/ansible/template_module.html) | Uses a local file template to create a file on a remote node. |
| [apt](http://docs.ansible.com/ansible/apt_module.html) | Manages apt packages on Debian or Ubuntu systems. |
| [git](http://docs.ansible.com/ansible/apt_module.html) | Deploy software or files from git checkouts. |
| [service](http://docs.ansible.com/ansible/apt_module.html) | Manage services on your remote node's system. Supports BSD init, OpenRC, SysV, Solaris SMF, systemd, upstart init systems.  |

    {{< file "Playbook Skeleton" yaml >}}
---
     - hosts: [target hosts]
     vars:
     var1: [value 1]
     var2: [value 2]
     remote_user: [yourname]
     tasks:
    - name: [task 1]
      module:
    - name: [task 2]
      module:

      {{< /file >}}

The following example Playbook targets all hosts in the `marketing_servers` group and ensures Apache is started. The task is executed as the webadmin user.

    {{< file "Service Check Playbook" yaml >}}
---
      - hosts: [marketing_servers]
       remote_user: webadmin
        tasks:
       - name: Ensure the Apache daemon has started
       service: name=httpd state=started
       become: yes
       become_method: sudo

       {{< /file >}}

## Web Server Setup with Ansible Playbooks

In this section, you'll create three distinct Playbooks to configure your utho as a web server running a LAMP stack, along with adding a limited user account. These Playbooks provide basic configurations that can be expanded upon if needed.

Note: The Playbooks created here are primarily for educational purposes and do not result in a fully secured server. For enhanced security, consider utilizing Ansible's [firewalld module](https://docs.ansible.com/ansible/latest/modules/firewalld_module.html).

### Add a Limited User Account

This section covers creating a Playbook to add a limited user account to your utho.

#### Create a Password Hash

When creating a limited user account, you'll need to generate a hashed password for the new user. To maintain security, plaintext passwords should not be included in Playbooks. Here, you'll use Python's PassLib library to create a password hash securely.

Note: While [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html#encrypt-string-for-use-in-yaml) can also encrypt sensitive data, this guide doesn't utilize it.

On your Ansible control node, generate a password hash using Python's PassLib library for later use. Install PassLib with the following commands if not already installed:

Install pip, the Python package installer, if not already installed:

         sudo apt install python-pip

Install the passlib library:

        sudo pip install passlib

Generate a password hash using passlib. Replace `myPlainTextPassword` with your desired password for utho access.

         sudo python -c "from passlib.hash import sha512_crypt; print (sha512_crypt.hash('myPlainTextPassword'))"

You'll receive a similar output displaying the hash of your password.

    {{< output >}}
    $6$rounds=656000$dwgOSA/I9yQVHIjJ$rSk8VmlZSlzig7tEwIN/tkT1rqyLQp/S/cD08dlbYctPjdC9ioSp1ykFtSKgLmAnzWVM9T3dTinrz5IeH41/K1
    {{</ output >}}

Copy and store the generated hash for later use.

#### Disable Host Key Checking

Ansible relies on the sshpass helper program for SSH authentication. Ensure that host key checking is disabled on your Ansible control node for sshpass to function properly.

Disable host key checking. Open the `/etc/ansible/ansible.cfg` configuration file in a text editor, uncomment the following line, and save the changes.

        {{< file "/etc/ansible/ansible.cfg" ini >}}
         #host_key_checking = False

          {{< /file >}}

#### Create the Inventory File

To target your utho in a Playbook, you'll need to include it in your Ansible control node's inventory file.

Edit your inventory file to create the `webserver` group and add your utho to it. Open the `/etc/ansible/hosts` file in your preferred text editor and append the following information. Replace 192.0.2.1 with your utho's IP address.

        {{< file "/etc/ansible/hosts" ini >}}
        [webserver]
        192.0.2.1

        {{< /file >}}

#### Create the Limited User Account Playbook

Now you're ready to create the Limited User Account Playbook. This playbook will create a new user on your utho, add your Ansible control node's SSH public key to the utho, and include the new user in the utho's `sudoers` file.

In your home directory, create a file named `limited_user_account.yml` and add the contents of the example below. Replace the following values in the file:

- Replace `yourusername` with the desired username for the new utho user.

- Replace $6$rounds=656000$W.dSl with the password hash you generated in the [Create a Password Hash](#create-a-password-has) section of this guide.

         {{< file "limited_user_account.yml" yaml >}}
---
         - hosts: webserver
         remote_user: root
         vars:
         NORMAL_USER_NAME: 'yourusername'
         tasks:
        - name: "Create a secondary, non-root user"
        user: name={{ NORMAL_USER_NAME }}
            password='$6$rounds=656000$W.dSl'
            shell=/bin/bash
         - name: Add remote authorized key to allow future passwordless logins
          authorized_key: user={{ NORMAL_USER_NAME }} key="{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
          - name: Add normal user to sudoers
          lineinfile: dest=/etc/sudoers
                  regexp="{{ NORMAL_USER_NAME }} ALL"
                  line="{{ NORMAL_USER_NAME }} ALL=(ALL) ALL"
                  state=present

                 {{< /file >}}

The first two lines of the file instruct Ansible to target the `webserver` group of hosts in the inventory file and execute remote host tasks as the `root` user.

The `vars` section defines the `NORMAL_USER_NAME` variable, which can be reused throughout the playbook. Ansible also supports creating and utilizing variables in separate files instead of directly in the playbook. For a comprehensive understanding of variable usage with Ansible, refer to the official documentation on [Using Variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#defining-variables-in-files).

The `tasks` block of the playbook consists of three tasks. The first task creates the new user and assigns a password to it. The second task adds the Ansible control node's public SSH key to the utho. The third task involves adding the new user to the sudoers file.

Each task utilizes Jinja templating (e.g., `{{ NORMAL_USER_NAME }}`) to access the referenced variable values. Jinja templating is a powerful feature of Ansible that provides access to control logic, filters, lookups, and functions within your playbooks. To delve deeper, refer to Ansible's official documentation.

Run the `limited_user_account.yml` playbook. The `--ask-pass` option directs Ansible to log into the utho using password authentication instead of SSH, as your public SSH key is not yet stored there. The `-u root` option specifies Ansible to log in as the root user. If not provided, Ansible will use your current local system's username by default.

        ansible-playbook --ask-pass -u root limited_user_account.yml

Ansible should provide output indicating that all three tasks completed successfully, with a status of "changed." With this, we can proceed to work with new playbooks using our limited user account and keys.

### Configure the Base System

The next playbook will handle common server setup tasks, such as configuring the timezone, updating the hosts file, and upgrading packages.

Create a file named `common_server_setup.yml` in your home directory and input the following example contents. Replace the following values in the file:

- Replace `yourusername` with the username you created in the Create the Limited User Account Playbook section of the guide.

- Replace `web01` with the desired hostname for your utho.

- If you have a domain name to set up, replace `www.example.com` with it.


          {{< file "common_server_setup.yml" yaml >}}
---
        - hosts: webserver
        remote_user: yourusername
        become: yes
        become_method: sudo
        vars:
         LOCAL_HOSTNAME: 'web01'
         LOCAL_FQDN_NAME: 'www.example.com'
        tasks:
       - name: Set the timezone for the server to be UTC
        command: ln -sf /usr/share/zoneinfo/UTC /etc/localtime
       - name: Set up a unique hostname
        hostname: name={{ LOCAL_HOSTNAME }}
       - name: Add the server's domain to the hosts file
       lineinfile: dest=/etc/hosts
                  regexp='.*{{ item }}$'
                  line="{{ hostvars[item].ansible_default_ipv4.address }} {{ LOCAL_FQDN_NAME }} {{ LOCAL_HOSTNAME }}"
                  state=present
      when: hostvars[item].ansible_default_ipv4.address is defined
      with_items: "{{ groups['utho'] }}"
    - name: Update packages
      apt: update_cache=yes upgrade=dist
          {{< /file >}}

- The first task in this playbook utilizes the command module to set the utho's timezone to UTC time.* The second task employs the hostname module to configure your system's hostname.

- The third task updates the utho's host file using the lineinfile module. This task leverages hostvars to retrieve the host's IP address and utilize it for updating the hosts file. hostvars is a reserved special variable that grants access to various host information.

- The fourth task upgrades your system's packages using the apt module.

Execute the `common_server_setup.yml` playbook. Use the `--ask-become-pass` option for Ansible to prompt for the limited user account's password to become the sudo user and execute the playbook via the limited user account.


Note: By default, Ansible will authenticate with your utho using your current local system's username. If your local username differs from your utho's limited user account name, you need to specify the -u option along with the limited user account name to authenticate properly. Replace limitedUserAccountName with the limited user account name created in the Create the Limited User Account Playbook section of the guide.

        ansible-playbook common_server_setup.yml --ask-become-pass -u limitedUserAccountName

- Upon the playbook execution initiation, you'll be prompted to input a `BECOME password:`. This password is the one you generated in the [Create a Password Hash](#create-a-password-hash) Hash section of the guide.

- As the playbook runs, you'll observe the tasks displaying as "changed" again.

- Note that updating packages may require a few minutes.

### Install the Stack

Now, you're prepared to craft the `setup_webserver.yml` playbook, which will configure your utho with Apache, PHP, and a test MySQL database.

Follow the instructions in the [Create a Password Hash](#create-a-password-hash) section of the guide to generate a new password hash for use in this playbook.

Create a file named `setup_webserver.yml` in your home directory and insert the following example contents. Replace the following values in the file:

- Replace `yourusername` with the username you created in the [Create the Limited User Account Playbook](#create-the-limited-user-account-playbook) section of the guide.

- In the Create a new user for connections task, replace the value of `password` with your desired password.

Note: To avoid using plain text passwords in your playbooks, consider utilizing Ansible-Vault and variables to encrypt sensitive data. You can refer to the How to use the utho Ansible Module to Deploy uthos guide for an example demonstrating this feature.

        {{< file "setup_webserver.yml" yaml >}}
---
        - hosts: webserver
         remote_user: yourusername
         become: yes
         become_method: sudo
         tasks:
        - name: "Install Apache, MySQL, and PHP"
         apt:
          pkg:
           - apache2
           - mysql-server
           - python-mysqldb
           - php
           - php-pear
           - php-mysql
             state: present

      - name: "Turn on Apache and MySQL and set them to run on boot"
      service: name={{ item }} state=started enabled=yes
      with_items:
        - apache2
        - mysql

           - name: Create a test database
                mysql_db: name= testDb
                state= present

    - name: Create a new user for connections
      mysql_user: name=webapp
                  password='$6$rounds=656000$W.dSl'
                  priv=*.*:ALL state=present

                  {{< /file >}}

- The first task handles the installation of Apache, MySQL, and PHP.

- The subsequent task ensures that Apache and MySQL persist running after a system reboot. This task employs a [loop](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html) to populate the value of the `service` name.

- Following that, the playbook creates a MySQL database named `testDB`.

- Lastly, a new MySQL user named `webapp` is established.

Execute the playbook from your control machine using the following command:

        ansible-playbook setup_webserver.yml --ask-become-pass

Once the playbook completes its execution, navigate to your utho's IP address or Fully Qualified Domain Name (FQDN) to view the default Ubuntu Apache index page.

SSH into the utho you just configured and verify that the testDB database has been successfully created:

         sudo mysql -u webapp -p
         show databases;

Optionally, you can create a sample PHP page and locate it in `/var/www/html` to confirm that PHP is operational on the server.

## Next Steps

Now that you've gained familiarity with Playbooks, you can further your understanding of Ansible. Ansible's GitHub repository offers numerous example Playbooks that serve as references for exploring various implementation options and patterns. Here are some suggested topics for further exploration in order to construct Playbooks of increased complexity:

* [Ansible Example Playbooks (GitHub)](https://github.com/ansible/ansible-examples)
  * [WordPress + nginx + PHP-FPM](https://github.com/ansible/ansible-examples/tree/master/wordpress-nginx)
  * [Simple LAMP Stack](https://github.com/ansible/ansible-examples/tree/master/lamp_simple)
  * [Sharded, production-ready MongoDB cluster](https://github.com/ansible/ansible-examples/tree/master/mongodb)
* [Ansible Documentation](http://docs.ansible.com/ansible/index.html)
* Important Next Topics:
  * [Users, and Switching Users](http://docs.ansible.com/ansible/playbooks_intro.html#hosts-and-users) and [Privilege Escalation](http://docs.ansible.com/ansible/become.html)
  * [Handlers: Running Operations On Change](https://docs.ansible.com/ansible/latest/user_guide/playbooks_handlers.html)
  * [Roles](https://docs.ansible.com/ansible/latest/user_guide/playbooks_reuse_roles.html)
  * [Variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html)
  * [Playbook Best Practices](http://docs.ansible.com/ansible/playbooks_best_practices.html)
