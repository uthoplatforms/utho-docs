---
slug: "Mastering Salt-SSH: Configuration and Execution Tips"
description: 'This comprehensive guide offers step-by-step instructions on setting up and configuring your Linux system to leverage Salt SSH, eliminating the need for installing a salt-minion package.'
keywords: ["Saltstack", " salt", " salt-ssh"]
tags: ["automation","salt","ssh"]
published: 2024-04-01
modified: 2024-04-01
modified_by:
  name: Utho
title: 'Mastering Salt SSH: Managing Your Uthos with Ease'
authors: ["Pawan Kumar"]
---

## Introduction to Salt SSH

Salt SSH enables you to run Salt commands or states without needing to install a salt-minion package.

During execution, Salt SSH will transfer required files to the target system's `/tmp` folder using SSH, execute commands, and then clean up Salt temporary files.

Please note: Due to its use of SSH, Salt SSH tends to be slower compared to standard Salt with ZeroMQ.

## Before You Begin

1. This guide assumes you're using an rpm-based system such as CentOS, RedHat, or Oracle Enterprise Linux.

2. Ensure that you have the `salt` and `salt-ssh` packages installed on your master. You can check if these packages are installed using:
        $rpm -q salt
        $rpm -q salt-ssh

    {{< note respectIndent=false >}}

For detailed instructions on setting up the SaltStack repository, please refer to the [Salt Stack Installation Guide](/docs/guides/getting-started-with-salt-basic-installation-and-setup/)
{{< /note >}}

3. Your minions must have Python installed. Without Python, you can only run Salt SSH in raw mode. In raw mode, you can't use execution modules or apply Salt states. If you're using a recent version of CentOS/RedHat, Python is likely already installed.

4. You need at least one master server and one minion (client) to get started.

## Set Up Salt Roster File

The Roster file holds information about target systems, including connection details and credentials. By default, it's located at `/etc/salt/roster`.

{{< note >}}
The Roster file is configured on the master server.
{{< /note >}}

1.  Open `/etc/salt/roster` with a text editor. Define the client systems by adding the following lines to the file:

This is a basic example of host definition.

    {{< file "/etc/salt/roster" >}}
    utho1:
    host: <IPADDRESS OR HOSTNAME>
    user: <username>
    passwd: <password>

    {{< /file >}}


{{< note respectIndent=false >}}
The Roster file stores data in YAML format. Avoid adding unnecessary spaces to the configuration file.
{{< /note >}}

2.  If you have a public key stored on the minion and a private key on the master system, you can configure access to the minion using a private key. For public key authentication, add the following lines to the Roster file:

        {{< file "/etc/salt/roster" >}}
#Example of Minimal Host Definition Using Private Key:

        utho1:
        host: <IPADDRESS OR HOSTNAME>
        user: <username>
        priv: /<username_home_folder>/.ssh/id_rsa

        {{< /file >}}


{{< note respectIndent=false >}}
Using SSH keys is the most secure method for accessing your minions because passwords aren't stored in plain text.
{{< /note >}}

3. To establish a connection to a minion as a regular user, you need to configure a few files. Salt will then utilize privileges via sudo. To enable sudo, set `sudo: True` in the `host definition` section of the Roster file. By default, sudo only works when the real user is logged in over TTY. You can overcome this in two ways:

**a.** To disable the TTY check, comment out a line in the sudoers file on your minion:

    {{< file "/etc/sudoers" >}}
# Defaults requiretty

{{< /file >}}

**b.** To force TTY allocation, set the `tty: True` option in your Roster file:

    {{< file "/etc/salt/roster" >}}
    utho1:
    host: <IPADDRESS OR HOSTNAME>
    user: <username>
    passwd: <password>
    sudo: True
    tty: True

{{< /file >}}

{{< note respectIndent=false >}}

