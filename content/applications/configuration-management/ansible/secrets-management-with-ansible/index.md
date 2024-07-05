---
slug: Managing Secrets with Ansible
description: "Ansible automates server tasks but often involves sensitive data like passwords. Secure these with secrets management in Ansible. Learn how in this tutorial"
keywords: ['ansible secrets manager','ansible vault tutorial','ansible secrets best practices']
published: 2024-05-16
modified_by:
  name: Pawan Kumar
title: "Secrets Management with Ansible"
authors: ["Pawan Kumar"]
tags: ["saas"]
---

Ansible excels at automating server management tasks with its versatile playbooks and resource organization features. Yet, these operations often require handling sensitive data like passwords and API keys. To ensure security within your Ansible setup, implement a secrets management process. This keeps your secrets safe while Ansible continues to streamline server tasks. This tutorial guides you through various methods of secrets management, helping you choose the most suitable approach for your needs.

## Before You Begin

If you haven't already, create a Utho account. Check out our Getting Started with Utho guide.

Follow our guide on [Getting Started With Ansible: Basic Installation and Setup](/docs/guides/getting-started-with-ansible/). Specifically, go through the sections on setting up a control node and managed nodes, configuring Ansible, and creating an Ansible inventory.

Consult our guide Automate Server Configuration with Ansible Playbooks for an overview of Ansible playbooks and their operations..

## Secrets in Ansible

Items like access tokens, API keys, and passwords for databases and systems.

When managing nodes with Ansible, integrating secrets is often necessary. Typically, these secrets are included directly within Ansible playbooks, but this exposes them to potential interception and misuse.

To fortify your secrets, implementing secrets management within Ansible playbooks is essential. This involves securely storing secrets, with various methods balancing accessibility and security.

## Managing Secrets in Ansible

Numerous options are available for managing secrets within Ansible playbooks, tailored to your specific setup. Considerations include the level of accessibility required and the desired level of security.

The following sections outline several valuable options for secrets management with Ansible, catering to diverse use cases from manual to automated processes.

All examples herein assume an Ansible setup with one control node and two managed nodes, identified by the example IP addresses `192.0.2.3` and `192.0.2.4`, respectively, listed in an `ansiblenodes` group in the control node's Ansible inventory.

### Using Prompts to Manually Enter Secrets

Ansible playbooks offer the option to prompt users for variables, presenting an avenue for managing secrets within your Ansible environment.

This method entails configuring Ansible playbooks to solicit manual input for secrets, without persisting them on the system, ensuring their safeguarding. Although straightforward, it limits automation integration and introduces potential risks from manual handling of secrets.

Below is an example Ansible playbook excerpt from our [Automate Server Configuration with Ansible Playbooks](/docs/guides/running-ansible-playbooks/) guide. This playbook creates a new non-root user on managed nodes, utilizing the `vars_prompt` option to solicit a password from the user, which Ansible then hashes and deploys to each managed node.

Note: This playbook assumes SSH public key presence on your control node for secure passwordless connections to the new user in the future. Learn more in our guide [Using SSH Public Key Authentication](/docs/guides/use-public-key-authentication-with-ssh/).

Additionally, this tutorial assumes password protection for your control node's SSH key, hence includes the `--ask-pass` option in some Ansible playbook commands below. If your SSH key lacks password protection, omit the `--ask-pass` option from the commands provided in this tutorial.

```file {title="add_limited_user.yml" lang="yml"}
---
- hosts: ansiblenodes
  remote_user: root
  vars:
    limited_user_name: 'example-user'
  vars_prompt:
    - name: limited_user_password
      prompt: Enter a password for the new non-root user
  tasks:
    - name: "Create a non-root user"
      user: name={{ limited_user_name }}
            password={{ limited_user_password | password_hash }}
            shell=/bin/bash
    - name: Add an authorized key for passwordless logins
      authorized_key: user={{ limited_user_name }} key="{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
    - name: Add the new user to the sudoers list
      lineinfile: dest=/etc/sudoers
                  regexp="{{ limited_user_name }} ALL"
                  line="{{ limited_user_name }} ALL=(ALL) ALL"
                  state=present
```

Before running the playbook, ensure you're in the playbook's directory, then execute the following command:

```command {title="Ansible Control Node"}
ansible-playbook --ask-pass add_limited_user.yml
```

First, Ansible prompts for the SSH password, followed by a prompt for the password for the new user. The output should resemble the example shown below:

