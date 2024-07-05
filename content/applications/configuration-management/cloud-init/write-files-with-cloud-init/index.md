---
slug: "Unlocking Cloud Power: Master the Art of Writing Files with Cloud-Init"
title: "Cloud-Init Magic: Scribble to Files with Style"
description: "Discover the ultimate hack for automating file writing and tweaking on your fresh server setups with the ingenious power of cloud-init"
keywords: ['cloud-init','cloudinit','write files','sed']
authors: ["Pawan Kumar"]
published: 2024-03-26
modified_by:
  name: Pawan Kumar
---

[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html)simplifies server initialization across various platforms and distributions, making it a go-to tool in the industry. It receives metadata from the cloud platform to streamline server deployment, while custom user data empowers you to script nearly every aspect of initialization.

This guide walks you through using a cloud-config script to effortlessly write files onto your server during initialization. Automate file creation and editing to ensure your software and system configurations are precisely tailored from the get-go.

Before getting started, review our guide on how to [Use Cloud-Init to Automatically Configure and Secure Your Servers](/docs/guides/configure-and-secure-servers-with-cloud-init/). There, you can see how to create a cloud-config file, which you need to follow along with this guide. When you are ready to deploy your cloud-config, the guide linked above details how.

To begin, check out our guide on Using [Use Cloud-Init to Automatically Configure and Secure Your Servers](/docs/guides/configure-and-secure-servers-with-cloud-init/). For instructions on creating a cloud-config file, which you'll need for this process. Once your cloud-config is ready for deployment, follow the detailed steps in the linked guide to proceed.

## Write to a File

Cloud-init offers a convenient module for writing files called `write_files`, which you can use within your cloud-config script. This module provides an easy way to create a new file or replace an existing one during server initialization.

When you use the `write_files` option, it automatically handles creating a new file or updating an existing one at the specified location. Additionally, if any parent directories of the target file don't exist, cloud-init takes care of creating them.

Here's an example demonstrating how to create an HTML file using `write_files`:

```file {title="cloud-config.yaml" lang="yaml"}
write_files:
  - path: /var/www/html/example.com/index.html
    content: |
      <html>
      <body>
        <h1>Hello, World!</h1>
        <p>Welcome to the example web page!</p>
      </body>
      </html>
    owner: 'root:root'
    permissions: '0644'
```

Let's break down what the example write_files configuration does:

- `path`: This specifies the location where the file will be created. If a file already exists at this location, it will be overwritten. Cloud-init will also create any necessary parent directories. This is the only required option for write_files.

- `content`: Here, you define the content of the file. It can be a single line or, as shown above, you can use YAML formatting for multi-line content. If you omit the content option, an empty file will be created.

- `owner`: Optionally, you can specify a user and/or group to own the file. By default, it's set to `root:root`. If you want to use a user/group created within the cloud-config script, you should set defer: true to ensure the user/group is created before the file.

- `permissions`: This option lets you set the file's permissions using octal notation, similar to `chmod`. For example, permissions 0### sets permissions where ### is an octal number. Here, the permissions give read-write access to the owner (`6--`), read access to the user's group (`-4-`), and read access to all other users (`--4`).

You can also use the `defer` option to delay the creation of the file until the final stage of cloud-init's initialization. This ensures that the file is created only after all necessary users and software are installed.

Here's another example demonstrating the `defer` option by creating an [Apache Web Server](/docs/guides/how-to-install-apache-web-server-ubuntu-18-04/) configuration. Using defer ensures that Apache is installed and the Apache user (usually `www-data` on Debian and Ubuntu) is created before the file is created.

```file {title="cloud-config.yaml" lang="yaml"}
write_files:
  - path: /etc/apache2/sites-available/example.com.conf
    content: |
      <VirtualHost *:80>
          ServerAdmin webmaster@example.com
          ServerName example.com
          ServerAlias www.example.com
          DocumentRoot /var/www/example.com/html/
          ErrorLog /var/www/example.com/logs/error.log
          CustomLog /var/www/example.com/logs/access.log combined
      </VirtualHost>
    owner: 'www-data:www-data'
    permissions: '0640'
    defer: true
```

If you don't include the `defer: true` option as shown above, you'll encounter an error because the `www-data` user won't exist when cloud-init tries to create the file.

## Modify a File

When you require file modification, cloud-init offers several approaches to choose from:

- Utilizing the `write_files` option, basic file modifications can be accomplished using its `append: true` feature. For example, the following illustration demonstrates how to adjust the SSH service configuration by appending a `PermitRootLogin` no rule:

    ```file {title="cloud-config.yaml" lang="yaml"}
    write_files:
      - path: /etc/ssh/sshd_config
        content: PermitRootLogin no
        append: true
    ```

Alternatively, if you need to make modifications that `write_files` cannot handle, you'll have to recreate the files entirely. In this scenario, you'll need to include the entire configuration along with your desired changes in your cloud-config script.

- A more straightforward and sustainable approach is to utilize cloud-init's runcmd option to execute `sed` commands on the server. sed allows for text editing through shell commands, while runcmd enables the execution of shell commands from a cloud-init script. Explore more about using `runcmd` in our guide Use [Use Cloud-Init to Run Commands and Bash Scripts on First Boot](/docs/guides/run-shell-commands-with-cloud-init/), and delve into `sed` in our guide [Manipulate Text from the Command Line with sed](/docs/guides/manipulate-text-from-the-command-line-with-sed/).

With the `runcmd` option, you can provide a list of shell commands. In the forthcoming example, two shell commands are executed to modify the SSH service configuration, similar to the previous example. However, `sed` allows you to replace existing settings rather than solely appending new ones.

    ```file {title="cloud-config.yaml" lang="yaml"}
    runcmd:
      - sed -i -e 's/PermitRootLogin\s*yes/PermitRootLogin no/g' /etc/ssh/sshd_config
      - sed -i -e 's/#*\s*PermitRootLogin/PermitRootLogin/g' /etc/ssh/sshd_config
    ```

Each command listed in the `runcmd` section above modifies the `/etc/ssh/sshd_config` file. These two commands work together to change the `PermitRootLogin` setting to no and ensure it's not commented out.

The first `sed` command replaces `PermitRootLogin` followed by any number of spaces and `yes` with just `PermitRootLogin no`. The second command removes the comment marker (`#`) from the beginning of any instances of `PermitRootLogin`.

## Verify File Contents

After your instance is up and operational, employ the `cat` command to confirm that files are composed as anticipated. Simply execute `cat` followed by the file's location to inspect the contents specified in the cloud-config.

```command
cat /var/www/html/example.com/index.html
```

```output
<html>
<body>
  <h1>Hello, World!</h1>
  <p>Welcome to the example web page!</p>
</body>
</html>
```

You can apply a similar approach to check for file modifications. However, you can streamline this process by filtering the output to display only matching terms. For instance, you can filter the contents of the `sshd_config` file to show only the lines where cloud-init applied changes, specifically those containing `PermitRootLogin`.

```command
cat /etc/ssh/sshd_config | grep PermitRootLogin
```

```output
PermitRootLogin no
```

To confirm file permissions, utilize the `stat` command. For example, in the case of the `example.com.conf` file mentioned earlier, ensure that the file is owned by www-data and has the permission `0640`.

```command
sudo stat /etc/apache2/sites-available/example.com.conf
```

```output
  File: /etc/apache2/sites-available/example.com.conf
  Size: 284       	Blocks: 8          IO Block: 4096   regular file
Device: 800h/2048d	Inode: 261541      Links: 1
Access: (0640/-rw-r-----)  Uid: (   33/www-data)   Gid: (   33/www-data)
...
```
