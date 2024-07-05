---
slug: "Unlocking the Magic: A Novice's Journey into Salt"
description: 'Explore the fundamental elements, functionalities, and setup options of SaltStack, tailored for newcomers to effortlessly navigate and harness its power.'
keywords: ["salt", "automation", "saltstack", "configuration management"]
modified: 2024-03-27
modified_by:
  name: Utho
published: 2018-10-16
title: A Beginner's Guide to Salt
tags: ["automation","salt"]
authors: ["Utho"]
---

[Salt](https://www.saltproject.io) also known as *SaltStack*, is a Python-based system for configuration management and orchestration. It operates on a master/client model, where a designated Salt master server oversees one or more Salt minion servers. Salt primarily performs two tasks:

- Allows executing commands across a group of minions.

- Applies Salt *states* to manage configurations across minions.

This guide serves as an introduction to the fundamental concepts used by Salt to accomplish these tasks.

## Masters and Minions

The Salt master serves as the command-and-control center for its minions. It's where Salt's remote execution commands originate. For instance, you can use this command to check the current disk usage on all minions managed by the master:

    salt '*' disk.usage

Numerous other commands are accessible. This command installs NGINX on the minion labeled as `webserver1`

    salt 'webserver1' pkg.install nginx

Salt minions are the servers responsible for running your applications and services. Each minion is assigned an ID, which can be automatically generated from the minion's hostname. The Salt master can use this ID to target commands to specific minions.

{{< note respectIndent=false >}}
When using Salt, it's recommended to configure and manage your minion servers primarily from the master, rather than directly logging into them via SSH or any other protocol.
{{< /note >}}

To enable all of these functions, the Salt master server operates a daemon called **Salt-master**, while the **Salt minion** servers run a daemon known as salt-minion.

### Authentication

Communication between the master and minions happens over the [ZeroMQ](https://docs.saltproject.io/en/latest/topics/development/topology.html) transport protocol, and all communication is encrypted using public/private keypairs. When Salt is initially installed on a minion, it generates a keypair. The minion then sends its public key to the master. To establish communication, you'll need to accept the minion's key from the master. After that, communication can proceed between the two.

## Remote Execution

Salt provides an [very wide array](https://docs.saltproject.io/en/latest/ref/modules/all/) of remote execution modules, which you can explore here. An execution module is a set of related functions that you can execute on your minions **from the master**. For example:

    salt 'webserver1' npm.install gulp

In this command, `npm` serves as [the module](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.npm.html), while `install` is [the function](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.npm.html#salt.modules.npm.install). It installs the Gulp Node.js package via the Node Package Manager (NPM). Additionally, the `npm` module includes other functions for tasks such as uninstalling NPM packages and listing installed ones.

The execution modules provided by Salt encompass various system administration tasks typically carried out in a shell, including, but not limited to:

- Establishing and overseeing [system users](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.user.html)

- Setting up and removing [software](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.pkg.html)

- Modifying or crafting configuration [files](https://docs.saltproject.io/en/latest/ref/modules/all/salt.modules.file.html#module-salt.modules.file)

{{< note respectIndent=false >}}
You also have the option to [write your own](/docs/guides/create-a-salt-execution-module/) execution modules.
{{< /note >}}

### cmd.run

The `cmd.run` function is employed to execute arbitrary commands on your minions from the master.

    salt '*' cmd.run 'ls -l /etc'

This command would retrieve the contents of `/etc` on each minion.

{{< note respectIndent=false >}}

Whenever feasible, utilizing execution modules is preferable to "shell out" with `cmd.run`.

{{< /note >}}

## States, Formulas, and the Top File

The previous section covered using remote execution to execute specific actions on a minion. With remote execution, you administer a minion by entering a series of such commands.

Salt provides another approach to configure a minion by defining the desired state of a minion. This configuration method is known as a Salt state, falling under the broader category of configuration management.

The difference between these two approaches is subtle. For instance, consider installing NGINX in each method:

-   **Remote execution**: "Install NGINX on the minion"

-   **Configuration management**: "Ensure NGINX is installed on the minion"

Salt states are outlined in state files. Once you've defined your states, you apply them to a minion. Salt examines the state file and determines the actions required to ensure the minion meets the state's specifications.

{{< note respectIndent=false >}}
This can lead to the same command being executed as with remote execution, but not always. Take the NGINX example: if Salt detects that NGINX was previously installed, it won't trigger the package manager again when applying the state.
{{< /note >}}

### Anatomy of a State

Here's a simple state file that makes sure:

rsync and curl are installed
NGINX is installed
NGINX starts and runs automatically at boot:

     {{< file "/srv/salt/webserver_setup.sls">}}
     network_utilities:
     pkg.installed:
     - pkgs:
     - rsync
     - curl

     nginx_pkg:
     pkg.installed:
     - name: nginx

     nginx_service:
     service.running:
     - name: nginx
     - enable: True
     - require:
     - pkg: nginx_pkg
    {{< /file >}}

State files end with the extension `.sls` (SaLt State). They can contain one or more state declarations, which are the main sections of the file (`network_utilities`, `nginx_pkg`, and `nginx_service` in the example above). The IDs of state declarations are flexible, so you can name them as you like.

{{< note respectIndent=false >}}
If you name the ID the same as the relevant installed package, you don't need to specify the `- name` option, as it will be inferred from the ID. For example, this snippet also installs NGINX:

    {{< file >}}
    nginx:
    pkg.installed
    {{< /file >}}

The same name/ID inference convention applies to other Salt modules as well.
{{< /note >}}

State declarations contain *state modules*, which are separate from execution modules but often perform similar tasks. For example, there's a `pkg` state module that mirrors the functionalities of the `pkg` execution module, including functions like `pkg.installed` and `pkg.install`. Just like execution modules, Salt provides a [wide array](https://docs.saltproject.io/en/latest/ref/states/all/) of state modules for your use.

{{< note respectIndent=false >}}
State declarations might not be applied in the order they appear in a state file. However, you can specify dependencies between declarations using the `require` option. In the example above, Salt won't attempt to start and enable NGINX until it's installed.
{{< /note >}}

State files are essentially collections of dictionaries, lists, strings, and numbers that Salt interprets. By default, Salt uses the [YAML syntax](https://docs.saltproject.io/en/latest/topics/yaml/) for representing states.

Typically, state files are stored on the Salt master's filesystem, but they can also be located in [other fileserver locations](https://docs.saltproject.io/en/latest/ref/file_server/backends.html), such as a Git repository (specifically, on GitHub).

### Applying a State to a Minion

To apply a state to a minion, utilize the `state.apply` function from the master:

    salt `webserver1` state.apply webserver_setup

This command applies the example state `webserver_setup.sls` to a minion named `webserver1`. When applying the state, you don't need to include the `.sls` suffix. All state declarations in the file are applied.

### Salt Formulas


Formulas are essentially bundles of states that collectively configure an application or system component on a minion. Typically, formulas are organized across multiple `.sls` files. Splitting a formula's states across different files can help keep your work organized. State declarations can reference and include declarations from other files.

Generic formulas are often shared on GitHub for others to use. The SaltStack organization maintains a [collection of popular formulas](https://github.com/saltstack-formulas). Salt's documentation includes [a guide on using a formula](https://docs.saltproject.io/en/latest/topics/development/conventions/formulas.html) hosted on GitHub.

The definition of a formula is somewhat flexible, and there's no strict structure mandated by Salt.

### The Top File

Apart from manually applying states to minions, Salt offers an automated method for mapping which states should be applied to different minions. This mapping is known as the *top file*.

Here's a basic top file:

    {{< file "/srv/salt/top.sls" >}}
    base:
    '*':
    - universal_setup

    'webserver1':
    - webserver_setup
    {{< /file >}}

In this example, `base` denotes the default Salt [*environment*](https://docs.saltproject.io/en/latest/ref/states/top.html#environments). You can define multiple environments to correspond with different stages of your workflow, such as development, QA, and production.

Within each environment, you specify groups of minions, with corresponding states listed for each group. In the provided top file, the `universal_setup` state is designated for all minions (`'*'`), while the `webserver_setup` state is assigned specifically to the webserver1 minion.

When you execute the `state.apply` function without any arguments, Salt examines the top file and applies all states listed in it based on your defined mappings.

    salt '*' state.apply

{{< note respectIndent=false >}}
This action is commonly referred to as a [*highstate*](https://docs.saltproject.io/en/latest/topics/tutorials/states_pt1.html#running-highstate).
{{< /note >}}

### Advantages of States and Configuration Management

Using states to define your configurations offers several benefits for system administration:

- Simplified Setup: States minimize human error by eliminating the need to manually enter commands one by one.

- Idempotent Operations: Applying a state to a minion multiple times typically doesn't cause any changes beyond the initial application. Salt recognizes when a state has already been implemented on a minion and avoids unnecessary actions.

- Efficient Updates: When you update a state file and apply it to a minion, Salt detects and applies only the changes, making system updates more efficient.

- Reusability: States can be reused and applied to multiple minions, ensuring consistent configurations across different servers.

- Version Control Integration: State files can be stored in a version control system, allowing you to track changes to your systems over time. This helps with maintaining system integrity and troubleshooting.

## Targeting Minions

You can use shell-style globbing to match your minions' IDs. This works both at the command line and in the top file.

For instance, these examples would apply the `webserver_setup` state to all minions whose ID starts with webserver (like `webserver1`, `webserver2`, etc.):

- CLI:

        salt 'webserver*' state.apply webserver_setup

- Top file:

        {{< file >}}
- base:
        'webserver*':
        - webserver_setup
        {{< /file >}}

[Regular Expressions](https://docs.saltproject.io/en/latest/topics/targeting/globbing.html#regular-expressions) and [lists](https://docs.saltproject.io/en/latest/topics/targeting/globbing.html#lists) You can also utilize it to match against minion IDs.

## Grains

Salt's [*grains*](https://docs.saltproject.io/en/latest/topics/grains/) system offers access to information generated by and stored on a minion. This includes details like the minion's operating system, domain name, IP address, and more. Additionally, you can define custom grain data on a minion, as described in Salt's documentation.

Grain data can be utilized to target minions from the command line. For instance, this command installs httpd on all minions running CentOS:

    salt -G 'os:CentOS' pkg.install httpd

You can also employ grains in a top file:

{{< file >}}
base:
'os:CentOS':
- match: grain
- centos_setup
{{< /file >}}

Grain information typically isn't very dynamic, but it may change occasionally. Salt automatically refreshes its grain data when such changes occur. To view your minions' grain data:

    salt '*' grains.items

## Storing Data and Secrets in Pillar

Salt's [*pillar*](https://docs.saltproject.io/en/latest/topics/tutorials/pillar.html) feature allows you to define data on the Salt master and distribute it to minions. One of its primary purposes is to store secrets like account credentials. Additionally, pillar serves as a convenient location to store non-secret data that you prefer not to include directly in your state files.

{{< note respectIndent=false >}}
Aside from storing pillar data on the master, you can also store it in other locations, such as in a [Git repository](https://docs.saltproject.io/en/latest/ref/pillar/all/salt.pillar.git_pillar.html) or Hashicorp's Vault](https://docs.saltproject.io/en/latest/ref/pillar/all/salt.pillar.vault.html).
{{< /note >}}

For instance, suppose you need to create system users on a minion and assign different shells to each of them. If you were to encode this information into a state file, you would require a new declaration for each user. However, if you store the data in pillar, you can create a single state declaration and inject the pillar data into it using Salt's [Jinja templating](#jinja-templates) feature.

{{< note respectIndent=false >}}
Salt Pillar is occasionally mistaken for Salt Grains because they both store data utilized in states and remote execution. However, there's a crucial difference: Grains store data originating from the minions, while Pillar stores data originating on the master (or another backend) and delivers it to the minions.
{{< /note >}}

### Anatomy of Pillar Data

Pillar data is stored in `.sls` files, which use the same YAML syntax as states:

    {{< file "/srv/pillar/user_info.sls">}}
    users:
    joe:
    shell: /bin/zsh
    amy:
    shell: /bin/bash
    sam
    shell: /bin/fish
    {{< /file >}}

Similar to state files, a top file (distinct from your states' top file) is used to map pillar data to minions:

    {{< file "/srv/pillar/top.sls">}}
    base:
    'webserver1':
    - user_info
    {{< /file >}}

## Jinja Templates

To incorporate pillar data into your states, employ  [Jinja's template syntax](https://docs.saltproject.io/en/latest/topics/jinja/index.html). Although Salt utilizes YAML syntax for state and pillar files, these files are initially interpreted as Jinja templates (by default).

Here's an example state file that utilizes the pillar data from the previous section to create system users and set the shell for each:

    {{< file "/srv/salt/user_setup.sls" >}}
    {% for user_name, user_info in pillar['users'].iteritems() %}
    {{ user_name }}:
    user.present:
    - shell: {{ user_info['shell'] }}
    {% endfor %}
    {{< /file >}}

Before applying the state file to the minion, Salt will compile it into something resembling this:

    {{< file >}}
    joe:
    user.present:
    - shell: /bin/zsh

    amy:
    user.present:
    - shell: /bin/bash

    sam:
    user.present:
    - shell: /bin/fish
    {{< /file >}}

You can also utilize Jinja to work with grain data in your states. For example, this state will install Apache and adjust the package name based on the operating system:

    {{< file "/srv/salt/webserver_setup.sls" >}}
    install_apache:
    pkg.installed:
    {% if grains['os'] == 'CentOS' %}
    - name: httpd
    {% else %}
    - name: apache
    {% endif %}
    {{< /file >}}

{{< note respectIndent=false >}}
In addition to Salt's documentation on Jinja, you can refer to the [official Jinja documentation](http://jinja.pocoo.org/docs/2.10/templates/) for detailed information on the template syntax.
{{< /note >}}

## Beacons

The[beacon](https://docs.saltproject.io/en/latest/topics/beacons/) system monitors various system processes on Salt minions. It offers numerous [beacon modules](https://docs.saltproject.io/en/latest/ref/beacons/all/index.html) for this purpose.

Beacons can activate [reactors](https://docs.saltproject.io/en/latest/topics/reactor/index.html#reactor), which can facilitate implementing changes or troubleshooting issues. For instance, if a service's response times out, the reactor system can restart the service.

## Getting Started with Salt

Now that you've gained familiarity with some of Salt's fundamental terms and components, proceed to our guide [Getting Started with Salt - Basic Installation and Setup](/docs/guides/getting-started-with-salt-basic-installation-and-setup/) to establish a configuration for executing commands and provisioning minion servers.

Additionally, SaltStack's documentation includes a page of [best practices](https://docs.saltproject.io/en/latest/topics/best_practices.html) to keep in mind while working with Salt. It's advisable to review this page and integrate these practices into your workflow whenever feasible.
