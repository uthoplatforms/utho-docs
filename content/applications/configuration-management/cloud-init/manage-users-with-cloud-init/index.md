---
slug: "Cloud Control: Mastering User Management with Cloud-Init"
title: "Use Cloud-Init to Manage Users on New Servers"
description: "Embark on a journey to streamline server setup with cloud-init as your guide. This comprehensive tutorial walks you through leveraging cloud-init for effortless management of users and user groups on fresh servers"
authors: ["Pawan Kumar"]
published: 2024-05-20
modified_by:
  name: Pawan Kumar

---
Cloud-init stands as a widely embraced solution for automating server deployments, offering seamless support across various platforms and distributions. By merging platform metadata with tailored user data scripts, cloud-init drastically simplifies the process of bootstrapping new servers.

Utilizing Utho's Metadata service, you can harness cloud-init to roll out Compute Instances effortlessly. A cloud-config user data script can encompass all the essentials for initializing instances, covering security configurations, user setups, software installations, and execution of shell scripts.

This guide delves into the intricacies of integrating user management into your cloud-init deployments. Explore cloud-config scripts designed to provision users, incorporate SSH keys, and secure your servers by disabling remote root access.

Before diving in, familiarize yourself with our guide on Using Cloud-Init to Automatically Configure and Secure Your Servers. There, you'll learn how to craft a cloud-config file, essential for following the instructions in this guide. Once you're prepared to deploy your cloud-config, refer back to the linked guide for step-by-step deployment instructions.

## Create User

In a cloud-config script, the users option takes charge of user creation and most user management features. At its core, this option can operate with just a default item, ensuring that cloud-init establishes the default user. It's advisable to maintain an entry for the default user alongside any additional users you add.


```file {title="cloud-config.yaml" lang="yaml"}
users:
  - default
```

To introduce an additional user, simply append another item to the list with at least a name field specifying the username. For instance, to create an example-user, utilize a configuration like the one below.

```file {title="cloud-config.yaml" lang="yaml" hl_lines="3"}
users:
  - default
  - name: example-user
```

While the cloud-init process sets up the user with default settings like a home directory and user group, you may want finer control over user creation, particularly if SSH access is required. Subsequent sections delve into features such as group assignment (including granting sudo access) and adding SSH keys to users. The example and accompanying explanation below outline additional useful options for managing new users.

```file {title="cloud-config.yaml" lang="yaml" hl_lines="4-7"}
users:
  - default
  - name: example-user
    gecos: Example User,600-700-8090
    shell: /bin/bash
    lock_passwd: false
    passwd: <PASSWORD_HASH>
```

This snippet creates a basic user, accessible via a username and password. Each component of the example is explained as follows:

- name: Specifies the username for the user. This field is mandatory.
gecos: Offers a comment on the user, allowing entry of GECOS information like real name and contact details. Separate pieces of information with commas.

- shell: Indicates the user's shell. While not compulsory, omitting this field may lead to unexpected shell behavior.

- lock_passwd: Determines whether to disable password logins for the user. The default is true, favoring SSH access instead. SSH keys are generally more secure, and password hashes are included in the cloud-config, making them challenging to secure.

- passwd: Sets the user's password as a password hash. To utilize this password for login, set lock_passwd to false. Generate a password hash using the command provided.

    ```command
    mkpasswd --method=SHA-512 --rounds=4096
    ```

Note: The configuration supports a plain_text_passwd option, enabling setting a user password from plain text. However, refrain from using this option in production environments, as it exposes the password to greater vulnerability.

For a comprehensive range of user configuration options, refer to cloud-init's Users and Groups module reference documentation.

## Manage and Assign Groups

Your cloud-config script can manage user groups independently using the groups option or within a users entry. The groups option grants more control over groups themselves, allowing addition of existing users like the default root user to new groups.

Under groups, a list of groups to be added to the system is specified. Simply listing the name of a group, as shown with user-group below, creates an empty group. Including a list of usernames beneath a group name, as demonstrated with admin-group below, initializes the system with those users belonging to the group.

```file {title="cloud-config.yaml" lang="yaml"}
groups:
  - admin-group:
    - root
  - user-group
```

Cloud-config also supports a groups option within each users entry. Employing this groups option offers a user-centric approach, facilitating creation and assignment of groups on a per-user basis. In the example below, a new example-group group is formed along with the user, who is subsequently assigned to that group.

```file {title="cloud-config.yaml" lang="yaml"}
users:
  - name: example-user
    groups:
      - example-group
```

