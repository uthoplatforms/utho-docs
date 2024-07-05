---
slug: "Streamline Utho Deployment with Ansible: Simplify Your Infrastructure Management"
description: "Discover the seamless process of deploying and overseeing Uthos through Ansible alongside the Utho_v4 module in this comprehensive guide"
keywords: ['ansible','Utho module','dynamic inventory','configuration management']
published: 2024-05-21
modified: 2024-05-21
modified_by:
  name: Utho
title: "Using the Utho Ansible Module to Deploy Uthos"
title_meta: "How to use the Utho Ansible Module to Deploy Uthos"
deprecated: true
deprecated_link: 'guides/deploy-Uthos-using-Utho-ansible-collection/'
tags: ["automation"]
authors: ["Utho"]
---

Note: This guide demonstrates how to utilize the legacy Utho Ansible module for Utho infrastructure management, which is maintained by the Utho community. Although still functional, we recommend transitioning to the newer Utho Ansible collection, managed by the Utho development team. For further details, refer to our Using the Utho Ansible Collection to Deploy a Utho guide.

Ansible, a widely-used open-source tool, facilitates automating various IT tasks, including cloud provisioning and configuration management. With the release of Ansible 2.8, integration with our latest API (v4) enables deploying Utho instances. The Utho_v4 module empowers users to deploy and oversee Uthos via the command line or within their Ansible Playbooks, complemented by a dynamic inventory plugin for Utho to streamline inventory management.

In this guide, you'll master:

Deploying and managing Uthos through Ansible and the Utho_v4 module.
Generating an Ansible inventory tailored to your Utho infrastructure using the Utho dynamic inventory plugin.
Kindly note: The instructions provided in this guide will create a 1GB Utho (Nanode) on your Utho account, resulting in billable resources. If you opt not to retain the created Utho, ensure to delete the resource upon completing the guide. Removal of the resource afterward will incur charges solely for the duration it remained active in your account.

## Before You Begin

Note: The procedures detailed in this guide necessitate Ansible version 2.8 and were formulated using Ubuntu 18.04.

To begin, add a limited user to your Utho by following the steps outlined below. This process is elaborated in the Add a limited User Account section of our Setting Up and Securing a Compute Instance guide. Please ensure that all commands are executed under your limited user's context.

Next, install Ansible on your local machine. You can achieve this by following the instructions in the Control Node Setup section of the Getting Started With Ansible - Basic Installation and Setup guide.

Before proceeding further, confirm that your computer has Python version 2.7 or higher installed. You can verify the installed Python version by executing the following command:

                    python --version

Install the official Python library for the Utho API v4 by following the provided instructions.

                    sudo apt-get install python-pip
                    sudo pip install Utho_api4

Generate a Utho API v4 access token with both read and write permissions for Uthos. You can refer to the Get an Access Token section of the Getting Started with the Utho API guide if you haven't obtained one yet.

If your computer doesn't already have one, create an authentication key-pair.

## Configure Ansible

The Ansible configuration file serves to customize Ansible's default system settings. Ansible scans for a configuration file in the directories listed below, adhering to the specified order, and applies the first encountered configuration values:

ANSIBLE_CONFIG environment variable pointing to a configuration file location. If provided, it supersedes the default Ansible configuration file.

- `ansible.cfg` file in the current directory.
- `~/.ansible.cfg` in the home directory.
- `/etc/ansible/ansible.cfg`.

In this segment, you'll craft an Ansible configuration file, incorporating options to deactivate host key checking and permit the Utho inventory plugin. Although the Ansible configuration file will be positioned in a development directory you create, it could potentially reside in any of the aforementioned locations. Refer to Ansible's official documentation for a comprehensive compilation of available configuration settings.

Please note: When storing your Ansible configuration file, exercise caution to ensure that the corresponding directory does not possess world-writable permissions. This precautionary measure mitigates potential security vulnerabilities, preventing malicious users from exploiting Ansible to compromise your local system and remote infrastructure. Ideally, the directory should restrict access to specific users and groups. For instance, you can establish an ansible group, exclusively enlist privileged users in the ansible group, and adjust the Ansible configuration file's directory permissions to 764. For further insights on permissions, consult the Linux Users and Groups guide.

