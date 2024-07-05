---
slug: "How to Safeguard Your Servers: Mastering Cloud Init Configuration"
title: "Use Cloud-Init to Automatically Configure and Secure Your Servers"
description: "Discover the power of cloud-init for automating the setup and fortification of your cloud instances, streamlining configuration and security processes effortlessly"
keywords: ['cloud-init','cloudinit','metadata']
authors: ["Nathaniel Stickman"]
published: 2024-04-25
modified_by:
  name: Pawan Kumar
---
Learn to automate the setup of your new Compute Instances effortlessly with Utho Metadata service and cloud-init. This guide takes you step-by-step through creating a cloud-config file for cloud-init and deploying a Compute Instance using this configuration. It includes a range of recommended options for initializing and securing your Compute Instance, following the steps outlined in our guide on [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/). Towards the end, you'll find the [Complete Cloud-Config File](#complete-cloud-config-file) and instructions on [Deploy an Instance with User Data](#deploy-an-instance-with-user-data).

## What is cloud-init?

[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) simplifies the setup of cloud instances across various platforms and distributions by automating initialization processes. It combines instance metadata and configuration scripts (user data) to swiftly configure new servers.

Utho's [Metadata](/docs/products/compute/compute-instances/guides/metadata/) service complements cloud-init by providing an API that delivers instance and user data crucial for initializing your server. As your new instance launches, cloud-init springs into action locally, accessing this metadata and seamlessly configuring your system.

