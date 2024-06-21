---
title: "How to Install Minikube on Ubuntu server"
date: "2023-04-19"
title_meta: "Minikube installtion guide on Ubuntu"
description: "Learn how to install Minikube on Ubuntu Server with this comprehensive guide. Follow these step-by-step instructions to set up Minikube, a tool for running Kubernetes clusters locally, on your Ubuntu server for efficient container orchestration and development.
"
keywords: ["install Minikube Ubuntu server", "Minikube setup Ubuntu server", "Ubuntu server Minikube installation guide", "Kubernetes development tool Ubuntu server", "Ubuntu server Minikube tutorial", "Minikube installation steps Ubuntu server", "container orchestration Ubuntu server", "Minikube Ubuntu server instructions"]

tags: ["Minikube", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/how-to-install-minikube-on-ubuntu-server/']
tab: true
---

<figure>

![How to Install Minikube on Ubuntu server](images/How-to-install-minikube-on-Ubuntu-22.04.png)

<figcaption>

How to Install Minikube on Ubuntu server

</figcaption>

</figure>

This post will show you how to install Minikube on Ubuntu 22.04 or 20.04 LTS. Minikube is a single-node Kubernetes (k8s) cluster. If you're new to Kubernetes and want to learn about it and test deploying apps on it, minikube is the way to do it. It gives you a command line interface for managing a Kubernetes (k8s) cluster and all of its parts.

## Prerequisites

- Super user or any normal user with SUDO privileges.
- Ubuntu server with up to date security patches
- Machine with pre-installed docker containers. If you did not installed Docker in you machine yet, kindly follow [this article to install Docker on Ubuntu](https://utho.com/docs/tutorial/how-to-install-docker-on-ubuntu-22-04/).

## Steps to install Minikube on Ubuntu

Step 1: Update your apt repositories and make sure you have installed all security patches

```
 # apt update && apt upgrade -y 
```

**Step 2:**Run the following command to install the necessary requirements for Minikube,

```
# apt install -y curl wget apt-transport-https 
```

Step 3: Use the curl command below to get the newest minikube binaries,

```
# curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 
```

Step 4: Once the binary has been downloaded, transfer it to /usr/local/bin and make sure it can be run.

```
# install minikube-linux-amd64 /usr/local/bin/minikube 
```

Step 5: Now verify the installed minikube version

```
# minikube version 
```

<figure>

![installed Minikube version ](images/image-890.png)

<figcaption>

Installed Minikube version

</figcaption>

</figure>

## Install Kubectl binary

Step 6: Kubectl is a command line tool that lets you talk to a [Kubernetes cluster](https://www.vmware.com/topics/glossary/content/kubernetes-cluster.html#:~:text=What%20is%20a%20Kubernetes%20cluster,and%20flexible%20than%20virtual%20machines.). It is used to control deployments, services, pods, and other things. Use the curl command shown below to get the most recent version of kubectl.

```
 # curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
```

Step 7: Once kubectl is downloaded, configure the kubectl binary to be executable and transfer it to /usr/local/bin.

```
 # chmod +x kubectl  
```
# mv kubectl /usr/local/bin/ 
```

```

Step 8: Now, let's check the installed version of kubectl.

```
# kubectl version -o yaml 
```

<figure>

![Installed version of kubectl](images/image-898.png)

<figcaption>

Installed version of kubectl

</figcaption>

</figure>

## Start the Minikube

Step 9: As we said in the beginning, we would be using docker as the foundation for minikube, so start the minikube with the docker driver, execute

```
$ minikube start --driver=docker 
```

Note:: If you run the above command with root privileges, you will encounter the below error. To overcome this, either use --force argument or execute the above command with any normal user.

<figure>

![](images/image-899-1024x165.png)

<figcaption>

Error while starting minikube with super user

</figcaption>

</figure>

```
# minikube start --drive=docker --force 
```

<figure>

![start the minikube with the docker driver](images/image-903-1024x399.png)

<figcaption>

start the minikube with the docker driver

</figcaption>

</figure>

Note: If you wish to start minikube with custom resources and have the installer choose the driver automatically, you may execute the following command:

```
minikube start --addons=ingress --cpus=3 --cni=flannel --install-addons=true --kubernetes-version=stable --memory=4g
```
Step 10: Run the minikube command below to see what's going on.

```
# minikube status 
```

```
Output:

minikube
type: Control Plane
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

Step 11: Run the following kubectl command to check the version of Kubernetes, the status of each node, and cluster information.

```
kubectl cluster-info && kubectl get nodes
```
<figure>

![Cluster information after installing minikube](images/image-901.png)

<figcaption>

Cluster information after installing minikube

</figcaption>

</figure>