Navigate to your home directory and establish a dedicated folder to manage all your Ansible-related files:

        mkdir development && cd development

1.  Generate the Ansible configuration file named ansible.cfg within the development directory, and incorporate the host_key_checking and enable_plugins options.

                        {{< file "~/development/ansible.cfg">}}
                        [defaults]
                         host_key_checking = False
                         VAULT_PASSWORD_FILE = ./vault-pass
                         [inventory]
                         enable_plugins = Utho  
                        {{</ file >}}

- Setting host_key_checking = False will authorize Ansible to SSH into hosts without necessitating acceptance of the remote server's host key. This configuration globally disables host key checking.

- Utilizing VAULT_PASSWORD_FILE = ./vault-pass allows specification of a Vault password file to be used whenever Ansible Vault requires a password. Ansible Vault provides multiple options for managing passwords. For further insights on password management, refer to Ansible's Providing Vault Passwords documentation.

- Enabling enable_plugins = Utho facilitates the Utho dynamic inventory plugin.

## Create a Utho Instance

Now, you can initiate the process of creating Utho instances using Ansible. In this segment, you will craft an Ansible Playbook capable of deploying Uthos.

### Create your Utho Playbook

Confirm that you are currently within the development directory you established in the Configure Ansible section:

                   cd ~/development

 Utilize your preferred text editor to craft the Create Utho Playbook file, incorporating the following values:

                  {{< file "~/development/Utho_create.yml" yaml >}}
                 - name: Create Utho
                    hosts: localhost
                     vars_files:
                    - ./group_vars/example_group/vars
                      tasks:
                    - name: Create a new Utho.
                       Utho_v4:
                        label: "{{ label }}{{ 100 |random }}"
                         access_token: "{{ token }}"
                         type: g6-nanode-1
                        region: us-east
                        image: Utho/debian9
                        root_pass: "{{ password }}"
                        authorized_keys: "{{ ssh_keys }}"
                        group: example_group
                         tags: example_group
                         state: present
                         register: my_Utho
                         {{</ file >}}

The Playbook my_Utho encompasses the Create Utho play, scheduled to run on hosts: localhost. This signifies that the Ansible playbook will execute on the local system, orchestrating the deployment of remote Utho instances.

The vars_files directive specifies the location of a local file containing variable values to populate within the play. Any variables defined in this file will replace corresponding Jinja template variables used in the Playbook. Jinja template variables are denoted by curly brackets, such as: {{ my_var }}.

The Create a new Utho task invokes the Utho_v4 module, supplying all required module parameters as arguments, along with additional arguments for configuring the deployment of the Utho. Refer to the Utho_v4 Module Parameters section for detailed information on each parameter.

Note: Although the usage of groups is deprecated, it is still supported by Utho's API v4. The Utho dynamic inventory module necessitates groups to generate an Ansible inventory, which will be utilized later in this guide.

The register keyword defines a variable named my_Utho to store data returned by the Utho_v4 module. This variable can be referenced later in the Playbook to execute additional actions based on Utho data. While this keyword is not mandatory for deploying a Utho instance, it represents a common approach for declaring and utilizing variables in Ansible Playbooks. The task depicted below will utilize Ansible's debug module alongside the my_Utho variable to output a message containing the Utho instance's ID and IPv4 address during Playbook execution.

                   {{< file >}}
                   ...
                   - name: Print info about my Utho instance
                     debug:
                     msg: "ID is {{ my_Utho.instance.id }} IP is {{ my_Utho.instance.ipv4 }}"
                     {{</ file >}}

### Create the Variables File

In the preceding section, you crafted the Create Utho Playbook for deploying Utho instances, utilizing Jinja template variables. Now, in this section, you'll generate the variables file to supply values to those template variables.

Establish the directory to house your Playbook's variable files. The directory layout is organized to categorize your variable files by inventory group. This structure facilitates file-level encryption, which Ansible Vault can recognize and interpret. Although it's not directly applicable to this guide's example, it's regarded as a best practice.

                    mkdir -p ~/development/group_vars/example_group/

