---
title: "Python 3 Installation and Programming Environment Configuration on an Ubuntu 22.04"
date: "2022-09-16"
---

![Python 3 Installation and Programming Environment Configuration on an Ubuntu 22.04](images/Python-3-Installation-and-Programming-Environment-Configuration-on-an-Ubuntu-22.04-1024x576.png)

## Introduction

The Python programming language is an increasingly popular choice for both beginners and experienced developers. Flexible and versatile, Python has strengths in scripting, automation, data analysis, machine learning, and back-end development.

## Prerequisite

On an Ubuntu 22.04 server, you will need a super user such as root or any non-root user with sudo privileges in order to follow this tutorial.

## 1\. Setup Python3

Python 3 comes pre-installed on Ubuntu 22.04 and other Debian Linux distributions. To ensure your versions are current, you should update your local package index

```
# apt update
```

Then upgrade the packages installed on your system to ensure you have the latest versions

```
# apt upgrade -y 
```

Once the process is complete, check the version of Python 3 that is installed in the system by running the following

```
# python3 -V 
```

<figure>

![](images/image-66.png)

<figcaption>

output

</figcaption>

</figure>

Let's install pip, a tool for installing and managing programming packages we might want to use in our development projects, to manage Python software packages.

```
# apt install -y python3-pip 
```

The following can be used to install Python packages:

```
# pip3 install package_name 
```

Here, package name can be any Python package or library, like Django for web development or NumPy for scientific computing. So, if you want to install NumPy, you can use the command pip3 install numpy to do so.  
  
Similarly, if you are venturing into python-based web scraping, you might find libraries like Scrapy or BeautifulSoup useful. However, modern-day web scraping has evolved to match with robust website anti-bot measures. Take ZenRows for instance, it's a [next-generation web scraping API](https://www.zenrows.com/) that takes care of everything from rotating proxies to bypassing advanced anti-bot systems, easing the task for programmers. It's a valuable tool to have in your python web scraping toolkit.

You still need to install a few more packages and development tools to make sure that your programming environment is set up well.

```
# apt install -y build-essential libssl-dev libffi-dev python3-dev 
```

After setting up Python and installing pip and other tools, you can set up a virtual environment for your development projects.

## 2\. Setting Up a Virtual Environment

Using virtual environments, you can give each of your Python projects its own space on your server. This way, each project can have its own set of dependencies that won't affect any of your other projects.

Setting up a programming environment gives you more control over Python projects and how different versions of packages are handled. This is very important when working with packages from other companies.

As many Python programming environments as you want can be set up. Each environment is basically a directory or folder on your server that has a few scripts in it to make it work as an environment.

While there are a few ways to achieve a programming environment in Python, we’ll be using the `venv` module here, which is part of the standard Python 3 library. Let’s install `venv` by running the following:

```
# apt install -y python3-venv 
```

Once this is set up, you can start making environments. Let's choose which directory we want to put our Python programming environments in, or use mkdir to make a new directory, as in:

```
# mkdir environments 
```

Then go to the directory where you'll keep your programming environments:

```
# cd environments 
```

Once you are in the directory where you want the environments to live, you can run the following command to create an environment:

```
# python3 -m venv my_env
```

Basically, pyvenv creates a new directory with a few files that we can look at with the ls command:

```
# ls my_env 
```

These files work together to make sure that your projects are separated from the rest of your server, so that system files and project files don't get mixed up. This is a good way to keep track of versions and make sure that each of your projects can use the packages it needs.

To use this environment, you need to turn it on. You can do this by typing the following command, which runs the activate script:

```
# source my_env/bin/activate
```

Your environment's name, in this case my env, will now appear at the beginning of your command prompt. Your prefix may look a little different depending on which version of Debian Linux you are using, but the name of your environment in parentheses should be the first thing you see on your line:

`(my_env) root@ubuntu:~/environments$`

This prefix tells us that the environment my env is currently running. This means that when we make programmes here, they will only use the settings and packages for this environment.

After following these steps, your virtual environment is ready to use.

## 3\. Creating a “Hello, World” Program

Now that we've set up our virtual environment, let's make a simple "Hello, World!" programme. This will let us try out our environment and give us a chance to learn more about Python, if we don't already.

To do this, open up a command line text editor such as vi and create a new file:

```
# vi helloworld.py 
```

Once the text file opens in the terminal window, type out the programme:

```
print("Hello, World!") 
```

Once you exit the editor and return to your shell, you can run the program:

```
# python hello.py 
```

The output of the hello.py programme you made should look like this in your terminal:

```
Hello, World!
```

Type the command deactivate to leave the environment and go back to your original directory.
