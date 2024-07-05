---
slug: Deploy-Uthos-with-Utho-Ansible-Collection
description: "The Utho Ansible collection offers plugins designed for administering Utho services through Ansible. This tutorial illustrates the installation and utilization of the Utho Ansible collection"
keywords: ['ansible','Utho Ansible Collection','dynamic inventory','configuration management']
published: 2024-05-16
modified: 2024-05-16
modified_by:
  name: Utho
title: "Use the Utho Ansible Collection to Deploy a Utho"
tags: ["automation", "saas", "gaming"]
authors: ["Utho"]
---

Ansible stands out as a widely embraced open-source Infrastructure as Code (IaC) tool renowned for its proficiency in accomplishing various IT tasks such as cloud provisioning and configuration management across diverse infrastructure components. Renowned for its adeptness in addressing challenges related to multi-cloud configurations, automation, and continuous delivery, Ansible has established itself as a cornerstone in the contemporary cloud ecosystem.

[Ansible Collections](https://github.com/ansible-collections/overview) represent the latest standard for efficiently managing Ansible content, streamlining the process of installing roles, modules, and plugins with reduced developer and administrative overhead. [The Utho Ansible collection](https://github.com/Utho/ansible_Utho) furnishes essential plugins necessary for promptly leveraging Utho services within the Ansible framework.

Note: The instructions provided in this guide involve creating a [1GB Utho](https://www.Utho.com/pricing/#compute-shared) which incurs billable charges on your Utho account. If you do not intend to retain the Utho created during this guide, ensure to delete it upon completion to avoid continued charges.

## Before You Begin

Note: The steps outlined in this guide necessitate [Ansible version 2.9.10 or greater](https://github.com/ansible/ansible/releases/tag/v2.9.10) and were validated on a Utho running Ubuntu 22.04. However, these instructions can be adapted for other Linux distributions or operating systems.

Provision a server to serve as the Ansible [*control node*](/docs/guides/getting-started-with-ansible/#what-is-ansible), from which other compute instances are deployed. Refer to our [Creating a Compute Instance](/docs/products/compute/compute-instances/guides/create/) guide to create a Utho instance running Ubuntu 22.04. A shared CPU 1GB is suitable. Alternatively, you can utilize an existing workstation or laptop.

Add a restricted Linux user to your control node Utho by following the steps outlined in the [Add a Limited User Account](/docs/products/compute/compute-instances/guides/set-up-and-secure/#add-a-limited-user-account) section of our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide. Ensure all subsequent commands in this guide are executed as your limited user.

Ensure your system is up to date by performing system updates:

            sudo apt update && sudo apt upgrade

Install Ansible on your control node by following the instructions outlined in the [Install Ansible](/docs/guides/getting-started-with-ansible/#install-ansible) section of the [Getting Started With Ansible - Basic Installation and Setup](/docs/guides/getting-started-with-ansible/) guide.

Verify that Python version 2.7 or higher is installed on your control node by executing the following command:

             python --version

Many operating systems, including Ubuntu 22.04, come with Python 3 installed by default. You can typically invoke the `Python 3` interpreter using the python3 command. The remainder of this guide assumes Python 3 is installed and utilized. To check your Python 3 version, execute:

              python3 --version

Install the pip package manager:

              sudo apt install python3-pip

Generate a Utho API v4 access token with permissions to read and write Uthos, and securely record it in a password manager or other safe location. Follow the steps outlined in the [Get an Access Token](/docs/products/tools/api/get-started/#get-an-access-token) section of the [Getting Started with the Utho API](/docs/products/tools/api/get-started/) guide.

## Install the Utho Ansible Collection

The Utho Ansible collection is currently available as an open-source project hosted on both a public GitHub repository and Ansible Galaxy. Ansible Galaxy serves as Ansible's community-centric repository, offering access to a diverse range of Ansible collections and roles, and is natively supported in the latest Ansible versions.

Users have the option to install the Utho Ansible collection from source files or via Git repositories. However, the following steps demonstrate how to utilize Ansible Galaxy:

Install necessary dependencies for Ansible:

             sudo -H pip3 install -Iv 'resolvelib<0.6.0'

Download the latest version of the Utho Ansible collection using the `ansible-galaxy` command:

            ansible-galaxy collection install Utho.cloud

Once the collection is installed, all configuration files are stored in the default `~/.ansible/collections/ansible_collections/` collections folder.

Install the Python module dependencies required for the Utho Ansible collection. The Utho collection's installation directory contains a `requirements.txt` file that lists the Python dependencies, including the official Python library for the Utho API v4. Use pip to install these dependencies:

        sudo pip3 install -r .ansible/collections/ansible_collections/Utho/cloud/requirements.txt

The Utho Ansible collection is now installed and ready to deploy and manage Utho services.

## Configure Ansible

When interfacing with the Utho Ansible collection, it is generally good practice to use variables to securely store sensitive strings like API tokens. This section shows how to securely store and access the Utho API Access token (generated in the Before You Begin section) along with a root password that is assigned to new Utho instances. Both of these are encrypted with Ansible Vault.

### Create an Ansible Vault Password File

From the control node's home directory, create a development directory to hold user-generated Ansible files. Then navigate to this new directory:

                  mkdir development && cd development

In the `development` directory, create a new empty text file called `.vault-pass` (with no file extension). Then generate a unique, complex new password (for example, by using a password manager), copy it into the new file, and save it. This password is used to encrypt and decrypt information stored with Ansible Vault:

                  {{< file "~/development/.vault-pass" >}}
                  <PasteYourAnsibleVaultPasswordHere>
                  {{< /file >}}

This is an Ansible Vault password file. A password file provides your Vault password to Ansible Vault's encryption commands. Ansible Vault also offers other options for password management. To learn more about password management, read Ansible's [Providing Vault Passwords](https://docs.ansible.com/ansible/latest/user_guide/vault.html#providing-vault-passwords) documentation.

Set permissions on the file so that only your user can read and write to it:

                  chmod 600 .vault-pass

### Create an Ansible Configuration File

Create an Ansible configuration file called `ansible.cfg` with a text editor of your choice. Copy this snippet into the file:

              {{< file "~/development/ansible.cfg">}}
              [defaults]
              VAULT_PASSWORD_FILE = ./.vault-pass
              {{< /file >}}

These lines specify the location of your password file.

### Encrypt Variables with Ansible Vault

Create a directory to store variable files used with your [Ansible playbooks](/docs/guides/getting-started-with-ansible/#what-is-ansible):

        mkdir -p ~/development/group_vars/

Make a new empty text file called `vars.yml` in this directory. In the next steps, your encrypted API token and root password are stored in this file:

        touch ~/development/group_vars/vars.yml

Generate a unique, complex new password (for example, by using a password manager) that should be used as the root password for new compute instances created with the Utho Ansible collection. This should be different from the Ansible Vault password specified in the `.vault-pass` file.

Use the following ansible-vault encrypt_string command to encrypt the new root password, replacing `MySecureRootPassword` with your password. Because this command is run from inside your `~/development` directory, the Ansible Vault password in your `.vault-pass` file is used to perform the encryption:

           ansible-vault encrypt_string 'MySecureRootPassword' --name 'password' | tee -a group_vars/vars.yml

In the above command, `tee -a group_vars/vars.yml` appends the encrypted string to your `vars.yml` file. Once completed, output similar to the following appears:

           {{< output >}}
               password: !vault |
               $ANSIBLE_VAULT;1.1;AES256
               30376134633639613832373335313062366536313334316465303462656664333064373933393831
               3432313261613532346134633761316363363535326333360a626431376265373133653535373238
               38323166666665376366663964343830633462623537623065356364343831316439396462343935
               6233646239363434380a383433643763373066633535366137346638613261353064353466303734
               3833
               {{< /output >}}

Run the following command to add a newline at the end of your `vars.yml` file:

        echo "" >> group_vars/vars.yml

Use the following `ansible-vault encrypt_string` command to encrypt your Utho API token and append it to your `vars.yml` file, replacing `MyAPIToken` with your own access token:

           ansible-vault encrypt_string 'MyAPIToken' --name 'api-token' | tee -a group_vars/vars.yml

Run the following command to add another newline at the end of your `vars.yml` file:

             echo "" >> group_vars/vars.yml

Your `vars.yml` file should now resemble:

          {{< file "~/development/group_vars/vars.yml">}}
        password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          30376134633639613832373335313062366536313334316465303462656664333064373933393831
          3432313261613532346134633761316363363535326333360a626431376265373133653535373238
          38323166666665376366663964343830633462623537623065356364343831316439396462343935
          6233646239363434380a383433643763373066633535366137346638613261353064353466303734
          3833
       token: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          65363565316233613963653465613661316134333164623962643834383632646439306566623061
          3938393939373039373135663239633162336530373738300a316661373731623538306164363434
          31656434356431353734666633656534343237333662613036653137396235353833313430626534
          3330323437653835660a303865636365303532373864613632323930343265343665393432326231
          61313635653463333630636631336539643430326662373137303166303739616262643338373834
          34613532353031333731336339396233623533326130376431346462633832353432316163373833
          35316333626530643736636332323161353139306533633961376432623161626132353933373661
          36663135323664663130
           {{< /file >}}

## Understanding Fully Qualified Collection Namespaces

Ansible is now configured and the Utho Ansible collection is installed. You can create [playbooks](/docs/guides/running-ansible-playbooks/#playbook-basics) to leverage the collection and create compute instances and other Utho resources.

Within playbooks, the Utho Ansible collection is further divided by resource types through the [Fully Qualified Collection Name](https://github.com/ansible-collections/overview#terminology)(FQCN) affiliated with the desired resource. These names serve as identifiers that help Ansible to more easily and authoritatively delineate between modules and plugins within a collection.

## Deploy a Utho with the Utho Ansible Collection

This section demonstrates how to write a playbook utilizing the Utho Ansible collection and your encrypted API token and root password to create a new Utho instance:

Create a playbook file called `deployUtho.yml` in your `~/development` directory. Copy this snippet into the file and save it:

                {{< file "~/development/deployUtho.yml">}}
              - name: Create Utho Instance
                hosts: localhost
                vars_files:
                  - ./group_vars/vars.yml
             tasks:
                 - name: Create a Utho instance
                          Utho.cloud.instance:
                          api_token: "{{ api_token }}"
                          label: my-ansible-Utho
                          type: g6-1
                          region: us-east
                          image: Utho/ubuntu22.04
                          root_pass: "{{ password }}"
                          state: present
                         {{< /file >}}

The playbook contains the Create `Utho Instance play`. When executed, the control node receives instructions from Ansible and employs the Utho API to deploy infrastructure as required.

The `vars_files` key specifies the location of the variable file(s) utilized to populate information relevant to tasks within the play.

Each task in the playbook is identified by its `name`, serving as a label, along with the Fully Qualified Collection Name (FQCN) utilized to configure the resource, such as a Utho compute instance.

Configuration options associated with the FQCN are specified. These options are unique to each resource.

Secure strings are inserted for options where encrypted variables are utilized, sourced from the `./group_vars/vars.yml` file, which includes the API token and root password.

After saving the playbook, execute the following command to run it and create a Utho instance. This command is executed from within your `~/development` directory, utilizing the Ansible Vault password stored in your .vault-pass file to decrypt the variables:

            ansible-playbook deployUtho.yml

Upon completion, output similar to the following will be displayed:

         {{< output >}}
    PLAY [Create Utho] *********************************************************************

    TASK [Gathering Facts] *******************************************************************
    ok: [localhost]

    TASK [Create a new Utho.] **************************************************************
    changed: [localhost]

    PLAY RECAP *******************************************************************************
    localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    {{< /output >}}
