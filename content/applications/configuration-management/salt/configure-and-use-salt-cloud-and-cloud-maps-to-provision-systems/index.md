---
slug: Configure and Utilize Salt Cloud and Cloud Maps for System Provisioning
description: "This guide demonstrates the installation, configuration, and usage of Salt Cloud to provision multiple Uthos directly from the command line"
og_description: "Salt Cloud, an integral component of SaltStack, simplifies the process of provisioning multiple cloud systems. Follow our guide to efficiently create, manage, and map your own Salt Cloud infrastructure."
keywords: ["SaltStack", "Salt", "salt-cloud"]
published: 2024-04-01
modified: 2024-04-01
modified_by:
  name: Utho
title: "Mastering System Provisioning with Salt Cloud and Cloud Maps"
title_meta: "Unlock Seamless System Provisioning with Salt Cloud and Cloud Maps"
tags: ["automation","salt"]
authors: ["Pawan Kumar"]
---

## What is Salt Cloud?

[Salt Cloud](https://docs.saltproject.io/en/latest/topics/cloud/) is a configuration management tool that allows users to provision systems on cloud hosts or hypervisors. During installation, Salt Cloud installs Salt on all provisioned systems by default. This enables the user to put systems into the desired state during provisioning.

Key features of Salt Cloud include:

Streamlines system information gathering and lifecycle management via a user-friendly Command Line Interface (CLI).
Supports Utho as a provider without requiring additional plugin installations.

## Before You Begin

1. Set up a management server to create and oversee your Utho servers. You can host this server remotely on a Utho or on a local machine, as long as it's capable of installing and running Salt Cloud.

2. This guide assumes that Salt Cloud will be installed along with the Salt master server.

3. Generate an [API key](/docs/products/tools/api/guides/manage-api-tokens/) key to access the Utho API. This key will be utilized by Salt Cloud to manage your instances. Ensure to keep your API key secure. Set the environment variable `API_TOKEN` and verify that your API key works through the REST interface.

        curl -H "Authorization:Bearer $API_TOKEN" https://api.utho.com/v4/account | json_pp

4.  The management server needs unrestricted access to the Utho API, without going through a proxy.

## Installing Salt and Salt Cloud Using Bootstrap Script
The preferred method for installing Salt Cloud is by using a Salt Bootstrap script. This script installs Salt, Salt Cloud packages, and all necessary dependencies. Run the script with the `-h` flag to see additional options, or check out the [Salt Bootstrap Guide](https://docs.saltproject.io/en/latest/topics/tutorials/salt_bootstrap.html) for detailed instructions.

1. Download the Salt Bootstrap script using curl:

        curl -o bootstrap-salt.sh -L https://bootstrap.saltproject.io

2. Execute the script and utilize the -L option to install Salt and Salt Cloud:

        sh bootstrap-salt.sh -L

## Configure Salt Cloud

### Set up Provider Configuration:

Configure and test access to the Utho API.
1. Open the file `/etc/salt/cloud.providers.d/utho.conf` to configure your provider settings. Salt Cloud will utilize this configuration during operations with instances in the CLI. Choose a short and memorable name (such as `li`) for your provider. Additionally, you can specify multiple Utho providers for managing multiple accounts. Note that Utho requires the default root password for new servers to be eight characters long and contain lowercase letters, uppercase letters, and numbers.

       {{< file "/etc/salt/cloud.providers.d/utho.conf" conf >}}
       my-utho-provider:
       api_version: v4
       apikey: <Your API key>
       password: <Default password for the new instances>
       driver: utho
       {{< /file >}}

    {{< note respectIndent=false >}}
All configuration files store data in YAML format. Pay attention to indentation; use only spaces and avoid tabs. Typically, each level of indentation is separated by 2 spaces.
{{< /note >}}

2. Verify access to the Utho API by performing a test.

Run the following command from your master to test access to the Utho API:

        salt-cloud --list-locations my-utho-provider

If you've correctly configured the connection to Utho, you'll see output similar to:

        my-utho_provider:
            ----------
            utho:
                ----------
                Atlanta, GA, USA:
                    ----------
                    ABBR:
                        atlanta
                    DATACENTERID:
                        4
                    LOCATION:
                        Atlanta, GA, USA

## Create a New Salt Cloud Instance

### List Available Locations, Images and Sizes

Before creating new instances, you need to specify the instance size, which includes the amount of system memory, CPU, and storage. Additionally, you'll need to specify the location, which refers to the physical location of the data center, and the image, which represents the operating system.

You can obtain this information using the following commands:

*  Available locations:

        salt-cloud --list-locations my-utho-provider

*  Available sizes:

        salt-cloud --list-sizes my-utho-provider

*  Available images:

        salt-cloud --list-images my-utho-provider

### Set up Profile Configuration

To create an instance profile, you'll describe a server that will be created on your Utho account. The minimum configuration should include the provider, size, image, and location.

For this example, let's create an instance with a standard size, using a Debian 11 image, located in London.

1.  Open `/etc/salt/cloud.profiles.d/utho-london-1gb.conf` and paste the following configuration:

        {{< file "/etc/salt/cloud.profiles.d/utho-london-1gb.conf" conf >}}
        utho_1gb:
        provider: my-utho-provider
        size: g6-standard-1
        image: utho/debian11
        location: eu-west
        {{< /file >}}

You have the option to use one file for all profiles or create one file per instance profile. All files from `/etc/salt/cloud.profiles.d/` are read during execution.

2. By default, Salt Cloud installs Salt Minion on all provisioned servers. To enable provisioned systems to connect to the master, set the default master configuration for all provisioned systems.

Open `/etc/salt/cloud.conf.d/master.conf` and paste the following content, replacing saltmaster.example.com with the IP address or domain name of your master server:

    {{< file "/etc/salt/cloud.conf.d/master.conf" >}}
    minion:
    master: saltmaster.example.com
    {{< /file >}}

An alternative approach is to configure this parameter for specific instance profiles:

    {{< file "/etc/salt/cloud.profiles.d/utho-london-1gb.conf" conf >}}
    utho_1gb_with_master:
    provider: my-utho-provider
    size: g6-standard-1
    image: utho/debian11
    location: eu-west
    minion:
    master: mymaster.example.com
    {{< /file >}}

3. Configure [SSH key authentication](/docs/guides/use-public-key-authentication-with-ssh/) for your instance during provisioning by setting up the profile as follows. Replace `ssh_pubkey` and `ssh_key_file` with the key information for an SSH key on your master server:

       {{< file "/etc/salt/cloud.profiles.d/utho-london-1gb.conf" conf >}}
       utho_1gb_with_ssh_key:
       provider: my-utho-provider
       size: g6-standard-1
       image: utho/debian11
       location: eu-west
       ssh_pubkey: ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIKHEOLLbeXgaqRQT9NBAopVz366SdYc0KKX33vAnq+2R user@host
       ssh_key_file: ~/.ssh/id_ed25519
       {{< /file >}}

{{< note respectIndent=false >}}
If your master server is behind a firewall, you'll need to open ports 4505-4506 in your [firewall](https://docs.saltproject.io/en/latest/topics/tutorials/firewall.html). Depending on your network setup, you might also need to configure port forwarding for these ports.
{{< /note >}}

## Salt Cloud Interface

### Create Utho Instances

There are multiple methods for creating new instances:

*  **Create a single new instance**:

        salt-cloud -p utho_1gb utho1

Creating the instance and installing Salt Minion on it may require some time.

    When deployment is complete, you will see following summary:

        utho1:
            ----------
            deployed:
                True
            id:
                <ID>
            image:
                utho/debian11
            name:
                utho1
            private_ips:
            public_ips:
                - <ip_address>
            size:
                g6-standard-1
            state:
                Running

*  You can connect to the instance using the username `root` and the password specified in the configuration file.

*  To **create multiple servers in one command** type the following:

        salt-cloud -p utho_1gb utho1 utho2

The instance names provided in this command are used for internal management purposes and are not linked to the instance hostname.

Utho labels:

* May only contain ASCII letters or numbers, dashes, and underscores.
* Must start and end with letters or numbers.
* Must be at least three characters in length.
* By default, instances are usually created one after another, in a serial manner. To create instances simultaneously, speeding up the process, utilize the `salt-cloud` command with the `-P`option **create instances in parallel allowing for deployment**:

        salt-cloud -P -p utho_1gb utho1 utho2

* **If you prefer not to install Salt Minion on the provisioned server**, execute `salt-cloud` with the `--no-deploy` option:

        salt-cloud -p utho_1gb --no-deploy utho3

Salt Cloud will generate an error message, but the instance will still be created:    

        utho3:
            ----------
            Error:
                ----------
                No Deploy:
                    'deploy' is not enabled. Not deploying.

### Destroy Salt Cloud Instances

1.  To destroy an instance, run salt-cloud with the `-d` option:

        salt-cloud -d utho1

2.  The server will be deleted after you confirm the deletion.

### Retrieve Information About Running Instances

**Partial Information**

Obtain partial information by running salt-cloud with the `-Q` option:

    salt-cloud -Q

**Full Information**

Retrieve full information about instances using the `-F` option:

    salt-cloud -F

**Configure a Selective Query**

1. Edit `/etc/salt/cloud.conf.d/query.conf` and add the fields you would like to select:

       {{< file "/etc/salt/cloud.conf.d/query.conf" >}}
       query.selection:
       - image
       - size
       {{< /file >}}

2.  Perform a selective query using the `-S` option:

        salt-cloud -S

    Output:

        utho3:
            ----------
            image:
                utho/debian11
            size:
                1024

## Performing Actions on Salt Cloud Instances
Actions are specific features that apply to individual instances. Currently, the following actions are supported:

* `show_instance`
* `start`
* `stop`

For instance, to stop a running instance named `utho1`, execute the `salt-cloud` command with the `-a` option and specify the stop command:

    salt-cloud -a stop utho1

## Use Cloud Map Files to Manage Complex Environments

Scaling, creating, and destroying servers one by one can be tedious. To streamline this process, you can use Cloud Map files.

Cloud maps assign profiles to a list of instances. When executed, Salt Cloud will attempt to synchronize the state of these instances with the map file. New instances will be created, while existing ones will remain unmodified.

### Configure Cloud Map
In this example, the Cloud map will specify two instances: `utho_web` and `utho_db`. Both instances will utilize the profile `utho_1gb`, which was defined earlier.

1.  Open `/etc/salt/cloud.conf.d/utho.map` and paste the following configuration:


    {{< file "/etc/salt/cloud.conf.d/utho.map" >}}
    utho_1gb:
    - utho_web
    - utho_db
    {{< /file >}}

The Cloud map file enables you to define instances from multiple Utho accounts or even from different providers. Refer to the [Cloud Map documentation](https://docs.saltproject.io/en/latest/topics/cloud/map.html)  for a detailed guide.

2.  To create instances from the Cloud map file, run salt-cloud with the `-m` option and specify the `.map` file:

        salt-cloud -m /etc/salt/cloud.conf.d/utho.map

3.  Salt Cloud will prompt you to confirm the target configuration:

        The following virtual machines are set to be created:
            utho_web
            utho_db

        Proceed? [N/y] y
        ... proceeding
        .  .  .

To create instances in parallel, use the `-P`option with Cloud map files.

### Deleting Instances Created by Cloud Map Files

If an existing instance is removed from the Cloud map file, it will remain running. To delete instances created by map files:

* Delete single or multiple instances by specifying their names:

        salt-cloud -d utho_web utho_db

* Delete all instances described in the `map` file:

        salt-cloud -d -m /etc/salt/cloud.conf.d/utho.map

* Allow Salt Cloud to destroy every instance not described in the `map` file. Note that SaltStack considers deleting such instances dangerous, and this feature is disabled by default. To enable it:

1. Modify the file `/etc/salt/cloud` and add the following:

       {{< file "/etc/salt/cloud" >}}
       enable_hard_maps: True
       {{< /file >}}

2. Execute `salt-cloud` with the `--hard` option:

           salt-cloud -d -m /etc/salt/cloud.conf.d/utho.map

3. Confirm the deletion when prompted.