Permissions granted via sudo will only work if the NOPASSWD option is set up for the user connecting to the minion in the /etc/sudoers file. For more details on Roster files, refer to the [Roster files documentation](https://docs.saltproject.io/en/latest/topics/ssh/roster.html#ssh-roster).
{{< /note >}}

4.  Ensure that the master server has access to the client by using the `salt-ssh` command:

        [root@master ~]# salt-ssh utho1 test.ping

The output should display:

        utho1:
            True

{{< note respectIndent=false >}}
If SSH keys weren't deployed, you might receive the message `The host key needs to be accepted, to auto accept run salt-ssh with the -i flag:`. In this case, simply run `salt-ssh` with the `-i` flag. This will allow Salt to automatically accept a minion's public key. This step only needs to be done once during the initial SSH key exchange.
{{< /note >}}

## Executing Remote Commands Using Salt SSH

1. You can run commands on your minions using the cmd execution module:

        [root@master ~]# salt-ssh utho1 cmd.run "du -sh /root"
            utho1:
                15M /root

2. Salt SSH supports globbing and PCRE regular expressions. For instance, if you want to execute a command on all minions with names containing "utho":


        [root@master ~]# salt-ssh "utho*" cmd.run 'uname -r'
        utho1:
            3.10.0-229.1.2.el7.x86_64
        utho2:
            2.6.32-573.3.1.el6.x86_64

{{< note respectIndent=false >}}
Salt SSH executes commands concurrently, with a default maximum of 25 simultaneous connections.
{{< /note >}}

3. You can utilize any execution module with Salt SSH. These modules enable you to install packages, manage services, collect system information, and perform various tasks.

        [root@master ~]# salt-ssh utho1 pkg.install iftop
        utho1:
            ----------
            iftop:
            ----------
            new:
                1.0-0.14.pre4.el7
            old:

        [root@master ~]# salt-ssh utho1 service.restart httpd
            utho1:
                True

        [root@master ~]# salt-ssh utho1 disk.percent /var
            utho1:
                22%

    {{< note respectIndent=false >}}
    A comprehensive list of execution modules can be found at [Execution modules documentation](https://docs.saltproject.io/en/latest/ref/modules/all/index.html).
{{< /note >}}

## Installing Salt-Minion Remotely Using Salt SSH
A useful scenario for Salt SSH is automating the installation of `salt-minion` using a simple Salt state.

1. Create a directory to hold your state:

        [root@master ~]# mkdir /srv/salt/install_salt_minion

2. Open the `/srv/salt/install_salt_minion/init.sls` file and define your state:

       {{< file "/srv/salt/install_salt_minion/init.sls" >}}
- This state installs salt-minion on your hosts using Salt SSH.
- It installs the SaltStack repository, installs salt-minion from that repository, enables and starts the salt-minion service, and
- Declares the master in the /etc/salt/minion file.
salt-minion:

# Installing the SaltStack Repository for RHEL/CentOS Systems

    pkgrepo.managed:
        - name: salt-latest
        - humanname: SaltStack Latest Release Channel for RHEL/Centos $releasever
        - baseurl: https://repo.saltproject.io/yum/redhat/$releasever/$basearch/latest
        - gpgkey: https://repo.saltproject.io/yum/redhat/$releasever/$basearch/latest/SALTSTACK-GPG-KEY.pub
        - gpgcheck: 1
        - enabled: 1
    # Install the salt-minion package and all its dependencies.
    pkg:
        - installed
        # Require that SaltStack repo is set up before installing salt-minion.
        - require:
            - pkgrepo: salt-latest
    # Start and enable the salt-minion daemon.
    service:
        - running
        - enable: True
        # Require that the salt-minion package is installed before starting daemon
        - require:
            - pkg: salt-minion
        # Restart salt-minion daemon if /etc/salt/minion file is changed
        - watch:
            - file: /etc/salt/minion

# Configure Salt master in conf file
    /etc/salt/minion:
    file.managed:
        # File will contain only one line
        - contents:
            - master: <IPADDRESS OR HOSTNAME>

    {{< /file >}}

3. To apply this state, execute the following command:

        [root@master salt]#  salt-ssh utho2 state.apply install_salt_minion

4. Check if the minion's key is pending acceptance using the `salt-key` command::

        [root@master salt]# salt-key -l un
        Unaccepted Keys:
            utho2

5. To finalize the minion's configuration, accept its public key:

        [root@master salt]# salt-key -a utho2

Once the minion's key is accepted, it is fully configured and ready for command execution.
