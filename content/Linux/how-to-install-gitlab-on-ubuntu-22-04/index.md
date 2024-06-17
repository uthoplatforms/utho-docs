---
title: "How to install GitLab on Ubuntu 22.04"
date: "2023-07-27"
---

![How to install GitLab on Ubuntu 22.04](images/How-to-install-GitLab-on-Ubuntu-22.04-1024x576.jpg)

## Introduction

In this article, you will learn how to install GitLab on Ubuntu 22.04.

[GitLab](https://en.wikipedia.org/wiki/GitLab) is an open-source tool that makes it easy to manage repositories, issues, CI/CD pipelines, and much more. If you use Ubuntu 22.04 or 20.04 and want to set up your own GitLab instance to streamline your DevOps process, you're in the right place.

This step-by-step tutorial will show you how to install GitLab on Ubuntu 22.04 or 20.04. There are two versions of GitLab: Enterprise Edition (EE) and Community Edition (CE). We'll talk about the community version in this post.

##### Step 1: Update the System

**Let's start by updating the list of packages and upgrading any packages that are already installed to their most recent versions.**

```
# apt update -y

```

![How to install GitLab on Ubuntu 22.04](images/image-1221.png)

```
# apt upgrade -y

```

##### Step 2: Install Dependencies

**GitLab needs some other things to work properly. Use the following instructions to set them up:**

```
# apt install -y curl openssh-server ca-certificates postfix

```

![dependencies](images/image-1222.png)

**During the postfix installation, there will be a configuration box. Select "Internet Site" and type the hostname of your machine as the mail server name. This will make it possible for GitLab to send emails.**

##### Step 3: Add GitLab Apt Repository

**Run the following curl command to add the GitLab project. It will instantly figure out what version of Ubuntu you have and set the repository to match.**

```
# curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | bash

```

##### Step 4: Install Gitlab

**Run the command below to quickly install and set up gitlab-ce on your Ubuntu system. Replace the server's hostname with the name of your setup.**

```
# EXTERNAL\_URL="http://gitlab.utho.net" apt install gitlab-ce

```

**Once the command above has been run properly, the output will look something like this:**

![install GitLab on Ubuntu](images/image-1220.png)

**The output shown above shows that GitLab has been set up correctly. The user name for the gitlab web interface is "root," and the password is saved at "/etc/gitlab/initial\_root\_password."**

##### Step 5: Access GitLab Web Interface

**Once GitLab is loaded and set up, open your web browser and type in the IP address or hostname of your server.**

![How to install GitLab on Ubuntu 22.04](images/image-1219.png)

## Conclusion

Hopefully, now you have learned how to install GitLab on Ubuntu 22.04.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
