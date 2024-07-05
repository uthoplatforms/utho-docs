---
slug: "Mastering Software: Streamline Installs & Updates with Cloud-Init Magic"
title: "Effortless Server Setup: Utilize Cloud-Init for Seamless Software Installation and Updates"
description: "Unlock the Power of Cloud-Init: Seamlessly Upgrade and Install Software on Fresh Server Deployments"
keywords: ['cloud-init','cloudinit','apt','yum']
authors: ["Pawan Kumar"]
published: 2024-03-22
modified_by:
  name: Pawan Kumar
---

[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html) simplifies server setup across different platforms and distributions by automating initialization tasks. By integrating with Utho, you can use cloud-init to deploy Compute Instances with custom user data scripts to define your desired configuration.

In this guide, we'll walk you through managing packages on new servers using cloud-init. Whether you need to upgrade system packages, install packages during initialization, or manage repositories, this tutorial has you covered.

Before diving in, it's essential to review our guide on [Use Cloud-Init to Automatically Configure and Secure Your Servers](/docs/guides/configure-and-secure-servers-with-cloud-init/) to Automatically Configure and Secure Your Servers. This guide covers creating a cloud-config file, which you'll need for the steps outlined here. Once you're ready to deploy your cloud-config, the linked guide will show you how to do it effectively.

## Upgrade Packages

Cloud-init simplifies managing package updates and upgrades during server initialization. By setting the `package_update` option to `true`, you ensure that updates are applied to installed packages and the package repositories are refreshed. This feature is valuable for keeping your server up-to-date with the latest package references.

```file {title="cloud-config.yaml" lang="yaml"}
package_update: true
```

The `package_upgrade` option updates installed packages to their newest versions. Using this option during initialization enhances system stability and security, unless you require specific package versions.

```file {title="cloud-config.yaml" lang="yaml"}
package_upgrade: true
```
Furthermore, in cloud-config, there's an option to guarantee system reboot for any package upgrades or installations that demand it. Enabling this option ensures that package upgrades and installations are readily available for immediate use once the cloud-init process concludes.

```file {title="cloud-config.yaml" lang="yaml"}
package_update: true
package_upgrade: true
package_reboot_if_required: true
```
## Install Packages

To install packages using cloud-init, utilize the `packages` option in your cloud-config. Simply provide this option with a list of package names, and cloud-init takes care of the installation during initialization.

Here are examples of installing the main components of a LAMP stack, a commonly used web application setup. It's important to note that cloud-config requires precise package names, which may differ between distributions, along with varying prerequisites for the setup. Below, we demonstrate how the setup would differ between two different distributions.

For more information about the LAMP stack and its package prerequisites, refer to our guide on [How to Install a LAMP Stack](/docs/guides/how-to-install-a-lamp-stack-on-ubuntu-22-04/). You can explore different distributions by using the dropdown menu at the top of that guide.

    {{< tabs >}}
    {{< tab "Ubuntu 22.04" >}}
```file {title="cloud-config.yaml" lang="yaml"}
packages:
  - apache2
  - mysql-server
  - php
  - libapache2-mod-php
  - php-mysql
```
    {{< /tab >}}
    {{< tab "CentOS 8" >}}
```file {title="cloud-config.yaml" lang="yaml"}
packages:
  - httpd
  - mariadb-server
  - php
  - php-pear
  - php-mysqlnd
```
    {{< /tab >}}
    {{< /tabs >}}

Note: The `package_reboot_if_required` option, discussed earlier, also applies to package installations. When set to `true`, it guarantees that the system will reboot if any newly installed packages necessitate it.

## Add Software Repositories

Cloud-init offers advanced package manager tools, including the capability to add custom repositories during initialization. The process varies based on your distribution, as cloud-init utilizes specific modules for managing different package managers. Below, we cover two widely used package managers: [APT](/docs/guides/apt-package-manager/), commonly found on Debian and Ubuntu systems, and [Yum](/docs/guides/yum-package-manager/)/[DNF](/docs/guides/dnf-package-manager/), prevalent on CentOS, Fedora, and other RHEL-based distributions.