```output
SSH password:
Enter a password for the new non-root user:

PLAY [ansiblenodes] ************************************************************

TASK [Gathering Facts] *********************************************************
ok: [192.0.2.2]
ok: [192.0.2.1]

TASK [Create a non-root user] **************************************************
changed: [192.0.2.1]
changed: [192.0.2.2]

TASK [Add remote authorized key to allow future passwordless logins] ***********
ok: [192.0.2.1]
ok: [192.0.2.2]

TASK [Add normal user to sudoers] **********************************************
ok: [192.0.2.1]
ok: [192.0.2.2]

PLAY RECAP *********************************************************************
192.0.2.1              : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
192.0.2.2              : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### Using the Ansible Vault to Manage Secrets

Ansible offers a tool called Ansible Vault, designed to streamline secrets management. The Vault encrypts information, enabling its safe use within Ansible playbooks.

With proper setup, Ansible Vault ensures both security and accessibility of secrets. These secrets are encrypted, requiring a password for access, yet remain accessible to Ansible. Utilizing a password file provides Ansible with all necessary credentials for automated operations.

The vault password can be input either manually or automatically through a password file. Alternatively, you can utilize an external password manager or implement a script to retrieve the password.

In this example using Ansible Vault, [rclone](https://rclone.org/) is deployed to the managed nodes and configured to connect to a Utho Object Storage instance. The secrets required are the access keys for the object storage instance.

To participate, you must establish a Utho Object Storage instance with access keys and at least one bucket. Instructions for this setup can be found in our guide Object Storage - Get Started.

Generate a file containing the access keys for your Utho Object Storage instance. Use the command below, replacing the placeholders with your actual object storage keys:

    ```command {title="Ansible Control Node"}
    echo "s3_access_token: <S3_ACCESS_TOKEN>" > s3_secrets.enc
    echo "s3_secret_token: <S3_SECRET_TOKEN>" >> s3_secrets.enc
    ansible-vault encrypt s3_secrets.enc
    ```

Before encrypting the file's contents, Ansible Vault will prompt you to create a vault password.

    ```output
    New Vault password:
    Confirm New Vault password:
    Encryption successful
    ```

Create a password file in the directory where you plan to create the Ansible playbook. This file should contain only the password for your encrypted secrets file. The following command assumes your password is `examplepassword`:

    ```command {title="Ansible Control Node"}
    echo "examplepassword" > example.pwd
    ```

Create a new Ansible playbook with the following content. This playbook connects to the non-root users created in the previous section of this tutorial. It installs rclone, creates a configuration file for it, and inserts the access keys from the `s3_secrets.enc` file into the configuration.

    ```file {title="set_up_rclone.yml" lang="yml"}
    ---
    - hosts: ansiblenodes
      remote_user: 'example-user'
      become: yes
      become_method: sudo
      vars:
        s3_region: 'us-southeast-1'
      tasks:
        - name: "Install rclone"
          apt:
            pkg:
              - rclone
            state: present
            update_cache: yes
        - name: "Create the directory for the rclone configuration"
          file:
            path: "/home/example-user/.config/rclone"
            state: directory
        - name: "Create the rclone configuration file"
          copy:
            dest: "/home/example-user/.config/rclone/rclone.conf"
            content: |
              [Uthos3]
              type = s3
              env_auth = false
              acl = private
              access_key_id = {{ s3_access_token }}
              secret_access_key = {{ s3_secret_token }}
              region = {{ s3_region }}
              endpoint = {{ s3_region }}.Uthoobjects.com
    ```

Execute the Ansible playbook. The playbook command includes variables from the secrets file using the `-e` option and obtains the password for decryption from the `--vault-password-file` flag. Additionally, the `--ask-become-pass` option prompts for the limited user's sudo password.

    ```command {title="Ansible Control Node"}
    ansible-playbook -e @s3_secrets.enc --vault-password-file example.pwd --ask-pass --ask-become-pass set_up_rclone.yml
    ```

The outcome should resemble:

    ```output
    SSH password:
    BECOME password[defaults to SSH password]:

    PLAY [ansiblenodes] ************************************************************

    TASK [Gathering Facts] *********************************************************
    ok: [192.0.2.2]
    ok: [192.0.2.1]

    TASK [Install rclone] **********************************************************
    changed: [192.0.2.1]
    changed: [192.0.2.2]

    TASK [Create the directory for the rclone configuration] ***********************
    changed: [192.0.2.2]
    changed: [192.0.2.1]

    TASK [Create the rclone configuration file] ************************************
    changed: [192.0.2.2]
    changed: [192.0.2.1]

    PLAY RECAP *********************************************************************
    192.0.2.1              : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    192.0.2.2              : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

To confirm the expected functionality, log into any of the managed nodes as the non-root user. Then, utilize the following command to list the buckets on your Utho Object Storage instance:

    ```command {title="Ansible Managed Node"}
    rclone lsd Uthos3:
    ```

Each bucket should display something similar to the following, with `ansible-test-bucket` representing the bucket's name:

    ``` output
    -1 2022-12-08 00:00:00        -1 ansible-test-bucket
    ```

### Using a Secrets Manager

Specialized solutions are available for managing secrets, with many password managers capable of handling secrets for your Ansible playbooks. Functionally, these tools often operate similarly to Ansible Vault. While they are external to Ansible, several have official or community-supported plugins for seamless integration.