By default, cloud-init automatically creates and assigns each user to a user group with the same name as the user itself. Consequently, the user example-user belongs to two groups: example-user and example-group. If you prefer not to create the default user group (example-user), you can set no_user_group: true on the user.

### Assigning Sudo Access

Cloud-init manages sudo access on users primarily through the sudo option. This option accepts a list of sudo rule strings, mirroring their appearance in the sudoers file. For comprehensive insights into sudo access and sudo rules, refer to the relevant sections in our Linux Users and Groups guide.

In the following example, a new example-user is created with sudo privileges. The single rule applied permits the user to execute any command as sudo upon entering the user's password. Additionally, this example adds the user to the sudo user group.

```file {title="cloud-config.yaml" lang="yaml"}
users:
  - name: example-user
    groups:
      - sudo
    sudo:
      - ALL=(ALL:ALL) ALL
```

Alternatively, you can utilize the following sudoers rule to grant sudo access without requiring a password. This approach is beneficial for users configured with SSH key access but lacking a password.

```file {title="cloud-config.yaml" lang="yaml"}
...
    sudo:
      - ALL=(ALL) NOPASSWD:ALL
```

## Add an SSH Key to a User

Leveraging the ssh_authorized_keys option, you can authorize a list of SSH public keys for remote user access. This method offers a more secure authentication mechanism than passwords and is thus recommended over password configuration.

If you haven't yet generated an SSH key pair, obtain one by following the relevant section in our guide on Using SSH Public Key Authentication.

Once you possess the SSH key pair, add your SSH public key to the ssh_authorized_keys list in the user configuration. In this example, example-user grants access from two SSH keys:

```file {title="cloud-config.yaml" lang="yaml"}
users:
  - name: example-user
    shell: /bin/bash
    ssh_authorized_keys:
      - <SSH_PUBLIC_KEY_FIRST>
      - <SSH_PUBLIC_KEY_SECOND>
```

With this setup, a machine possessing the corresponding SSH private key (typically where the key pair was generated) can access example-user via SSH. Authentication is performed via the SSH key, enhancing security compared to manual password entry.

## Disable Root User

From a security standpoint, it's generally recommended to disable root login via SSH. This mitigates the risk associated with exposing the root user and reduces the likelihood of unauthorized access with full root privileges.

To disable root access via SSH, you'll need to modify the SSH configuration file and restart your system's SSH service. All of these tasks can be executed using a series of shell commands, which cloud-config handles through the runcmd option. The example below demonstrates three commands to adjust the SSH service configuration:

The sed command removes any existing PermitRootLogin line from the configuration file, effectively eliminating any prior settings and bypassing the need for complex commands to identify commented-out settings.

The echo command adds a new PermitRootLogin setting to the configuration file, setting its value to no to disable root logins.

The systemctl command restarts the SSH service, ensuring the modified setting takes immediate effect.

```file {title="cloud-config.yaml" lang="yaml"}
runcmd:
  - sed -i '/PermitRootLogin/d' /etc/ssh/sshd_config
  - echo "PermitRootLogin no" >> /etc/ssh/sshd_config
  - systemctl restart sshd
```

## Verify User Configuration

After cloud-init completes the server's initialization, it's essential to confirm that your user and group configurations have been deployed correctly. A straightforward method to verify these configurations, as demonstrated throughout this tutorial, is to connect to the designated user via SSH.

For example, if your cloud-config established an example-user with an associated SSH key, you should be able to SSH into the server using that user's credentials. Replace 192.0.2.18 below with the actual IP address of the deployed server.

```command
ssh example-user@192.0.2.18
```

If you disabled remote root access, you should be able to verify that similarly, attempting to access the server as the `root` user:

```command
ssh root@192.0.2.18
```

For a more detailed verification, once logged into the server, you can utilize commands like getent and groups. The former, when paired with the passwd option and the username, provides a summary of the user's details on the system.

In the following example, you'll observe an entry for an example-user displaying details such as a GECOS comment, home directory, and an assigned shell program:

```command
sudo getent passwd example-user
```

```output
example-user:x:1000:1002:Example User,600-700-8090:/home/example-user:/bin/bash
```

However, this verification lacks confirmation of the user's group. To obtain this information, use the groups command followed by the username. The example below demonstrates this for example-user, indicating that the user belongs to a self-named user group alongside example-group.

```command
sudo groups example-user
```

```output
example-user : example-user sudo example-group
```
