---
title: "How to install Flatpak on Fedora 35"
date: "2022-10-08"
---

![](images/How-to-install-Flatpak-on-Fedora-35_utho.jpg)

## Introduction

In this tutorial, we are going to learn how to install Flatpak on Fedora 35.

Flatpak is a [Linux](https://utho.com/docs/tutorial/category/linux-tutorial/) tool used for managing software packages and deploying applications. With Flatpak, users can execute applications independently from the rest of the system in a sandbox.

[Flatpak](https://flatpak.org/) tries to be as environment- and framework-neutral as possible, making it compatible with a wide variety of desktop environments.

## **Advantages of using Flatpaks on Fedora**

1\. Updating applications is simple and does not require a system restart.

2\. Fedora silverblue has a simple application installation process.

3\. All officially approved Fedora releases are suitable with Flatpaks.

4\. In other words, users of any distribution can use Flatpaks.

Install Flatpak on Fedora 35

## 1\. Install updates

The first thing for any system is to update its repositories in order to make them up to date.

```
# dnf update -y
```

## 2\. Install Flatpak dependencies

```
# dnf install fedmod
```

## 3\. Install Flatpak on Fedora 35

```
# dnf install flatpak-module-tools
```

Check the version of installed Flatpak

```
# flatpak --version
```

![install Flatpak on Fedora](images/10-6.png)

## 4\. Add user to mock group

Mock is a tool for creating RPM packages in a repeatable manner. Fedora's build system uses it to create a chroot environment for compiling RPM source packages.

To add the user into mock group use the following command

```
# usermod -a -G mock $USER
```

Restart your system for the changes to take effect.

## 5\. Install Flatpak runtime environment

Since Flatpak is a container on the Fedora platform, we need to allow remote testing on our machine.

**Add remote testing**

```
# flatpak remote-add fedora-testing oci+https://registry.fedoraproject.org#testing
```

**Enable remote testing**

```
# flatpak remote-modify --enable fedora-testing
```

**Test Flatpak**

You can test its functionality by running the following comman

```
# flatpak install fedora-testing org.fedoraproject.Platform/x86_64/f35
```

## 6\. Enable Flathub

Flatpak's app repository is called Flathub. Basically, it's a mirror of Dockerhub, but for docker images. Simply enter this command to install any programme you like.

```
# wget https://flathub.org/repo/flathub.flatpakrepo
```

The following command, for some reason, can be used independently on both GNOME and KDE Fedora installations. Add the Fathub remote manually with the following command if that fails to work.

```
# flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Install app with Flathub

Let's have a look at a real-world application installation using Flathub as an example. Okay, let's set up steam.

```
# flatpak install flathub com.valvesoftware.Steam
```

To **run steam** do the following

```
# flatpak run com.valvesoftware.Steam
```

## Conclusion

On Fedora 35, we were able to install Flatpak without any problems.

Thank You 🙂