The primary advantage of using an external secrets management solution lies in leveraging a tool already widely adopted within your team or organization. Although Ansible Vault provides default integration with Ansible, it may not be extensively used for password management within your organization.

One popular solution for secret management is [HashiCorp's Vault](https://www.vaultproject.io/), offering centralized secrets management with a dynamic infrastructure to safeguard passwords, keys, and other sensitive data.

Ansible maintains a plugin for interfacing with HashiCorp's Vault, known as the [`hashi_vault` plugin](https://docs.ansible.com/ansible/latest/collections/community/hashi_vault/docsite/about_hashi_vault_lookup.html).

The following steps guide you through an example using HashiCorp's Vault with Ansible, achieving similar outcomes as the previous example for easy comparison:

Refer to our guide on [Setting Up and Using a Vault Server](/docs/guides/how-to-setup-and-use-a-vault-server/) to follow along. Upon completion, you should have HashiCorp's Vault installed, a vault server operational and unsealed, and be logged into the vault.

Ensure that the key-value (`kv`) engine is enabled for the `secret` path:

    ```command {title="Vault Server"}
    vault secrets enable -path=secret/ kv
    ```

    ```output
    Success! Enabled the kv secrets engine at: secret/
    ```

Insert the access keys for your Utho Object Storage instance into the `secret/s3` path in the vault. Replace the placeholders below with your corresponding keys:

    ```command {title="Vault Server"}
    vault kv put secret/s3 s3_access_token=<S3_ACCESS_TOKEN> s3_secret_token=<S3_SECRET_TOKEN>
    ```

    ```output
    Success! Data written to: secret/s3
    ```

On your Ansible control node, install `hvac` via `pip` to utilize the hashi_vault plugin referenced in the Ansible playbook provided below.

    ```command {title="Ansible Control Node"}
    pip install hvac
    ```

Create a new Ansible playbook with the following content. This playbook mirrors the one constructed in the previous section, which installs and configures `rclone` to connect to a Utho Object Storage instance. However, this version retrieves secrets from a HashiCorp Vault instead of an Ansible vault:

Replace both occurrences of `<HASHI_VAULT_IP>` with the IP address of your HashiCorp Vault server. Similarly, replace both instances of `<HASHI_VAULT_TOKEN>` with your login token for the HashiCorp Vault server.

    ```file {title="another_rclone_setup.yml" lang="yml"}
    ---
    - hosts: ansiblenodes
      remote_user: 'example-user'
      become: yes
      become_method: sudo
      vars:
        s3_region: 'us-southeast-1'
      tasks:
        - name: "Install rclone"
          apt:
            pkg:
              - rclone
            state: present
            update_cache: yes
        - name: "Create the directory for the rclone configuration"
          file:
            path: "/home/example-user/.config/rclone"
            state: directory
        - name: "Create the rclone configuration file"
          copy:
            dest: "/home/example-user/.config/rclone/rclone.conf"
            content: |
              [Uthos3]
              type = s3
              env_auth = false
              acl = private
              access_key_id = {{ lookup('hashi_vault', 'secret=secret/s3:s3_access_token token=<HASHI_VAULT_TOKEN> url=http://<HASHI_VAULT_IP>:8200')}}
              secret_access_key = {{ lookup('hashi_vault', 'secret=secret/s3:s3_secret_token token=<HASHI_VAULT_TOKEN> url=http://<HASHI_VAULT_IP>:8200')}}
              region = {{ s3_region }}
              endpoint = {{ s3_region }}.Uthoobjects.com
    ```

Execute the Ansible playbook, entering the necessary passwords when prompted:

    ```command {title="Ansible Control Node"}
    ansible-playbook --ask-pass --ask-become-pass another_rclone_setup.yml
    ```

The outcome should resemble:

    ```output
    SSH password:
    BECOME password[defaults to SSH password]:

    PLAY [ansiblenodes] ********************************************************

    TASK [Gathering Facts] *****************************************************
    ok: [192.0.2.2]
    ok: [192.0.2.1]

    TASK [Install rclone] ******************************************************
    changed: [192.0.2.2]
    changed: [192.0.2.1]

    TASK [Create the directory for the rclone configuration] *******************
    changed: [192.0.2.2]
    changed: [192.0.2.1]

    TASK [Create the rclone configuration file] ********************************
    changed: [192.0.2.1]
    changed: [192.0.2.2]

    PLAY RECAP *****************************************************************
    192.0.2.1              : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    192.0.2.2              : ok=4    changed=3    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

Just as in the previous section, you can verify the setup by logging into one of the managed nodes and executing an `rclone ls` command, such as `rclone lsd Uthos3:`.

## Conclusion

You now possess various options to ensure secure secrets within your Ansible setup. Your choice depends on factors like scale and accessibility. Manual entry is straightforward for small projects and teams, while Ansible Vault offers comprehensive security. However, an external solution might better suit your team and organization.

For further learning on Ansible and optimizing server task automation, explore more of our [guides on Ansible](/docs/guides/applications/configuration-management/ansible/).
