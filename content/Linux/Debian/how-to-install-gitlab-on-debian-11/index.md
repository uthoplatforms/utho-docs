---
title: "How to install GitLab on Debian 11"
date: "2023-08-05"
title_meta: "GUIDE How to install Gitlab on Debian 11"

description: "A detailed guide on how to install GitLab, a web-based DevOps lifecycle tool, on Debian 11."

keywords: ['Debian 11', 'GitLab', 'installation', 'DevOps', 'Linux', 'version control', 'CI/CD']

tags: ["GitLab"]
icon: "Debian"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Debian/how-to-install-gitlab-on-debian-11/']
tab: true
---

![How to install GitLab on Debian 11](images/How-to-install-GitLab-on-Debian-11-1024x576.jpg)

## Introduction

In this article, you will learn how to install GitLab on Debian 11.

[GitLab](https://en.wikipedia.org/wiki/GitLab) is an open-source tool that makes it easy to manage repositories, issues, CI/CD pipelines, and much more. If you use Debian 11 and want to set up your own GitLab instance to streamline your DevOps process, you're in the right place.

This step-by-step tutorial will show you how to install GitLab on Debian 11. There are two versions of GitLab: Enterprise Edition (EE) and Community Edition (CE). We'll talk about the community version in this post.

##### Step 1: Update the System

**Let's start by updating the list of packages and upgrading any packages that are already installed to their most recent versions.**

```
# apt update -y

```

```
# apt upgrade -y

```

##### Step 2: Install Dependencies

**GitLab needs some other things to work properly. Use the following instructions to set them up:**

```
# apt-get install curl ca-certificates apt-transport-https gnupg2 -y

```

##### Step 3: Add GitLab Apt Repository

**Run the following curl command to add the GitLab project. It will instantly figure out what version of Debian you have and set the repository to match.**

```
# curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash

```

**At the time this guide was written, there was no GitLab package for Debian 11. You will need to change the code name for Debian 11 (Bullseye) in the GitLab source file to Debian 10 (Buster).**

```
# vi /etc/apt/sources.list.d/gitlab\_gitlab-ce.list

```

**Find the lines below**.

```
deb https://packages.gitlab.com/gitlab/gitlab-ce/debian/ bullseye main
deb-src https://packages.gitlab.com/gitlab/gitlab-ce/debian/ bullseye main
```

**Replaced them with the lines below.**

```
deb https://packages.gitlab.com/gitlab/gitlab-ce/debian/ buster main
deb-src https://packages.gitlab.com/gitlab/gitlab-ce/debian/ buster main
```

**Save the file, close it, and use the instructions below to update the repository.**

```
# apt-get update

```

##### Step 4: Install Gitlab

**Run the command below to quickly install and set up gitlab-ce on your Debian system.**

```
# apt-get install gitlab-ce -y

```

**Once the command above has been run properly, the output will look something like this:**

![install GitLab on Debian](images/image-1220.png)

**The output shown above shows that GitLab has been set up correctly. The user name for the gitlab web interface is "root," and the password is saved at "/etc/gitlab/initial\_root\_password"**

##### Step 5: Configure GitLab

**After the installation is done, we will set up GitLab to meet our needs. Here are the changes you can make**.

```
# vi /etc/gitlab/gitlab.rb

```

**Change your website name in the next line**.

```
external_url 'https://Your_Domain_Name'
```

**Save and close the file.**

**Let's now use the command below to update the repository**.

```
# gitlab-ctl reconfigure

```

##### Step 5: Access GitLab Web Interface

**Once GitLab is loaded and set up, open your web browser and type in the IP address or hostname of your server.**

![How to install GitLab on Debian 11](images/image-1219.png)

## Conclusion

Hopefully, now you have learned how to install GitLab on Debian 11.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