Generate the variables file and populate it with the provided example variables. You have the option to substitute the values with your own preferences.

                             {{< file "~/development/group_vars/example_group/vars">}}
                             ssh_keys: >
                             ['ssh-rsa AAAAB3N..5bYqyRaQ== user@mycomputer', '~/.ssh/id_rsa.pub']
                             label: simple-Utho-
                             {{</ file >}}

The ssh_keys example entails a list containing two public SSH keys. The first entry furnishes the string value of the key, while the second designates a local public key file location.

Note: If your SSH keys are passphrase-protected, it's advisable to add them to your SSH agent to prevent Ansible from hanging when executing Playbooks on the remote Utho. Linux users can execute the following command. If your private key is stored elsewhere, adjust the path passed to ssh-add accordingly:

                              eval $(ssh-agent) && ssh-add ~/.ssh/id_rsa

If you initiate a new terminal session, you'll be required to execute the commands in this step once more to access the keys stored in your SSH agent.

label furnishes a label prefix that will be combined with a random number. This concatenation transpires during the parsing of the label argument in the Create Utho Playbook's Jinja templating (label: "{{ label }}{{ 100 |random }}").

#### Encrypt Sensitive Variables with Ansible Vault

Ansible Vault provides a means to encrypt sensitive data, such as passwords or tokens, safeguarding them from exposure in your Ansible Playbooks or Roles. You'll leverage this feature to encrypt your Utho instance's password and access_token within the variables file.

Note: Ansible Vault can also encrypt entire files containing sensitive values. Refer to Ansible's documentation on Vault for additional details.

Begin by creating your Ansible Vault password file and inserting your password into the file. Remember, the location of the password file was configured in the ansible.cfg file in the Configure Ansible section of this guide.

                    {{< file "~/development/vault-pass">}}
                    My.ANS1BLEvault-c00lPassw0rd
                     {{</ file >}}

Utilize Ansible Vault to encrypt the value of your Utho's root user password. Substituting My.c00lPassw0rd with your own robust password that adheres to the constraints of the root_pass parameter.

                 ansible-vault encrypt_string 'My.c00lPassw0rd' --name 'password'

Upon execution, you'll encounter a similar output:

                       {{< output >}}
                       password: !vault |
                       $ANSIBLE_VAULT;1.1;AES256
                        30376134633639613832373335313062366536313334316465303462656664333064373933393831
                        3432313261613532346134633761316363363535326333360a626431376265373133653535373238
                        38323166666665376366663964343830633462623537623065356364343831316439396462343935
                        6233646239363434380a383433643763373066633535366137346638613261353064353466303734
                        3833</br>
                        Encryption successful
                         {{</ output >}}

Copy the generated output and incorporate it into your vars file.

Encrypt the value of your access token. Replace the existing value of 86210...1e1c6bd with your own access token.

                   ansible-vault encrypt_string '86210...1e1c6bd' --name 'token'

Copy the resulting output and append it to the end of your vars file.

Your final vars file should resemble the example below

                       {{< file "~/development/group_vars/example_group/vars">}}
                       ssh_keys: >
                       ['ssh-rsa AAAAB3N..5bYqyRaQ== user@mycomputer', '~/.ssh/id_rsa.pub']
                       label: simple-Utho-
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
                           {{</ file >}}

### Run the Ansible Playbook

You are all set to execute the Create Utho Playbook. Running this playbook will deploy a 1GB Utho (Nanode) in the Newark data center. Remember to execute Ansible commands from the directory where your ansible.cfg file is situated.

Execute your playbook to initiate the creation of your Utho instances.

                      ansible-playbook ~/development/Utho_create.yml

You can expect to observe a comparable output:

    {{<output>}}

     PLAY [Create Utho] *********************************************************************

     TASK [Gathering Facts] *******************************************************************
     ok: [localhost]

     TASK [Create a new Utho.] **************************************************************
     changed: [localhost]

     PLAY RECAP *******************************************************************************
     localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
     {{</output>}}

### Utho_v4 Module Parameters

