---
slug: Installing Anaconda on CentOS 8 Stream
description: "Discover the process of installing Anaconda on a Linux CentOS distribution."
keywords: ['anaconda linux']
tags: ['python', 'centos']
published: 2024-03-14
modified_by:
  name: Utho
title: "Installing Anaconda on Linux CentOS"
title_meta: Learn how to install Anaconda on Linux CentOS with this step-by-step guide.
relations:
  platform:
    key: how-to-install-anaconda
    keywords:
      - distribution: CentOS 8 Stream
authors: ["Pawan Kumar"]
---
Anaconda is a package that includes Python and R programming languages, along with various add-ons. It's widely used in scientific computing, data analytics, and machine learning.

## Starting with Anaconda
Anaconda is compatible with Windows, macOS, and Linux operating systems. There are various licenses associated with Anaconda. This guide focuses on installing the free and open-source.

When you install Anaconda, you get:

- Updated Python and R interpreters, certified for reliability
- A wide range of open-source scientific computing libraries for Python and R
- The conda package and environment manager for easy library management
- The Anaconda Navigator graphical user interface (GUI), offering an alternative to conda command-line interface

The primary advantage of Anaconda is its ability to ensure compatibility among all the components mentioned earlier. This makes Anaconda a favorite tool among data scientists, researchers, and statisticians. It effectively manages dependency and compatibility concerns, allowing users in specialized fields to concentrate on their research without worrying about technical issues.

### Anaconda Installation Steps

Anaconda can be installed either through a GUI or via the command line. This guide will walk you through the command line installation process on a CentOS distribution.

Follow the steps below to install Anaconda. The installation process takes approximately twenty minutes and requires around 3.5 gigabytes of disk space within your $HOME directory.

1.Navigate to your working directory, which might be your home directory.

        cd /home/example_user

1. Update the packages on your CentOS system.

        dnf upgrade

1. Install `bzip2` package.

        sudo dnf install bzip2

1. If Wget is not installed on your CentOS system, install it now.

        sudo yum install -y wget

1. Download the Anaconda installer file.

        sudo wget https://repo.anaconda.com/archive/Anaconda3-2020.11-Linux-x86_64.sh

        This installs Anaconda 3 along with Python 3.8.

Note: You can visit the [Anaconda archive](https://repo.anaconda.com/archive) to find all Anaconda installers.

1. Verify the authenticity of the installer by executing the following command. Make sure to replace Anaconda3-2020.11-Linux-x86_64.sh with the version of Anaconda you installed in the previous step.

        md5sum Anaconda3-2020.11-Linux-x86_64.sh

You should see the signature of the specific release returned in the output. As of writing this guide, the output should resemble the following: 4cd48ef23a075e8555a8b6d0a8c4bae2 Anaconda3-2020.11-Linux-x86_64.sh. The Anaconda archive mentioned in the previous section lists integrity hashes for all available downloads.

1. Launch the installer using the following command. Make sure to replace Anaconda3-2020.11-Linux-x86_64.sh with the version of Anaconda you downloaded.

        sudo bash Anaconda3-2020.11-Linux-x86_64.sh

 When prompted, press **ENTER** to continue. Agree to the license terms by typing **yes**.

1. Accept the default installation location, which is typically /root/anaconda3.

1. When prompted to initialize Anaconda3 by running conda init, it is recommended to type yes. After the installation completes, you'll see a "Thank you for installing Anaconda3!" message. Run the following commands to ensure Anaconda is accessible and added to your system's path:

        sudo -s source /root/anaconda3/bin/activate
        export PATH="/root/anaconda3/bin:$PATH"

Note: If you choose no during the conda init prompt, conda won't be able to modify your shell scripts. To manually initialize conda after the installation is complete, run the commands below:

       sudo -s source /root/anaconda3/bin/activate
       export PATH="/root/anaconda3/bin:$PATH"
       conda init

1. Confirm that Anaconda is installed correctly by executing the following command:

        conda info

You will see an output similar to the following, depending on your installation location:

             {{< output >}}
             active environment : None
             user config file : /home/example_user/.condarc
             populated config files :
             conda version : 4.9.2
             conda-build version : 3.20.5
             python version : 3.8.5.final.0
             virtual packages : __glibc=2.28=0
                                    __unix=0=0
                           __archspec=1=x86_64
       base environment : /root/anaconda3  (read only)
           channel URLs : https://repo.anaconda.com/pkgs/main/linux-64
                          https://repo.anaconda.com/pkgs/main/noarch
                          https://repo.anaconda.com/pkgs/r/linux-64
                          https://repo.anaconda.com/pkgs/r/noarch
          package cache : /root/anaconda3/pkgs
                          /home/example_user/.conda/pkgs
       envs directories : /home/example_user/.conda/envs
                          /root/anaconda3/envs
               platform : linux-64
             user-agent : conda/4.9.2 requests/2.24.0 CPython/3.8.5 Linux/4.18.0-305.3.1.el8_4.x86_64 almalinux/8.4 glibc/2.28
                UID:GID : 1000:1000
             netrc file : None
           offline mode : False
                {{</ output >}}

You can confirm the conda installation by running either the list or version commands:

        conda list
        conda --version

1. Launch the Python programming shell by typing python and pressing Enter.

        python

At this point, Anaconda will launch Python version 3.8.5, or a different version if you installed a different Anaconda version. For more in-depth guidance on using Anaconda, you can explore the extensive [documentation](https://docs.anaconda.com/anaconda/) and other available [training materials](https://www.anaconda.com/help)  provided by Anaconda.
