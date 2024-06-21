---
title: "How To Install Git on Fedora"
date: "2022-10-07"

Title_meta: GUIDE TO Install Git on Fedora

Description: This guide provides clear, step-by-step instructions on installing Git on Fedora. Learn how to set up Git, a popular version control system, enabling efficient collaboration and code management on your Fedora system.

Keywords: ['Git', 'Fedora', 'install Git', 'version control system', 'code management']

Tags: ["Git", "Fedora", "Version Control System", "Code Management"]
icon: "fedora"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/Fedora/how-to-install-git-on-fedora/']
tab: true
---

![How To Install Git on Fedora](images/How-To-Install-Git-on-Fedora_utho.jpg)

## Introduction

In this article, you will learn how to install Git on Fedora.

**[Git](https://en.wikipedia.org/wiki/Git)** is free and open source software for distributed version control: tracking changes in any set of files, usually used for coordinating work among programmers collaboratively developing source code during software development. Its goals include speed, data integrity, and support for distributed, non-linear workflows (thousands of parallel branches running on different systems)

Git is used to track the history of changes made to a project and its associated files. Git keeps all of this data in a repository. All of a project's files and their respective revision histories are stored in a central location called a repository. Git is a version control system (VCS) that facilitates collaboration between programmers working on the same project by letting them monitor changes, roll back to a prior revision, and so on.

## Installing Git using DNF

Git packages are available in the standard Fedora repositories. A previous version, however, is included. Use the following command to install git for the Fedora operating system.

```
# sudo dnf install git -y
```

When you've finished the preceding, Git will be properly installed on your computer. Use the following command to see what version of git you're working with:

```
# git --version
```

![command output](images/image-280.png)

## Setting Up Git

The next step is to set up Git such that the commit messages it creates accurately reflect who you are.

In order to customize Git, you must provide your name and email address in the appropriate fields, as shown below:

```
# git config --global user.name "microhost"
```

```
# git config --global user.email "abc@microhost.com"
```

## Once Git has been set up, the setup can be tested with the following command:

```
# git config --list
```

The following is an example of what you might see:

user.name=microhost

user.email=abc@microhost.com

This guide helped you set up the most recent version of Git on a Fedora Linux machine.

## Conclusion

Hopefully, you have learned how to install Git on Fedora.

Also read: [How to Install MongoDB on Fedora 36/35/34](https://utho.com/docs/tutorial/how-to-install-mongodb-on-fedora-36-35-34/)

Thank You 🙂
