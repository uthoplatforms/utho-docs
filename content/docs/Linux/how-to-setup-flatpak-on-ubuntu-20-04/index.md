---
title: "How to Setup Flatpak on Ubuntu 20.04"
date: "2022-10-06"
---

![How to Setup Flatpak on Ubuntu 20.04](images/How-to-Setup-Flatpak-on-Ubuntu-20.04_utho.jpg)

## Introduction

In this article you will learn to Setup Flatpak on Ubuntu 20.04, When it comes to creating and distributing [](https://utho.com/docs/tutorial/category/linux-tutorial/)Linux desktop applications, Flatpak is the latest technology. Follow the steps in this article to set up [Flatpak](https://en.wikipedia.org/wiki/Flatpak) packages on Ubuntu 20.04 and manage them.

A application for managing software deployment and packages on Linux, originally known as xdg-app, Flatpak is now known as just Flatpak. It is promoted as having a sandbox environment, in which users are able to operate application software while remaining completely separate from the rest of the system.

In order for applications that use Flatpak to gain access to resources such as Bluetooth, sound (using PulseAudio), the network, and files, the appropriate permissions must be granted. These rights are established by the person who is in charge of maintaining the Flatpak package, but users on their own systems have the ability to add or delete them.

Another important benefit of using Flatpak is that it enables application developers to directly offer updates to end users, bypassing distributions in the process. This eliminates the need for the developers to package and test the programme in a manner specific to each distribution.

## 1\. Keep the server updated

```
# apt upgrade -y
```

## 2\. Install Flatpak

```
# apt install flatpak -y
```

## 3\. Add the Flathub repository

The best site to find and download Flatpak applications is Flathub. If you want to make use of it, type:

```
# flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

## 4\. Install apps

```
# flatpak install flathub com.nordpass.NordPass -y
```

To run the app, use the following command:

```
# flatpak run com.nordpass.NordPass
```

To update the app, use following command:

```
# flatpak update com.nordpass.NordPass
```

To list all installed app, use following command:

```
# flatpak list
```

![Setup Flatpak on Ubuntu](images/image-257.png)

## Conclusion

Hopefully, you now understand how to setup Flatpak on Ubuntu 20.04. The setup process was successful and is now complete.

Thank You 🙂
