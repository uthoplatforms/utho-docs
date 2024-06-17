---
title: "How To Install Node.js on CentOS 8"
date: "2022-09-16"
---

![How To Install Node.js on CentOS 8](images/How-To-Install-Node.js-on-CentOS-8_utho.jpg)

**introduction**

In this aricle you will learn How To Install [Node.js](https://en.wikipedia.org/wiki/Node.js) on CentOS 8..

Node.js is a runtime for JavaScript server-side development. It enables developers to design scalable backend functionality using JavaScript, a language that many web browser developers are already familiar with.

In this post, we will demonstrate three distinct methods for installing Node.js on a CentOS 8 server:

utilising dnf to install the nodejs package from CentOS's default AppStream repository utilising nvm, the Node Version Manager, to install and manage different versions of node constructing and installing node from source

The majority of users should utilise dnf to install the pre-packaged versions of Node that are pre-installed with their operating system. Use the nvm method if you are a developer or otherwise require the management of many Node installations. Rarely are most users required to build from source.

To Install [Node.js](https://utho.com/docs/tutorial/an-introduction-to-the-linux-alternatives-command/) on CentOS 8 follow the below steps that will help you to increase you knowledge..

**Prerequisites**

You will require a server running CentOS 8 in order to finish this tutorial. We'll presume you're signed in as a non-root, sudo-capable user on this server.

## Step .1 Node installation from the CentOS AppStream Repository

Node.js is accessible via the default AppStream software repository on CentOS 8. Multiple versions are available, and you can choose between them by activating the desired module stream. Using the dnf command, list the available streams for the nodejs module.

```
#sudo dnf module list nodejs
```

```
Output  
Name Stream Profiles Summary  
nodejs 10 [d] common [d], development, minimal, s2i Javascript runtime  
nodejs 12 common, development, minimal, s2i 
```

There are two streams available: 10 and 12. The \[d\] indicates that the default stream is version 10. Switch module streams now if you prefer to install Node.js 12:

```
#sudo dnf module enable nodejs:12
```

You will be asked to confirm your choice. The version 12 stream will then be enabled, and we can proceed with the installation. See the official CentOS AppStream documentation for more information on working with module streams.

Using dnf, install the nodejs package:

```
#sudo dnf install nodejs
```

dnf will ask you to confirm the actions it will take once more. To do so, press y then ENTER, and the software will be installed.

Check that the installation was successful by inquiring about the version number of node:

```
#node --version
```

```
Output  
v12.13.1
```

If you installed Node.js 10 instead, the answer you get from —version will be different.

**Note**Both Node.js versions are long-term support releases, which means they have a longer guaranteed window of maintenance. More lifecycle information can be found on the official Node.js releases page.

The npm Node Package Manager tool should be installed as a dependency along with the nodejs package. Check to make sure it was installed correctly as well:

```
#npm --version
```

```
Output  
6.12.1
```

At this point, you have successfully used the CentOS software repositories to install Node.js and npm. In the next section, you'll learn how to do this with the help of the Node Version Manager.

## Step.2 Node Version Manager installation

Using nvm, the Node Version Manager, is another flexible method of installing Node.js. Using this piece of software, you can simultaneously install and maintain numerous separate Node.js versions and their corresponding Node packages.

Visit the project's GitHub website to learn how to install NVM on a CentOS 8 system. From the README file that appears on the home page, copy the curl command. You can obtain the installation script's most recent version by doing this.

It is always a good idea to audit the script to make sure it isn't doing anything you don't agree with before pipelining the command through to bash. The | bash segment at the end of the curl command can be removed to do that:

```
#curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh
```

Check to see whether you like the modifications. When done, run the command with | bash added. The script can be downloaded and performed by typing:

```
#curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash
```

This adds the nvm script to your user account. To use it, first locate your.bash profile file:

```
#source ~/.bash_profile
```

You are now able to inquire with NVM regarding the versions of Node that are available:

```
#nvm list-remote
```

```
  
v12.13.0 (LTS: Erbium)  
v12.13.1 (LTS: Erbium)  
v12.14.0 (LTS: Erbium)  
v12.14.1 (LTS: Erbium)  
v12.15.0 (LTS: Erbium)  
v12.16.0 (LTS: Erbium)  
v12.16.1 (Latest LTS: Erbium)  
v13.0.0  
v13.0.1  
v13.1.0  
v13.2.0  
v13.3.0  
v13.4.0  
v13.5.0  
v13.6.0
```

It's a lengthy list! By typing any of the release versions you see, you can install a particular version of Node. For instance, you can type: to receive version v13.6.0.

```
#nvm install v13.6.0
```

You can see what versions you have by typing:

```
#nvm list
```

```
Output  
-> v13.6.0  
default -> v13.6.0  
node -> stable (-> v13.6.0) (default)  
stable -> 13.6 (-> v13.6.0) (default)
```

The first line shows the currently active version (-> v13.6.0), followed by some named aliases and the versions to which those aliases point.

**Note** If you also have a version of Node installed from the CentOS software repositories, you may see a system -> v12.13.1 (or some other version number) line here. You can always use nvm use system to turn on the system version of Node.

In addition, you will notice aliases for the various Node long-term support (LTS) releases:

```
Output  
lts/* -> lts/erbium (-> N/A)  
lts/argon -> v4.9.1 (-> N/A)  
lts/boron -> v6.17.1 (-> N/A)  
lts/carbon -> v8.17.0 (-> N/A)  
lts/dubnium -> v10.19.0 (-> N/A)  
lts/erbium -> v12.16.1 (-> N/A)
```

With these aliases, we can also install a release. For example, run the following to install the latest version of erbium that has long-term support:

```
#nvm install lts/erbium
```

```
Output  
Downloading and installing node v12.16.1...  
. . .  
Now using node v12.16.1 (npm v6.13.4)
```

With nvm, you can change between installed versions by using:

```
nvm use v13.6.0
```

\[/console\]Now using node v13.6.0 (npm v6.13.4)\[/console\]

Using the same method as in the other sections, you can check to see if the install went well by typing:

```
#node --version
```

```
Output  
v13.6.0
```

As expected, the correct version of Node is installed on our machine. There is also a compatible version of npm available.

## Step .3 Putting Node in place from its source code

You can also download the source code and compile it yourself to set up Node.js.

To do this, use your web browser to go to the official Node.js download page, right-click on the Source Code link, and choose Copy Link Address or a similar option.

Once you're back in your SSH session, make sure you're in a directory where you can write. We'll use the home directory of the user who is logged in:

```
#cd ~
```

After that, you should type curl, then paste the link that you copied from the webpage, and after that, you should type | tar xz:

```
#curl https://nodejs.org/dist/v12.16.1/node-v12.16.1.tar.gz | tar xz
```

This will download the source using curl, then pipe it directly to the tar utility, which will extract it into the current directory.

Enter the following code into the newly created source directory:

```
#cd node-v*
```

In order to compile the code, we need to get a few packages from the CentOS repositories. Install these now with dnf:

```
#sudo dnf install gcc-c++ make python2
```

You will be asked to confirm that you want to install. To do this, type y and press ENTER. We can now set up and build the software:

```
#./configure
```  
```
#make -j4
```

It will take a long time to put everything together (around 30 minutes on a four-core server). We used the -j4 option to make four compilations happen at the same time. You can leave this option out or change the number to match how many processor cores you have.

When the compilation is done, you can type: to install the software on your system.

```
#sudo make install
```

Ask Node to show its version number to see if the installation went well:

```
#node --version
```

```
output  
v12.16.1
```

If you see the right version number, that means the installation went well. By default, Node also instals a version of npm that works with it, so that should also be available.

Must read:- [https://utho.com/docs/tutorial/2-methods-for-re-running-last-executed-commands-in-linux/](https://utho.com/docs/tutorial/2-methods-for-re-running-last-executed-commands-in-linux/)

You should now understand How to Install Node.js on CentOS 8...

**Thankyou**
