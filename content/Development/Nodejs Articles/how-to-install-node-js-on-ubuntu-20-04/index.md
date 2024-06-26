---
title: "How To Install Node.js on Ubuntu 20.04"
date: "2022-09-15"
title_meta: "Guide to install Node.js on Ubuntu 20.04"
description: "Learn how to install Node.js on Ubuntu 20.04 with this comprehensive guide. Follow these step-by-step instructions to set up Node.js, a JavaScript runtime environment, on your Ubuntu 20.04 system for JavaScript development.
"
keywords: ["install Node.js Ubuntu 20.04", "Node.js setup Ubuntu 20.04", "Ubuntu 20.04 Node.js installation guide", "JavaScript runtime Ubuntu", "Node.js Ubuntu tutorial", "Node.js installation steps Ubuntu", "JavaScript development Ubuntu", "Node.js Ubuntu 20.04 instructions"]


tags: ["Node.js", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/how-to-install-node-js-on-ubuntu-20-04/']
tab: true
---

![How To Install Node.js on Ubuntu 20.04](images/How-To-Install-Node.js-on-Ubuntu-20.04_utho.jpg)

In this article you will see How To Install [Node.js](https://en.wikipedia.org/wiki/Node.js) on Ubuntu 20.04..

**Introduction**

Node.js is a runtime for JavaScript server-side development. It enables developers to design scalable backend functionality using JavaScript, a language that many web browser developers are already familiar with.

This post will demonstrate three distinct methods for installing Node.js on an Ubuntu 20.04 server:

_Installing the nodejs package from [Ubuntu's](https://utho.com/docs/tutorial/4-effective-ways-to-determine-the-name-of-a-plugged-usb-device-in-linux/) default software repository using apt  
_Using apt with an alternative PPA software repository to install specific versions of the nodejs package  
\*installing nvm, the Node Version Manager, and using it to install and manage different versions of Node.js.

**Prerequisites**

This guide assumes you are using Ubuntu 20.04. Set up a non-root user account with sudo privileges on your system before you start.

## Step.1 Apt Installation of Node.js from the Default Repositories

Node.js is included in Ubuntu 20.04's default repositories and can be used to deliver an uniform user experience across many platforms. The version in the repositories as of this writing is 10.19. Although this won't be the most recent version, it should be reliable and sufficient for fast language testing.

**warning**Node.js version 10.19, which came with Ubuntu 20.04, is no longer supported or maintained. Use one of the other sections of this guide to install a more recent version of Node instead of using this one in production.

```
#sudo apt update
```

install Node.js:

```
#sudo apt install nodejs
```

Check the install by querying node's version.

```
#node -v
```

```
Output  
v10.19.0
```

This is all there is to getting started with Node.js if the package in the repositories meets your needs. You should typically install npm, the Node.js package manager, as well. Install the npm package using apt to accomplish this:

```
#sudo apt install npm
```

This instals Node.js modules and packages.

You've installed Node.js and npm using apt and Ubuntu's default repositories. Next, we'll use an other repository to install Node.js.

## Step.2 Apt Installation of Node.js Using a NodeSource PPA

NodeSource maintains a PPA to install different versions of Node.js. These PPAs contain more Node.js versions than Ubuntu. Node.js v12, v14, and v16 are available.

Install the PPA to acquire its packages. Replace 16.x with your selected version when retrieving the installation script from your home directory using curl (if different).

```
#cd ~
```  
```
#curl -sL https://deb.nodesource.com/setup_16.x -o /tmp/nodesource_setup.sh
```

Consult NodeSource's documentation for version details.

Open the script in nano (or your chosen text editor):

```
#nano /tmp/nodesource_setup.sh
```

Exit your editor and run the script with sudo once you are sure it is safe to do so.

```
#sudo bash /tmp/nodesource_setup.sh
```

The PPA will be added to your settings, and your local cache of installed packages will be automatically updated. Installing the Node.js package is now possible in the same manner as in the previous section:

```
#sudo apt install nodejs
```

Run node with the -v version flag to confirm that the new version has been installed:

```
#node -v
```

```
Output  
v16.6.1
```

The NodeSource nodejs package contains both the node binaries and npm, so you do not need to independently install npm.

Using apt and the NodeSource PPA, you have successfully installed Node.js and npm. The following section will demonstrate how to use Node Version Manager to install and manage several Node.js versions.

## Step.3 Node Version Manager installation

nvm, the Node Version Manager, is an alternative method for installing Node.js that is especially flexible. This piece of software enables the installation and maintenance of many independent versions of Node.js and their accompanying Node packages.

To install NVM on a PC running Ubuntu 20.04, visit the project's GitHub website. The main page's README file contains the curl command. This will retrieve the most recent installation script version.

Before passing the command to bash, it is usually a good idea to audit the script to ensure that it doesn't perform any actions that you disagree with. Remove the | bash element from the end of the curl command to accomplish this.

```
#curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh
```

Check to see whether you like the modifications. When done, run the command with | bash added. The script can be downloaded and run by typing:

```
#curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

This will install the nvm script for the specified user. Before you can use it, you must source your.bashrc file:

```
#source ~/.bashrc
```

You can now ask NVM about Node versions:

```
#nvm list-remote
```

```
Output  
. . .  
v14.16.0 (LTS: Fermium)  
v14.16.1 (LTS: Fermium)  
v14.17.0 (LTS: Fermium)  
v14.17.1 (LTS: Fermium)  
v14.17.2 (LTS: Fermium)  
v14.17.3 (LTS: Fermium)  
v14.17.4 (Latest LTS: Fermium)  
v15.0.0  
v15.0.1  
v15.1.0  
v15.2.0  
v15.2.1  
v15.3.0  
v15.4.0  
v15.5.0  
v15.5.1
```

It's a lengthy list! You can install Node by entering any of the displayed release versions. For example, to obtain version v14.10.0, type:

```
#nvm install v14.10.0
```

You can view the different versions installed by entering:

```
#nvm list
```

```
Output  
-> v14.10.0  
system  
default -> v14.17.4 (-> N/A)  
iojs -> N/A (default)  
unstable -> N/A (default)  
node -> stable (-> v14.10.0) (default)  
stable -> 14.10 (-> v14.10.0) (default))  
. . .
```

This displays the presently active version (-> v14.10.0) on the first line, followed by a list of named aliases and the versions to which those aliases point.

**Note**If you also have a Node.js version installed via apt, you may see a system entry here. You can always use nvm use system to activate the system-installed version of Node.

In addition, you will notice aliases for the various Node long-term support (LTS) releases:

```
Output  
. . .  
lts/* -> lts/fermium (-> N/A)  
lts/argon -> v4.9.1 (-> N/A)  
lts/boron -> v6.17.1 (-> N/A)  
lts/carbon -> v8.17.0 (-> N/A)  
lts/dubnium -> v10.24.1 (-> N/A)  
lts/erbium -> v12.22.4 (-> N/A)  
lts/fermium -> v14.17.4 (-> N/A)
```

Additionally, we can install a release based on these aliases. For instance, to install the latest version of fermium with long-term support, execute the following:

```
#nvm install lts/fermium
```

```
Output  
Downloading and installing node v14.17.4...  
. . .  
Now using node v14.17.4 (npm v6.14.14))
```

With nvm use, you can switch between installed versions.

```
#nvm use v14.10.0
```

```
Output  
Now using node v14.10.0 (npm v6.14.8)  
```

You can verify that the install was successful using the same technique from the other sections, by typing:
```

```
#node -v
```

```
Output  
v14.10.0
```

Our machine has the expected Node version. npm is also compatible.
