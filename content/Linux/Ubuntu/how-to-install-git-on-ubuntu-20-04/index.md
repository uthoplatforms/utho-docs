---
title: "How to Install Git on Ubuntu 20.04"
date: "2022-10-06"
title_meta: "Guide to install Git on Ubuntu"
description: "Learn how to install Git on Ubuntu 20.04 with this step-by-step guide. Discover how to set up Git, a powerful version control system, for managing your code repositories on Ubuntu.
"
keywords: ["install Git Ubuntu 20.04", "Git setup Ubuntu", "Ubuntu 20.04 Git installation guide", "version control Ubuntu", "Ubuntu Git tutorial", "Git Ubuntu commands", "Git configuration Ubuntu", "Git setup steps Ubuntu"]

tags: ["Git", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/how-to-install-git-on-ubuntu-20-04/']
tab: true
---

![](images/How-to-Install-Git-on-Ubuntu-20.04_utho.jpg)

## Introduction

This article will explain how to install Git on Ubuntu 20.04. [Git](https://en.wikipedia.org/wiki/Git) is free and open source software for distributed version control: tracking changes in any set of files, usually used for coordinating work among programmers collaboratively developing source code during software development

Git is a piece of software that allows for the tracking of changes in any set of files, and it is typically used for the purpose of coordinating the work of multiple programmers who are working together on the development of source code for software. Git is both free and open source software. Speed, data integrity, and support for distributed, non-linear workflows (thousands of parallel branches operating on various systems) are some of its goals.

## Installing Git with Apt

```
# sudo apt update
```

```
# sudo apt install git
```

Execute the following command, which will print the version of Git, to ensure proper installation:

```
# git --version
```

![command output](images/image-250.png)

Now that you've installed Git on Ubuntu, you can begin using it.

## Configuring Git

Setting up your git username and email address is one of the first steps after installing Git. Each commit you make in Git is linked to your personal information.

Use the following commands to establish your global commit name and email address:

```
# git config --global user.name "Your Name"
```

```
# git config --global user.email "youremail@microhost.com"
```

Typing will check the settings modifications you made:

```
# git config --list
```

![command output](images/image-252.png)

The configuration settings are stored in the ~/.gitconfig file:

```
# vi ~/.gitconfig
```

![command output](images/image-254.png)

The result should look this:

![command output](images/image-255.png)

Either use the git config command (preferred) or manually edit the /.gitconfig file to make additional adjustments to your Git configuration.

If you are Looking to [Install Streamlit on Ubuntu 20.04](https://utho.com/docs/tutorial/how-to-install-streamlit-on-ubuntu-20-04/) I have already posted an article.

Thanks for reading this article on how to install Git on Ubuntu 20.04. I hope it helped. 🙂