Note: Utho’s Metadata service is now accessible in specific data centers. Employ Metadata to streamline system setup by incorporating directives or scripts during Compute Instance deployment. This user data can be utilized by cloud-init, a widely adopted system initialization tool, or accessed directly via the Metadata API. For guidance on utilizing the Metadata service and to check supported regions and distributions, consult our [documentation](/docs/products/compute/compute-instances/guides/metadata/#availability).

## Create a Cloud-Config File

Utho's Metadata server feeds instance metadata to Cloud-init, furnishing vital details about the Compute Instance. This enables Cloud-init to extract specific initialization steps from provided user data, typically in the form of cloud-config scripts.

Cloud-config scripts utilize YAML formatting to express configuration and other instructions essential for Cloud-init to initialize the new instance. For comprehensive insights into cloud-config scripts, delve into our guide on [Using Cloud-Config Files to Configure a Server](/docs/products/compute/compute-instances/guides/metadata-cloud-config/).

When setting up an Utho Compute Instance, incorporate the cloud-config via Cloud Manager, Utho CLI, or Utho API during instance deployment. Explore our[Metadata](/docs/products/compute/compute-instances/guides/metadata/) guide for precise instructions on when and how to include the cloud-config user data.

To kick off, craft an initial cloud-config file to tailor your server initialization preferences. The subsequent examples designate the file as `cloud-config.yaml`. All cloud-config files commence with the following line:

    ```file {title="cloud-config.yaml" lang="yaml"}
    #cloud-config
    ```

Next, you'll customize the cloud-config to suit your server requirements. Simply follow the steps outlined in this guide to create your file, or jump ahead to the end to find [a completed Cloud-Config file](#complete-cloud-config-file).

## Update Your System

Cloud-config incorporates a module designed for managing system updates through two keys: `package_update` and `package_upgrade`. Setting both to `true` guarantees that package updates and upgrades are executed during initialization.

    ```file {title="cloud-config.yaml" lang="yaml"}
    package_update: true
    package_upgrade: true
    ```

For more assistance on dealing with packages in cloud-config, refer to the section on [Install Any Additional Required Software](#install-any-additional-required-software).

## Set the Hostname and Timezone

Cloud-config offers various options to set up server specifics. This section focuses on two key settings: configuring the timezone and hostname for your server.

The `timezone` key provides a simple way to set your server's timezone. It accepts any valid timezone for your system as input. Typically, you can find these options by running `timedatectl list-timezones` or exploring paths in `/usr/share/zoneinfo`.

     ```file {title="cloud-config.yaml" lang="yaml"}
     timezone: 'US/Central'
    ```

The `hostname` key conveniently assigns a hostname for your instance. In the example provided, cloud-init initializes the server with the hostname `examplehost`.

    ```file {title="cloud-config.yaml" lang="yaml"}
    hostname: examplehost
    ```

Note: By default, Cloud-config's `hostname` key doesn't alter the `/etc/hosts` file. This allows you to manually control the server's hostname by modifying /etc/hosts after initialization.

Alternatively, you can enable cloud-init to fully manage the /etc/hosts file by setting the manage_etc_hosts key to true. This ensures that on each boot, cloud-init synchronizes the contents of `/etc/hosts` with `/etc/cloud/templates/hosts.tmpl`.

Explore further details about the [Update Etc Hosts](https://cloudinit.readthedocs.io/en/latest/reference/modules.html#update-etc-hosts) module in cloud-init's module reference here.

## Add a Limited User Account
To enhance security and prevent remote root access, it's essential to create at least one limited user account on your instance. Cloud-config's `users` key allows you to define one or more new users for your system, with options such as `sudo` access.

Below is a typical configuration for initializing a limited user with sudo access, along with the default user, which is recommended.

      ```file {title="cloud-config.yaml" lang="yaml"}
      users:
      - default
      - name: example-user
      groups:
      - sudo
        sudo:
      - ALL=(ALL) NOPASSWD:ALL
        shell: /bin/bash
      ```

However, the example is incomplete as it lacks a `password` or SSH key for the user. While you can set a password using the passwd option, it's not recommended. Instead, setting up an SSH key for the user is preferred, as demonstrated in the following section.

For comprehensive guidance on creating and managing users with cloud-init, consult our guide on [Use Cloud-Init to Manage Users on New Servers](/docs/guides/manage-users-with-cloud-init/). This guide also covers setting up user passwords if necessary.

## Add an SSH Key to Your Limited User Account

Instead of relying on passwords, a more secure approach is to enable SSH key authentication for your limited user(s). If you haven't generated an SSH key pair yet, you can do so by following the instructions in our guide on How to [Use SSH Public Key Authentication](/docs/guides/use-public-key-authentication-with-ssh/#generate-an-ssh-key-pair).

Once you have an SSH key pair, you can add the SSH public key to a user specified in the cloud-config using the `ssh_authorized_keys` option. This option allows you to list SSH public keys that are authorized for access to this user.

    ```file {title="cloud-config.yaml" lang="yaml"}
    users:
    - default
    - name: example-user
    groups:
    - sudo
    sudo:
    - ALL=(ALL) NOPASSWD:ALL
    shell: /bin/bash
    ssh_authorized_keys:
    - <SSH_PUBLIC_KEY>
   ```

## Harden SSH

To enhance SSH connection security on your Compute Instance, it's advisable to disable password authentication and root logins via SSH. This restricts access to authorized users and SSH key pairs for authentication.

By default, the cloud-config `users` setup assumes `lock_passwd: true`, automatically disabling password authentication. Further details on user setup and managing such features are available in our guide on [Use Cloud-Init to Manage Users on New Servers](/docs/guides/manage-users-with-cloud-init/).

To disable root logins, you'll need to adjust the SSH configuration file. Although cloud-config doesn't have a direct option for this, you can leverage its versatile `runcmd` key to automate the necessary commands. Explore more about the runcmd option in our guide on [Use Cloud-Init to Run Commands and Bash Scripts on First Boot](/docs/guides/run-commands-and-bash-scripts-with-cloud-init).

The example below demonstrates removing any existing `PermitRootLogin` configuration and adding a new configuration to disable PermitRootLogin. The final command restarts the `sshd` service to apply the changes.

    ```file {title="cloud-config.yaml" lang="yaml"}
    runcmd:
    - sed -i '/PermitRootLogin/d' /etc/ssh/sshd_config
    - echo "PermitRootLogin no" >> /etc/ssh/sshd_config
    - systemctl restart sshd
    ```

Note: In the example provided, it's assumed that your system utilizes `systemctl` to handle the SSH service. However, this isn't universal across all distributions. Depending on how your system manages the SSH service, you may need to adjust the commands accordingly.

For example, older systems such as CentOS 6, Debian 7, and Ubuntu 14.04 use `service` instead of `systemctl`. Therefore, you'd need to substitute the systemctl command in the example with the following:

    ```command
    service sshd restart
    ```

## Install Any Additional Required Software

Cloud-config's `packages` key enables you to automate software installation and management during server initialization. For comprehensive coverage of cloud-init's package management capabilities and practical examples, refer to our guide on [Use Cloud-Init to Install and Update Software on New Servers](/docs/guides/install-and-update-software-with-cloud-init/).

As an introductory example, the snippet below demonstrates how to install a set of software packages during instance initialization. Specifically, it installs software for a LEMP web stack (NGINX, MySQL, and PHP), a popular configuration for web applications. To delve deeper into LEMP stacks, check out our guide on [Install a LEMP Stack](/docs/guides/how-to-install-a-lemp-stack-on-ubuntu-22-04/).

    ```file {title="cloud-config.yaml" lang="yaml"}
    packages:
    - mysql-server
    - nginx
    - php
    ```

Note: Software packages may have varying names depending on the distribution you're using. Consult your distribution's package repository to determine the exact package names for the software you intend to install.

## Complete Cloud-Config File

Below is a comprehensive example of a cloud-config file, encompassing all initialization options discussed in this tutorial. You can utilize this as a foundation for initializing your own Compute Instances. For instance, by omitting the `packages` section and adjusting the limited user and server details, you can create a script aligning with our [Setting Up and Securing a Compute Instance](/docs/products/compute/compute-instances/guides/set-up-and-secure/) guide. Feel free to incorporate additional features to tailor the instance according to your requirements.

   ```file {title="cloud-config.yaml" lang="yaml" hl_lines="6,13,20,21,31,32,33"}
   #cloud-config

   # Configure a limited user
   users:
   - default
   - name: example-user
   groups:
   - sudo
   sudo:
   - ALL=(ALL) NOPASSWD:ALL
   shell: /bin/bash
   ssh_authorized_keys:
   - "SSH_PUBLIC_KEY"

   # Perform System Updates
   package_update: true
   package_upgrade: true

   # Configure server details
   timezone: 'US/Central'
   hostname: examplehost

   # Harden SSH access
   runcmd:
  - sed -i '/PermitRootLogin/d' /etc/ssh/sshd_config
  - echo "PermitRootLogin no" >> /etc/ssh/sshd_config
  - systemctl restart sshd

  # Install additional software packages
  packages:
  - nginx
  - mysql-server
  - php
  ```

## Deploy an Instance with User Data

There are three paths to deploy a new Compute Instance using your cloud-config initialization script. These options are summarized below, with links to relevant deployment guides. For more coverage of cloud-init deployments, review our [Overview of the Metadata Service](/docs/products/compute/compute-instances/guides/metadata/) guide.

- **Cloud Manager**: Easily configure and deploy new instances using a web browser. Follow our [Create a Compute Instance](/docs/products/compute/compute-instances/guides/create/). The guide includes a section on how to **Add User Data**, where you can input your cloud-config content.

- **Utho CLI**: The command-line tool offers a command for creating a new Compute Instance, which can include a `--metadata.user_data` option with your cloud-config script. To learn more about using the Utho CLI, consult our [Getting Started with the Utho CLI](/docs/products/tools/cli/get-started/) and [Utho CLI Commands for Compute Instances](/docs/products/tools/cli/guides/Utho-instances/) guides.

Below are example commands to deploy an instance from a cloud-config file. These commands assume you've set up the Utho CLI tool and your cloud-config is stored as `cloud-config.yaml` in the current directory. Refer to the linked guide for more details on the other options used in this example command.

The CLI command necessitates encoding the cloud-config as a base64 string, achievable using the `cat cloud-config.yaml | base64 -w 0` command. The first command below accomplishes this and assigns the value to a shell variable for convenience.

    ```command
    cloudconfigvar="$(cat cloud-config.yaml | base64 -w 0)"

    Utho-cli Uthos create \
      --label cloud-init-example \
      --region us-iad \
      --type g6-nanode-1 \
      --image Utho/ubuntu22.04 \
      --root_pass EXAMPLE_ROOT_PASSWORD \
      --metadata.user_data "$cloudconfigvar"
    ```

- **Utho API**: The `instances/` endpoint of the API offers a `metadata.user_data` option for inserting a cloud-config. With this, you can initialize a new Compute Instance through a straightforward POST request. Explore the Utho API further in our documentation on the Utho API and the Utho Instances API](/docs/api/Utho-instances/) documentation.

The Utho API's adaptability allows for various methods to deploy a server. Below outlines a simple command-line approach. This example can be easily adjusted for other scenarios.

Create a payload file. This file simplifies crafting and reusing data for a `POST` request. You can use any text editor to create it, but the example below uses the > command for simplicity:

        ```command
        cat > cloud-init-example-deployment.json <<'EOF'
        {
          "label": "cloud-init-example",
          "region": "us-iad",
          "type": "g6-nanode-1",
          "image": "Utho/ubuntu22.04",
          "root_pass": "EXAMPLE_ROOT_PASSWORD",
          "metadata": {
            "user_data": "<INSERT_CLOUD_CONFIG>"
          }
        }
        EOF
        ```

To incorporate the cloud-config as `metadata.user_data`, encode it as a base64 string, which is required by the API, similar to the CLI. Below, this is achieved using the command `cat cloud-config.yaml | base64 -w 0`, and the result is stored in a shell variable for convenience.

        ```command
        cloudconfigvar="$(cat cloud-config.yaml | base64 -w 0)"
        sed -i "s,<INSERT_CLOUD_CONFIG>,$cloudconfigvar," cloud-init-example-deployment.json
        ```

Alternatively, you can copy the base64 string and paste it into the user_data field while opening the file.

Then, execute a POST request to the instances API endpoint. Include your Utho API personal access token (explained in the linked guide) as an Authorization: Bearer header.

        ```command
        curl -H "Content-Type: application/json" \
          -H "Authorization: Bearer API_ACCESS_TOKEN" \
          -X POST \
          -d @cloud-init-example-deployment.json \
          https://api.Utho.com/v4/Utho/instances
        ```
