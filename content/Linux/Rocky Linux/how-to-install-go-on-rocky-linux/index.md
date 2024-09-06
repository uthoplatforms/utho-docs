---
title: "How to install GO on Rocky Linux"
date: "2023-08-05"
title_meta: "GUIDE TO Configure GO on RockyLinux"

description: "A detailed guide on how to install Go, the programming language created by Google, on Rocky Linux."

keywords: ['Rocky Linux', 'Go', 'Golang', 'installation', 'programming language', 'Linux', 'development']

tags: ["GO"]
icon: "RockyLinux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/Rocky Linux/how-to-install-go-on-rocky-linux']
tab: true
---

![How to install Go on Rocky Linux](images/How-to-install-Go-on-Rocky-Linux.jpg)

## Introduction

In this article, you will learn how to install Go on Rocky Linux 8. [Golang](https://en.wikipedia.org/wiki/Go_(programming_language)), also known as Go, is an open-source and cross-platform programming language that can be set up on Linux, Windows, and macOS. The language is well-built so that professionals can use it to build applications. Go is easy to build and run, which makes it a great programming language for making software that works well. It is reliable, builds quickly, and has software that works well and can grow quickly.

Python's productivity and comparatively simple design served as an inspiration for the Go language. For effective dependency management, it makes use of goroutines, or lightweight processes, and a selection of packages. It was created to address a number of issues, such as long build times, unmanaged dependencies, effort duplication, challenges creating automated tools, and cross-language development.

## Prerequisites

- Yum repository configured on your server.

- Any normal user with Sudo privileges or Super user to download packages

## Steps to Install Go on Rocky Linux

Step 1: First, download the latest Go package from the [official page](https://go.dev/dl/) using wget command( Click here, to learn more [about wget command](https://utho.com/docs/tutorial/download-online-resources-from-the-command-line-with-wget/)).

```
wget https://go.dev/dl/go1.20.5.linux-amd64.tar.gz
```
Step 2: Now, Create a new Go tree in /usr/local/go by extracting the archive you just downloaded into /usr/local:

```
tar -C /usr/local/ -xzvf go1.20.5.linux-amd64.tar.gz
```
Step 3: Set the Environmental variable PATH on your server.

```
export PATH=$PATH:/usr/local/go/bin
```
Step 4: That is it. you have successfully install Go on your machine. However, to check whether is it installed correctly or not, execute the following command.

```
go version
```
And, this is how you have learnt how to install Go on your Rocky linux