Other than these, cloud-init also supports the [Zypper](/docs/guides/zypper-package-manager/) package manager, used on openSUSE distributions. You can learn about adding repositories for Zypper in cloud-init's [Zypper Add Repo](https://cloudinit.readthedocs.io/en/latest/reference/modules.html#zypper-add-repo) module reference documentation.

    {{< tabs >}}
    {{< tab "APT" >}}

In cloud-config, the `apt` option provides detailed control over the APT package manager. You can explore its various features further in cloud-init's  [APT Configure](https://cloudinit.readthedocs.io/en/latest/reference/modules.html#apt-configure) module reference documentation here.

To include third-party repositories in `APT`, utilize the `sources` option within an apt block. The sources option is a dictionary containing one or more repository entries. Each entry requires a source string, specifying the repository location, along with a set of key-related options, such as providing the GPG key for the repository.

There are two methods for adding the repository, depending on how you intend to provide the GPG key:

- If using a GPG key server, you can specify the key ID and the server location, as shown in this example.

    ```file {title="cloud-config.yaml" lang="yaml"}
    apt:
    sources:
    docker:
    source: deb [arch="amd64"] https://download.docker.com/linux/ubuntu $RELEASE stable
    keyid: 8D81803C0EBFCD88
    keyserver: 'https://download.docker.com/linux/ubuntu/gpg'
    ```

In this scenario, the key ID was acquired from the GPG public key using the following sequence of commands:

    ```command
    wget https://download.docker.com/linux/ubuntu/gpg -O docker.gpg.pub.key
    gpg --list-packets docker.gpg.pub.key | awk '/keyid:/{ print $2 }'
    ```

-   Another option is to manually insert the GPG key using the `key` option, demonstrated in this example. Simply substitute the placeholder GPG string with the complete GPG public key, similar to the one obtained using the `wget` command earlier.

    ```file {title="cloud-config.yaml" lang="yaml"}
    apt:
      sources:
        docker:
          source: deb [arch="amd64"] https://download.docker.com/linux/ubuntu $RELEASE stable
          key: |
            -----BEGIN PGP PUBLIC KEY BLOCK-----

            mQINBFit2ioBEADhWpZ8/wvZ6hUTiXOwQHXMAlaFHcPH9hAtr4F1y2+OYdbtMuth
            lqqwp028AqyY+PRfVMtSYMbjuQuu5byyKR01BbqYhuS3jtqQmljZ/bJvXqnmiVXh
            38UuLa+z077PxyxQhu5BbqntTPQMfiyqEiU+BKbq2WmANUKQf+1AmZY/IruOXbnq
            ...
            jCxcpDzNmXpWQHEtHU7649OXHP7UeNST1mCUCH5qdank0V1iejF6/CfTFU4MfcrG
            YT90qFF93M3v01BbxP+EIY2/9tiIPbrd
            =0YYh
            -----END PGP PUBLIC KEY BLOCK-----
    ```

Using either approach, you can confirm the added repository on the new system with a command similar to this one:

```command
sudo apt-cache policy
```

```output
Package files:
 100 /var/lib/dpkg/status
     release a=now
 500 https://download.docker.com/linux/ubuntu jammy/stable amd64 Packages
     release o=Docker,a=jammy,l=Docker CE,c=stable,b=amd64
     origin download.docker.com
...
```

    {{< /tab >}}
    {{< tab "Yum/DNF" >}}

In cloud-config, you can manage repositories in Yum and DNF using the dedicated `yum_repos` option. Each repository added to the package manager requires an entry under `yum_repos`, including an identifier, URL, and additional details.

Here's an example that adds the Extra Packages for Enterprise Linux (EPEL) repository, commonly used for accessing a broader selection of packages on RHEL-based systems:

```file {title="cloud-config.yaml" lang="yaml"}
yum_repos:
  epel-release:
    name: Extra Packages for Enterprise Linux 8 - Release
    baseurl: http://download.fedoraproject.org/pub/epel/8/Everything/$basearch
    enabled: true
    failovermethod: priority
    gpgcheck: true
    gpgkey: http://download.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-8
```

The initial three options (`name`, `baseurl`, and `enabled`) are tailored for cloud-init's `yum_repos`. Nonetheless, `yum_repos` also accommodates Yum's native repository configuration options, which are utilized by the remaining options above.

