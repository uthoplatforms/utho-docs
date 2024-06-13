---
title: "How to install Python on Ubuntu 22.04"
date: "2023-03-14"
---

![How to install Python on Ubuntu 22.04](images/How-to-install-Python-on-Ubuntu-22.04_utho.jpg)

## Introduction

In this article, you will learn how to install Python on Ubuntu 22.04.

[Python](https://en.wikipedia.org/wiki/Python_(programming_language)) is a high-level programming language that can be interpreted and is object-oriented. Python also has dynamic semantics. Its high-level data structures that are built in, in conjunction with dynamic typing and dynamic binding, make it a very attractive choice for Rapid Application Development. Additionally, its use as a scripting language or glue language to connect already existing components makes it a very versatile choice.

## Step 1: Add the deadsnakes PPA to Ubuntu's list of repositories

```
# apt install software-properties-common

```

![Install software](images/image-857.png)

After you have finished installing the programme, you may use the following shell command to add the deadsnakes repository to your system.

```
# add-apt-repository ppa:deadsnakes/ppa

```

![Repository](images/image-858.png)

You are going to be asked to verify that the activity was performed. Click enter to continue.

In most cases, an update is triggered when a repository is added. You can perform the task manually if it does not.

```
# apt update

```

## Step 2: Python installation and setup

Installing Python 3.11  is a reasonably simple process once you have added the repository.  You can install it using apt in the manner described here.

```
# apt install python3.11

```

## Step 3: Establishing Python 3.11 as the standard version

Congrats on the successful installation of Python 3.11. But you might want to take note that the default Python version is still the one you had before.

The following command will allow you to make the changes necessary.

```
# update-alternatives --install /usr/bin/python python /usr/bin/python3.11 1

```

Now that you've tested the Python version, you should notice that it's 3.11. Run the below command to check the Python version.

```
# python --version

```

![How to install Python on Ubuntu 22.04](images/image-859.png)

## Conclusion

Hopefully, now you have learned how to install Python on Ubuntu 22.04.

Also Read: [How to Install NGINX Web Server on Ubuntu 22.04 LTS](https://utho.com/docs/tutorial/how-to-install-nginx-web-server-on-ubuntu-22-04-lts/)

Thank You 🙂
