---
title: "How to install Visual Studio Code on Debian 10"
date: "2023-04-29"
---

![How to install Visual Studio Code on Debian 10](images/How-to-install-Visual-Studio-Code-on-Debian-10_utho.jpg)

## Introduction

In this article, you will learn how to install Visual Studio Code on Debian 10.

[Visual Studio Code](https://en.wikipedia.org/wiki/Visual_Studio_Code) is a free code editor that was developed by Microsoft. It is compatible with several operating systems, including Linux, Windows, and Mac OS, and it is available for download. It is an effective tool that facilitates the debugging of code, the running of tasks, and the activation of version control.

It includes several features that set it apart from other code editors, such as automated code completion, snippets, refactoring, and syntax highlighting, amongst many more.

## Step 1: System update

**It is generally recommended that you update your operating system before installing any new software; as a result, the following command can be used by you to carry out this task.**

```
# apt update && apt upgrade -y

```

## Step 2: Install packages

**Now that the system has been updated, there are certain packages that need to be installed on your system before you can install the editor. You can only do this once the system has been updated.**

```
# apt install software-properties-common apt-transport-https wget -y

```

## Step 3: Import repository

**After the packages have been installed, the next step is to include the repository for Visual Studio Code; however, before that step can be completed, the Microsoft GPG key must be imported in order to authenticate the packages that have been installed.**

```
# wget -O- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | tee /usr/share/keyrings/vscode.gpg

```

**This will ensure that the packages that have been installed are genuine. Let's move on now and work toward merging the Microsoft Visual Source repository.**

```
# echo deb \[arch=amd64 signed-by=/usr/share/keyrings/vscode.gpg\] https://packages.microsoft.com/repos/vscode stable main | tee /etc/apt/sources.list.d/vscode.list

```

![How to install Visual Studio Code on Debian 10](images/image-877.png)

## Step 4: Update the system again

**After you have imported the repository and installed the packages, it is highly advised that you then update your operating system.**

```
# apt update -y

```

## Step 5: Install the editor

**To install Visual Studio Code Editor, all you need to do now is run the command that is provided below on the terminal.**

```
# apt install code

```

Run the following command to check the version:

```
# code --version --user-data-dir

```

![output](images/image-876.png)

**To launch the application, run the following command.**

```
# code &

```

## Conclusion

Hopefully, now you have learned how to install Visual Studio Code on Debian 10.

Also Read: [How to Install NGINX Web Server on Ubuntu 22.04 LTS](https://utho.com/docs/tutorial/how-to-install-nginx-web-server-on-ubuntu-22-04-lts/)

Thank You 🙂