| Parameter | Data type/Status | Usage |
| --------- | -------- | ------|
| `access_token` | string, *required* | Your Utho API v4 access token. The token should have permission to read and write Uthos. The token can also be specified by exposing the `Utho_ACCESS_TOKEN` environment variable. |
| `authorized_keys` | list | A list of SSH public keys or SSH public key file locations on your local system, for example, `['averylongstring','~/.ssh/id_rsa.pub']`. The public key will be stored in the `/root/.ssh/authorized_keys` file on your Utho. Ansible will use the public key to SSH into your Uthos as the root user and execute your Playbooks.|
| `group` | string, *deprecated* | The Utho instance's group. Please note, group labelling is deprecated but still supported. The encouraged method for marking instances is to use tags. This parameter must be provided to use the Utho dynamic inventory module. |
| `image` | string | The Image ID to deploy the Utho disk from. Official Utho Images start with `Utho/`, while your private images start with `private/`. For example, use `Utho/ubuntu18.04` to deploy a Utho instance with the Ubuntu 18.04 image. This is a required parameter only when creating Utho instances.</br></br> To view a list of all available Utho images, issue the following command: </br></br>`curl https://api.Utho.com/v4/images`.|
| `label` | string, *required* | The Utho instance label. The label is used by the module as the main determiner for idempotence and must be a unique value.</br></br> Utho labels have the following constraints:</br></br> &bull; Must start with an alpha character.</br>&bull; May only consist of alphanumeric characters, dashes (-), underscores (_) or periods (.).</br> &bull; Cannot have two dashes (--), underscores (__) or periods (..) in a row. |
| `region` | string | The region where the Utho will be located. This is a required parameter only when creating Utho instances.</br></br> To view a list of all available regions, issue the following command: </br></br>`curl https://api.Utho.com/v4/regions`. |
| `root_pass` | string | The password for the root user. If not specified, will be generated. This generated password will be available in the task success JSON.</br></br> The root password must conform to the following constraints: </br></br> &bull; May only use alphanumerics, punctuation, spaces, and tabs.</br>&bull; Must contain at least two of the following characters classes: upper-case letters, lower-case letters, digits, punctuation. |
| `state` | string, *required* | The desired instance state. The accepted values are `absent` and `present`. |
| `tags` | list | The user-defined labels attached to Uthos. Tags are used for grouping Uthos in a way that is relevant to the user. |
| `type` | string, | The Utho instance's plan type. The plan type determines your Utho's [hardware resources](/docs/products/compute/compute-instances/plans/choosing-a-plan/#hardware-resource-definitions) and its [pricing](https://www.Utho.com/pricing/). </br></br> To view a list of all available Utho types including pricing and specifications for each type, issue the following command: </br></br>`curl https://api.Utho.com/v4/Utho/types`. |

## The Utho Dynamic Inventory Plugin

Ansible employs inventories to manage various hosts constituting your infrastructure. This facilitates task execution on specific segments of your infrastructure. By default, Ansible searches for an inventory in /etc/ansible/hosts, but you can specify an alternate location for your inventory file and utilize multiple inventory files representing distinct aspects of your infrastructure. To accommodate evolving infrastructures, Ansible offers the capability to dynamically track inventory from sources like cloud providers. The Ansible dynamic inventory plugin for Utho enables you to source your inventory directly from Utho's API v4. In this segment, you'll leverage the Utho plugin to acquire your Utho inventory deployed via Ansible.

Note: The dynamic inventory plugin for Utho was enabled in the Ansible configuration file established in the Configure Ansible section of this guide.

### Configure the Plugin

Set up the Ansible dynamic inventory plugin for Utho by generating a file named Utho.yml.

         {{< file "~/development/Utho.yml"yaml>}}
         plugin: Utho
         regions:
         - us-east
          groups:
         - example_group
           types:
           - g6-nanode-1
           {{</ file >}}

This configuration file generates an inventory consisting of Uthos on your account situated in the us-east region, belonging to the example_group group, and categorized as type g6-nanode-1. Uthos not assigned to the example_group group but matching the us-east region and g6-nanode-type type will be listed as ungrouped. Any other Uthos will be omitted from the dynamic inventory. For comprehensive details on all supported parameters, refer to the Plugin Parameters section.

### Run the Inventory Plugin

Set your Utho API v4 access token as an environment variable in the shell using Utho_ACCESS_TOKEN. Replace mytoken with your unique access token.

        export Utho_ACCESS_TOKEN='mytoken'

Execute the Utho dynamic inventory plugin.

        ansible-inventory -i ~/development/Utho.yml --graph

You'll observe a similar output. Note that the output might differ based on the Uthos currently deployed on your account and the parameters you provide.

        @all:
        |--@example_group:
        |  |--simple-Utho-29

For a comprehensive output containing all Utho instance configurations, utilize the following command:

        ansible-inventory -i ~/development/Utho.yml --graph --vars

Before interacting with your Utho instances via the dynamic inventory plugin, ensure to include your Utho's IPv4 address and label in your /etc/hosts file.

The Utho Dynamic Inventory Plugin operates under the assumption that the Uthos within your account possess labels that align with hostnames listed in your resolver search path, typically located in /etc/hosts. Consequently, you must establish an entry in your /etc/hosts file to correlate the Utho's IPv4 address with its hostname.

Note: A pull request is currently pending to support the utilization of a public IP, private IP, or hostname. This enhancement will allow the inventory plugin to function with infrastructures lacking DNS hostnames or hostnames matching Utho labels.

To incorporate your deployed Utho instance into the /etc/hosts file:

- Retrieve the IPv4 address of your Utho instance:

            ansible-inventory -i ~/development/Utho.yml --graph --vars | grep 'ipv4\|simple-Utho'

Your output will appear similar to the following:

        ```output
        |  |--simple-Utho-36
        |  |  |--{ipv4 = [u'192.0.2.0']}
        |  |  |--{label = simple-Utho-36}
        ```

Open the /etc/hosts file and insert your Utho's IPv4 address along with its label:

        ```file {title="/etc/hosts"}
        127.0.0.1       localhost
        192.0.2.0 simple-Utho-29
        ```

Confirm that you can establish communication with your grouped inventory by pinging the Uthos. The ping command will leverage the dynamic inventory plugin configuration file to target example_group. Employ the u root option to execute the command as root on the Utho hosts.

        ansible -m ping example_group -i ~/development/Utho.yml -u root

You should observe output similar to the following:

          {{<output>}}
          simple-Utho-29 | SUCCESS => {
          "ansible_facts": {
          "discovered_interpreter_python": "/usr/bin/python"
          },
          "changed": false,
          "ping": "pong"
          }
          {{</output>}}

###  Plugin Parameters

| Parameter | Data type/Status | Usage |
| --------- | -------- | ------|
| `access_token` | string, *required* | Your Utho API v4 access token. The token should have permission to read and write Uthos. The token can also be specified by exposing the `Utho_ACCESS_TOKEN` environment variable. |
| `plugin` | string, *required* | The plugin name. The value must always be `Utho` in order to use the dynamic inventory plugin for Utho. |
| `regions` | list | The Utho region with which to populate the inventory. For example, `us-east` is possible value for this parameter.</br></br> To view a list of all available regions, issue the following command: </br></br>`curl https://api.Utho.com/v4/regions`.  |
| `types` | list | The Utho type with which to populate the inventory. For example, `g6-nanode-1` is a possible value for this parameter.</br></br> To view a list of all available Utho types including pricing and specifications for each type, issue the following command: </br></br>`curl https://api.Utho.com/v4/Utho/types`. |
| `groups` | list | The Utho group with which to populate the inventory. Please note, group labelling is deprecated but still supported. The encouraged method for marking instances is to use tags. This parameter must be provided to use the Utho dynamic inventory module. |

## Delete Your Resources

To remove the Utho instance established in this guide, craft a Delete Utho Playbook with the provided content. Ensure to substitute the value of label with your Utho's label.

         {{< file "~/development/Utho_delete.yml" yaml>}}
         - name: Delete Utho
         hosts: localhost
         vars_files:
         - ./group_vars/example_group/vars
         tasks:
        - name: Delete your Utho Instance.
         Utho_v4:
         label: simple-Utho-29
         state: absent
         {{</ file >}}

Execute the Delete Utho Playbook:

        ansible-playbook ~/development/Utho_delete.yml