After the initialization process completes, you can confirm the added repository by employing the `repolist` command for Yum/DNF:

```command
sudo dnf repolist
```

```output
repo id                                                                  repo name
...
epel-release                                                             Extra Packages for Enterprise Linux 8 - Release
...
```

Explore the diverse capabilities of the `yum_repos` option for managing repositories in cloud-init by referring to the [Yum Add Repo](https://cloudinit.readthedocs.io/en/latest/reference/modules.html#yum-add-repo) module reference documentation.

    {{< /tab >}}
    {{< /tabs >}}

## Confirm Updates and Installations

cloud-init maintains a log at `/var/log/cloud-init-output.log` containing all the output generated during its initialization process. For example, the snippet below displays the log entries related to APT installing the `apache2` package.

```command
sudo cat /var/log/cloud-init-output.log
```

```output
...
The following additional packages will be installed:
  apache2-bin apache2-data apache2-utils libapache2-mod-php8.1 libapr1
  libaprutil1 libaprutil1-dbd-sqlite3 libaprutil1-ldap libcgi-fast-perl
  libcgi-pm-perl libclone-perl libencode-locale-perl libevent-pthreads-2.1-7
  libfcgi-bin libfcgi-perl libfcgi0ldbl libhtml-parser-perl
  libhtml-tagset-perl libhtml-template-perl libhttp-date-perl
  libhttp-message-perl libio-html-perl liblua5.3-0 liblwp-mediatypes-perl
  libmecab2 libprotobuf-lite23 libtimedate-perl liburi-perl mecab-ipadic
  mecab-ipadic-utf8 mecab-utils mysql-client-8.0 mysql-client-core-8.0
  mysql-common mysql-server-8.0 mysql-server-core-8.0 php-common php8.1
  php8.1-cli php8.1-common php8.1-mysql php8.1-opcache php8.1-readline
  ssl-cert
Suggested packages:
  apache2-doc apache2-suexec-pristine | apache2-suexec-custom www-browser
  php-pear libdata-dump-perl libipc-sharedcache-perl libbusiness-isbn-perl
  libwww-perl mailx tinyca
The following NEW packages will be installed:
  apache2 apache2-bin apache2-data apache2-utils libapache2-mod-php
  libapache2-mod-php8.1 libapr1 libaprutil1 libaprutil1-dbd-sqlite3
  libaprutil1-ldap libcgi-fast-perl libcgi-pm-perl libclone-perl
  libencode-locale-perl libevent-pthreads-2.1-7 libfcgi-bin libfcgi-perl
  libfcgi0ldbl libhtml-parser-perl libhtml-tagset-perl libhtml-template-perl
  libhttp-date-perl libhttp-message-perl libio-html-perl liblua5.3-0
  liblwp-mediatypes-perl libmecab2 libprotobuf-lite23 libtimedate-perl
  liburi-perl mecab-ipadic mecab-ipadic-utf8 mecab-utils mysql-client-8.0
  mysql-client-core-8.0 mysql-common mysql-server mysql-server-8.0
  mysql-server-core-8.0 php php-common php-mysql php8.1 php8.1-cli
  php8.1-common php8.1-mysql php8.1-opcache php8.1-readline ssl-cert
0 upgraded, 49 newly installed, 0 to remove and 11 not upgraded.
...
```

Although the detailed logs are beneficial for debugging, they can make verifying upgraded and installed packages a bit complicated. It's more efficient to use commands tailored to your system's package manager for this task. Below, you'll find steps for verifying package upgrades and installations on Debian/Ubuntu systems (using APT) and RHEL-based systems like CentOS and Fedora (using DNF or Yum).

    {{< tabs >}}
    {{< tab "Debian, Ubuntu" >}}

The APT package manager offers a handy `list` command for reviewing packages. When you use the `--upgradable` option with this command, it presents a list of packages that have available upgrades.

``` command
sudo apt list --upgradable
```

Ideally, the output should be empty, but usually, your system retains a few packages that aren't upgraded with `apt upgrade`, often due to dependencies. If this happens, both the `upgrade` command and the cloud-init logs should indicate the packages that weren't upgraded.

    file {title="/var/log/cloud-init-output.log"}
    ...
    0 upgraded, 0 newly installed, 0 to remove and 11 not upgraded.
    ...

The `list` command offers the `--installed` option, which generates a complete list of packages installed via the APT package manager. However, this list can be overwhelming for verification as it includes all packages, including those installed as dependencies. To filter and display only the packages explicitly installed, utilize the `--manual-installed` option instead.

```command
sudo apt list --manual-installed
```

In the previous package installation example provided in this guide, there were only a few packages installed. Thus, you can further condense the output using a simple text filter. The following command achieves this by piping the output to `grep`. Each `\|` serves as a separator for search terms, each of which corresponds to one or more packages specified in the cloud-config.

```command
sudo apt list --manual-installed | grep 'apache2\|mysql-server\|php'
```

```output
apache2/jammy-updates,now 2.4.52-1ubuntu4.6 amd64 [installed]
libapache2-mod-php/jammy,now 2:8.1+92ubuntu1 all [installed]
mysql-server/jammy-updates,jammy-security,now 8.0.34-0ubuntu0.22.04.1 all [installed]
php-mysql/jammy,now 2:8.1+92ubuntu1 all [installed]
php/jammy,now 2:8.1+92ubuntu1 all [installed]
```

    {{< /tab >}}
    {{< tab "AlmaLinux, CentOS, Fedora, Rocky Linux" >}}

With the Yum/DNF package manager, you can utilize the dedicated `check-update` command to view any packages with available upgrades. Upon completion of the cloud-init initialization, an empty output suggests that all installed packages are up to date.

Note: In this section, the examples specifically employ `dnf`, as newer systems predominantly use the DNF package manager instead of Yum. However, if your system employs Yum, the same commands should function by simply replacing `dnf` with `yum`.

```command
sudo dnf check-update
```

Typically, immediately after initialization, the most straightforward method to verify installed packages is by using the `history` command. Its output displays recent commands executed by the package manager, including updates and installations.

```command
sudo dnf history
```

```output
ID     | Command line                                                                                                                  | Date and time    | Action(s)      | Altered
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
     2 | -y install httpd mariadb-server php php-pear php-mysqlnd                                                                      | 2023-08-09 12:12 | Install        |   76
     1 | -y upgrade                                                                                                                    | 2023-08-09 12:11 | I, U           |  132
```

In Yum/DNF, you can further check on installed packages using the `list installed` command. Below is an example that also employs a `grep` text search to filter the results, enabling you to focus the output solely on matching package names. In this command, each search term is separated by `\|`. This approach could prove beneficial when you want to verify a specific set of installed packages.

``` command
sudo dnf list installed | grep 'httpd\|mariadb-server\|php'
```

```output
httpd.x86_64                       2.4.37-62.module_el8+657+88b2113f       @appstream
httpd-filesystem.noarch            2.4.37-62.module_el8+657+88b2113f       @appstream
httpd-tools.x86_64                 2.4.37-62.module_el8+657+88b2113f       @appstream
mariadb-server.x86_64              3:10.3.28-1.module_el8.3.0+757+d382997d @appstream
mariadb-server-utils.x86_64        3:10.3.28-1.module_el8.3.0+757+d382997d @appstream
php.x86_64                         7.2.24-1.module_el8.2.0+313+b04d0a66    @appstream
php-cli.x86_64                     7.2.24-1.module_el8.2.0+313+b04d0a66    @appstream
php-common.x86_64                  7.2.24-1.module_el8.2.0+313+b04d0a66    @appstream
php-fpm.x86_64                     7.2.24-1.module_el8.2.0+313+b04d0a66    @appstream
php-mysqlnd.x86_64                 7.2.24-1.module_el8.2.0+313+b04d0a66    @appstream
php-pdo.x86_64                     7.2.24-1.module_el8.2.0+313+b04d0a66    @appstream
php-pear.noarch                    1:1.10.5-9.module_el8.2.0+313+b04d0a66  @appstream
php-process.x86_64                 7.2.24-1.module_el8.2.0+313+b04d0a66    @appstream
php-xml.x86_64                     7.2.24-1.module_el8.2.0+313+b04d0a66    @appstream
```

    {{< /tab >}}
    {{< /tabs >}}
