---
slug: "Ansible Adventure: Embarking on Your Journey"
description: "This guide will walk you through harnessing Ansible's power to execute fundamental configuration operations on your Utho, alongside crafting a straightforward web server setup"
keywords: ["ansible", "ansible configuration", "ansible provisioning", "ansible infrastructure", "ansible automation", "ansible configuration change management", "ansible server automation"]
published: 2024-05-02
modified: 2024-05-02
modified_by:
    name: Pawan Kumar
title: "Getting Started With Ansible: Basic Installation and Setup"
title_meta: "Getting Started with Ansible: Installation and Setup"
tags: ["automation"]
authors: ["Pawan Kumar"]
---

Automatically Configure Servers with Ansible and Playbooks

## What is Ansible?
[Ansible](http://www.ansible.com/home) stands as a powerful automation tool tailored for server provisioning, configuration, and management. It streamlines the organization of servers into groups, delineates their configurations, and orchestrates actions, all centrally controlled.

To kickstart your journey with Ansible, it's pivotal to grasp a handful of fundamental terms and concepts that underpin its core components:

- **Control Node**: Acting as the nerve center, the control node manages your infrastructure nodes. This can range from your personal computer to a dedicated server. Optimal management speed is achieved by hosting the control node as close as possible to your managed nodes.

- **Managed Nodes**: These are the backbone of your infrastructure—hosts managed by the Ansible control node. Notably, managed nodes don't necessitate Ansible installation.

- **Inventory**: Ansible tracks its managed nodes via an [inventory file](http://docs.ansible.com/ansible/intro_inventory.html), typically housed in `/etc/ansible/hosts`. Within the inventory file, you can group managed nodes, enabling targeted actions on specific hosts within your infrastructure. Ansible seamlessly integrates multiple inventory sources, including additional inventory files and dynamic inventory sourced via plugins or scripts

In case your Ansible-managed infrastructure evolves dynamically, leveraging the [dynamic inventory plugin for Linode](https://docs.ansible.com/ansible/latest/plugins/inventory/linode.html) is highly advised. Refer to the guide on [How to use the Linode Ansible Module to Deploy Linodes](/docs/guides/deploy-linodes-using-ansible/) to acquaint yourself with utilizing this plugin.

- **Modules**: Modules enhance Ansible's capabilities by providing additional functionality. You can invoke Ansible modules directly from the command line to execute tasks on your managed nodes or incorporate them into your Playbooks. Refer to [Ansible's module index](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html) for a categorized list of available modules.
- **Tasks**: The fundamental unit of operation in Ansible is a task. Tasks utilize Ansible modules to manage services, packages, files, and perform various system configurations on your hosts. They can be executed either from the command line or within Playbooks.
- **Playbooks**: Playbooks are YAML files that contain a sequence of tasks arranged in the desired execution order. You can execute Playbooks on your managed nodes and share or reuse them across your infrastructure. Leveraging [Variables](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html) and [Jinja templating](https://docs.ansible.com/ansible/latest/user_guide/playbooks_templating.html) offers a robust mechanism to execute complex tasks on your managed hosts.

## Scope of this Guide
This guide serves as an introduction to installing Ansible and configuring your environment for utilizing [Playbooks](https://docs.ansible.com/ansible/latest/user_guide/playbooks.html). You will accomplish the following tasks:

- Install and set up Ansible on either your local machine or a Linode, designated as the control node responsible for managing your infrastructure nodes.
- Create two Linodes to be managed by Ansible and establish a basic connection between the control node and these managed nodes. Throughout this guide, the managed nodes will be denoted as `node-1` and `node-2`.


Note: The examples provided in this guide offer a manual approach to establish a rudimentary connection between your control node and managed nodes, aiming to introduce the fundamentals of Ansible. For a more automated method utilizing Ansible's [Linode module](https://docs.ansible.com/ansible/latest/modules/linode_v4_module.html) to deploy and manage Linodes, refer to the guide on [How to use the Linode Ansible Module to Deploy Linodes](/docs/guides/deploy-linodes-using-ansible/). This guide assumes prior knowledge of Ansible modules, Playbooks, and dynamic inventories.

## Before You Begin

Note: The example instructions in this guide will result in the creation of up to three billable Linodes on your account. If you do not wish to continue using the example Linodes created during this process, make sure to [delete them](#delete-a-cluster) once you have completed the guide.

By removing these resources afterward, you will only incur charges for the hour(s) during which the resources were active on your account. For detailed information on how hourly billing functions, refer to the [Billing and Payments](/docs/products/platform/billing/) guide.

[Create two Linodes](/docs/products/compute/compute-instances/guides/create/) running Ubuntu 22.04 LTS to serve as your **managed nodes**. Alternatively, you can follow the examples in this guide using a single managed node if preferred.

Ansible relies on the SSH protocol for secure login to managed nodes and application of Playbook configurations. Generate an SSH key pair on the control node for authentication purposes. It is assumed in this guide that your public and private SSH key pair is stored in `~/.ssh/id_rsa.pub` and `~/.ssh/id_rsa`, respectively.

    ```command
    ssh-keygen -t rsa -b 4096
    ```

Transfer the key to `node-1`. Replace `203.0.112.0` with the IP address of your managed Linode.

    ```command
    ssh-copy-id root@203.0.113.0
    ```

Repeat this process for each additional node.

Note: This step can be automated using Ansible's Linode module. For further details, refer to the [How to use the Linode Ansible Module to Deploy Linodes](/docs/guides/deploy-linodes-using-ansible/) guide.

## Set up the Control Node

### Install and Set Up Miniconda

Utilizing Miniconda facilitates the creation of a virtualized environment for Ansible, which can streamline the installation process across various distributions and environments requiring multiple Python versions. Ensure your control node has Python version 2.7 or higher to run Ansible.

Download and install Miniconda:

    ```command
    curl -OL https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh
    bash Miniconda3-latest-Linux-x86_64.sh
    ```

Throughout the installation process, you'll encounter several prompts. Review the terms and conditions, selecting "yes" for each prompt.

Restart your shell session to apply the changes to your PATH.

    ```command
    exec bash -l
    ```

Next, create a new virtual environment for Ansible:

    ```command
    conda create -n ansible-dev python=3
    ```

Activate the newly created environment:

    ```command
    conda activate ansible-dev
    ```

Verify your Python version:

    ```command
    python --version
    ```

### Install Ansible

Note: This guide was created using Ansible 2.8.

1.  Follow the Ansible installation steps related to your control node's distribution.

    ```command {title="MacOS"}
    sudo easy_install pip
    sudo pip install ansible
    ```

    ```command {title="Linux"}
    pip install ansible
    ```

Note: On certain versions of CentOS, RHEL, and Scientific Linux, you may need to add the EPEL-Release repository.

Verify the installation of Ansible:

    ```command
    ansible --version
    ```

## Configure Ansible

By default, Ansible's configuration file is located at `/etc/ansible/ansible.cfg`. In most cases, the default configurations suffice to begin using Ansible. For this example, we will utilize Ansible's default configurations.

To display a list of all current configurations available to your control node, utilize the `ansible-config` command line utility.

    ```command
    ansible-config list
    ```

Upon execution, you will receive similar output:

    ```output
      ACTION_WARNINGS:
      default: true
      description: [By default Ansible will issue a warning when received from a task
          action (module or action plugin), These warnings can be silenced by adjusting
          this setting to False.]
      env:
      - {name: ANSIBLE_ACTION_WARNINGS}
      ini:
      - {key: action_warnings, section: defaults}
      name: Toggle action warnings
      type: boolean
      version_added: '2.5'
    AGNOSTIC_BECOME_PROMPT:
      default: false
      ...
    ```

### Create an Ansible Inventory

Ansible keeps track of its managed nodes using an [inventory file](http://docs.ansible.com/ansible/intro_inventory.html) located in `/etc/ansible/hosts`. In the inventory file, you can group your managed nodes and use these groups to target specific hosts that make up your infrastructure. Ansible can use multiple inventory sources, like other inventory files and dynamic inventory pulled using an inventory plugin or script. If your Ansible managed infrastructure will change over time, it is recommended to use the dynamic inventory plugin for Linode. You can read the [How to use the Linode Ansible Module to Deploy Linodes](/docs/guides/deploy-linodes-using-ansible/) to learn how to manage Linodes.

Following the example below, you will add your **managed nodes** to the `/etc/ansible/hosts` inventory file in two separate groups. The nodes can be listed using a name that can be resolved by DNS or an IP address.

Add your nodes to the default inventory file. Replace `203.0.113.0` and `203.0.113.1` with the public IP address or domain name of each respective node.

    ```file {title="/etc/ansible/hosts" lang="ini"}
    [nginx]
    203.0.113.0

    [wordpress]
    203.0.113.1
    ```

Each bracketed label represents an Ansible [group](http://docs.ansible.com/ansible/latest/intro_inventory.html#hosts-and-groups). Organizing your nodes by function enhances the precision when executing commands against specific sets of nodes.

Note: In some environments, the `/etc/ansible` directory may not exist by default. If this is the case, create it manually using the following command:

```command
mkdir /etc/ansible/
```

Note: If you utilize a non-standard SSH port on your nodes, append the port after a colon on the same line within your hosts file (`203.0.113.1:2221`).

## Connect to your Managed Nodes

After setting up your control node, you're ready to establish communication with your managed nodes and start configuring them as required. In this section, you'll test the connection to your Ansible-managed hosts using the ping module. This module returns a "pong" response when the control node successfully reaches a node, verifying the connection and the ability to execute Python on the hosts.

- Use the `all` directive to ping all servers in your inventory. By default, Ansible will utilize your local user account's name to connect to your nodes via SSH. You can override this default behavior by appending the -u option along with the desired username. Since there are no standard user accounts on the nodes, in this example, you'll execute the command as the root user.

    ```command
    ansible all -u root -m ping
    ```

    You should receive a similar output:

    ```output
    192.0.2.0 | SUCCESS => {
        "changed": false,
        "ping": "pong"
    }
    192.0.2.1 | SUCCESS => {
        "changed": false,
        "ping": "pong"
    }
    ```

- Repeat the command, targeting only the nodes in the `[nginx]` group that you defined in your [inventory file](#create-an-ansible-inventory).

    ```command
    ansible nginx -u root -m ping
    ```

This time, only `node-1` should respond.

## Next Steps
- Now that you've installed and configured Ansible, you can leverage Playbooks to manage your Linodes' configurations. Refer to our [Automate Server Configuration with Ansible Playbooks](/docs/guides/running-ansible-playbooks/) guide for a demonstration of setting up a basic web server using an Ansible Playbook.

- You can also explore various [example playbooks](https://github.com/ansible/ansible-examples) on Ansible's GitHub repository to see a range of implementations.

- Check the links below to delve into several more advanced concepts related to writing Playbooks:

    -   [Users, and Switching Users](http://docs.ansible.com/ansible/playbooks_intro.html#hosts-and-users) and [Privilege Escalation](http://docs.ansible.com/ansible/become.html)
    -   [Handlers: Running Operations On Change](http://docs.ansible.com/ansible/playbooks_intro.html#handlers-running-operations-on-change)
    -   [Roles](http://docs.ansible.com/ansible/playbooks_roles.html)
    -   [Variables](http://docs.ansible.com/ansible/playbooks_variables.html)
    -   [Playbook Best Practices](http://docs.ansible.com/ansible/playbooks_best_practices.html)

### Delete Your Linodes
If you decide to remove the Linodes created in this guide, you can delete them through the [Linode Cloud Manager](https://cloud.linode.com/linodes). To learn how to remove Linode resources using Ansible's Linode module, refer to the [Delete Your Resources](/docs/guides/deploy-linodes-using-ansible/#delete-your-resources) section of the [How to use the Linode Ansible Module to Deploy Linodes](/docs/guides/deploy-linodes-using-ansible/).
