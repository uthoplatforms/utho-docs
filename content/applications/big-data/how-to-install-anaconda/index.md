---
slug: "Installing-Anaconda: A Step-by-Step Guide"
description: "Discover how to install Anaconda, a Python/R distribution tailored for scientific computing, on Ubuntu"
keywords: ['Data Science','Anaconda','Package Management','Python','Ruby','Ubuntu']
published: 2024-03-12
image: Anaconda.jpg
modified_by:
  name: Utho
title: Installing Anaconda on Ubuntu:A Step-by-Step Guide
title_meta: "Installing Anaconda on Ubuntu: A Comprehensive Guide"
key: how-to-install-anaconda
distribution: Ubuntu
authors: ["Utho"]
---

## Installing Anaconda on Ubuntu: Step-by-Step Instructions

Installing Anaconda on Utho (or any other Linux system) using the command line interface might seem intimidating, but it's entirely achievable without a graphical environment.

Follow these steps to download the latest Anaconda installer and install Anaconda. As of writing this guide, the latest version is 2020.11.

- Connect to your Utho using SSH (or Lish) and log in with your username and password.
- Ensure you are in the Bash shell by typing bash.
- If you don't have a Downloads directory already, create one by typing mkdir Downloads.
- Navigate to your new Downloads directory by typing cd Downloads.
- In your web browser, visit the Anaconda Individual Edition downloads page here and copy the link of the "64-Bit (x86) Installer," but do not download it.
- In the terminal, enter the following command, replacing the URL with the one you copied earlier:

        wget https://repo.anaconda.com/archive/Anaconda3-2020.11-Linux-x86_64.sh

The part "Anaconda3-2020.11" may change with new releases.

- Press Enter (or Return) to start the download. The file is large and may take some time to download completely.

- Once the download is finished and you're back at the command prompt, type the following command, replacing the filename with the one you just downloaded:

      bash ~/Downloads/Anaconda3-2020.11-Linux-x86_64.sh

- Read through the license agreement and agree to it by typing Yes.

- During installation, you'll be prompted to press Enter (or Return) to accept the default installation location, which is a directory named "anaconda3" in your home directory. We suggest sticking with the default installation location. The installation process will take a few minutes.

- The installer will then prompt you to initialize Anaconda3 by running conda init. We recommend typing yes. If you type no, conda will not make any changes to your shell
- Once the installation is complete, you'll see this output:

            {{< output >}}

            Thank you for installing Anaconda!

             {{< /output >}}

- To apply the installation changes, type source ~/.bashrc.

- To verify the installation, enter `conda info`. The output should look something like this:

          {{< output >}}
          active environment : base
          active env location : /home/example_user/anaconda3
          shell level : 1
          user config file : /home/example_user/.condarc
          populated config files :
          conda version : 4.10.1
          conda-build version : 3.20.5
          python version : 3.8.5.final.0
          virtual packages : __linux=5.4.0=0
                              __glibc=2.31=0
                                  __unix=0=0
                          __archspec=1=x86_64
       base environment : /home/example_user/anaconda3  (writable)
      conda av data dir : /home/example_user/anaconda3/etc/conda
      cond a av metadata url : https://repo.anaconda.com/pkgs/main
      channel URLs : https://repo.anaconda.com/pkgs/main/linux-64
                       https://repo.anaconda.com/pkgs/main/noarch
                       https://repo.anaconda.com/pkgs/r/linux-64
                       https://repo.anaconda.com/pkgs/r/noarch
          package cache : /home/example_user/anaconda3/pkgs
                          /home/example_user/.conda/pkgs
       envs directories : /home/example_user/anaconda3/envs
                          /home/example_user/.conda/envs
               platform : linux-64
             user-agent : conda/4.10.1 requests/2.25.1 CPython/3.8.5 Linux/5.4.0-72-generic ubuntu/20.04.2 glibc/2.31
                UID:GID : 1000:1000
             netrc file : None
           offline mode : False
           {{< /output >}}

- Finally, delete the installer file by running the following command. Make sure to replace "filename" with the name of the file you downloaded earlier:

                        rm Anaconda3-2020.11-Linux-x86_64.sh

## Updating Anaconda on Ubuntu

Updating Anaconda on Ubuntu involves two simple steps:

- Update the conda utility by typing conda update conda.

- After that, update Anaconda itself by typing conda update anaconda.

## Additional Resources

- Explore the extensive knowledge base for Anaconda Individual Edition (and other versions) on the official website.

- For CLI users, understanding the conda utility is especially important.
