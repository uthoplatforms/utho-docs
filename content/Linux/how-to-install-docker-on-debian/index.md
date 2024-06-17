---
title: "How To Install Docker on Debian"
date: "2022-12-04"
---

![How To Install Docker on Debian](images/How-To-Install-Docker-on-Debian_utho.jpg)

## Introduction

In this article, you will learn how to install Docker on Debian.

[Docker](https://www.docker.com/) is a platform that is open source and allows developers to build, deploy, run, update, and manage containers. Containers are standardised, executable components that combine application source code with the operating system (OS) libraries and dependencies necessary to run that code in any environment. Docker enables developers to do all of these things.

Containers make it easier to design and deliver applications that run on distributed systems. As more and more businesses move their operations to cloud-native development and hybrid multicloud environments, their adoption rates have increased significantly. Developers have the ability to create containers even without the use of Docker by cooperating directly with features that are pre-installed in Linux and other operating systems. Docker, on the other hand, makes containerization more quickly, easily, and securely.

## Step 1: Update the system

The first step you need to do is get the repositories up to date. To accomplish this, use the following command:

```
# apt update -y
```

## Step 2: Install dependencies

For the installation to go off without a hitch, there are some prerequisites or dependencies that must be met. Therefore, in order to install them, execute the following command:

```
# apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common -y
```

## Step 3: Install Docker

The Docker packages are now served over the internet via the [HTTPS](https://utho.com/docs/tutorial/how-to-host-a-domain-on-centos-7/) protocol, which was previously used. In order to make use of the Docker package repository, you will need to add the GPG key that is associated with it.

```
# curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
```

Now, on your Debian machine, you need to execute the following command in order to add the Docker package repository.

```
# echo "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker-ce.list
```

Install Docker CE at this time by running the following command:

```
# apt install docker-ce docker-ce-cli containerd.io -y
```

This will install Docker as well as any extra packages, libraries, and dependencies that are necessary for Docker and any associated packages to function properly.

## Step 4: Manage Docker Service

```
# systemctl start docker
```

```
# systemctl enable docker
```

```
# systemctl restart docker
```

```
# systemctl status docker
```

![command output](images/image-557.png)

## Step 5: Confirm that Docker is installed

Execute the following command to check that Docker has successfully installed:

```
# docker version
```

![install Docker on Debian](images/image-556.png)

## Step 6: Test Docker

In order to evaluate Docker, we will obtain a 'hello-world' image from the Docker Hub repository. A container will be formed from the image that shows a "Hello world" message on the terminal along with the steps of what just transpired after the command has been done. This will take place after the image has been loaded.

```
# docker run hello-world
```

![command output](images/image-558.png)

Execute the following command to verify the photos that are currently stored on the system:

```
# docker images
```

![output](images/image-559.png)

## Conclusion

Hopefully, you have learned how to install Docker on Debian.

Thank You 🙂
