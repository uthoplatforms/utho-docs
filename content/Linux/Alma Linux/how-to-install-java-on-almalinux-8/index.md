---
title: "How to install Java on Almalinux 8"
date: "2023-06-12"
title_meta: "GUIDE How to install Java on Almalinux"
description: "Learn how to install Java on AlmaLinux 8. Follow this guide to set up the Java Development Kit (JDK) and Java Runtime Environment (JRE) on your AlmaLinux 8 system."
keywords: ['Java', 'AlmaLinux 8', 'install', 'JDK', 'JRE', 'Java Development Kit', 'Java Runtime Environment']

tags: ["Java"]
icon: "Alma Linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/Alma Linux/how-to-install-java-on-almalinux-8/']
tab: true
---

![How to install Java on Almalinux 8](images/How-to-install-Java-on-Almalinux-8-1024x576.jpg)

## Introduction

In this article, you will learn how install Java on Almalinux 8.

The Java Platform comes in three different versions: Standard Edition (SE), Enterprise Edition (EE), and Micro Edition (ME). This guide is all about Java SE (Java Platform, Standard Edition). Almost all [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) software that is free and open source is made to work with Java SE.

The Java Runtime Environment (JRE) and the Java Development Kit (JDK) are both Java SE packages that can be installed (JDK). JRE is a version of the Java Virtual Machine (JVM), which lets you run Java programmes and applets that have already been built. The JDK comes with the JRE and other software needed to write, develop, and compile Java programmes and applets.

#### Step 1: Installing OpenJDK

**To install the OpenJDK using yum, you can run dnf install java.**

```
# dnf install java

```

**If you don't specify a version when you try to install Java, the most common stable version of the OpenJDK JRE will be used. As you can see from this output, that is java-1.8.0-openjdk as of this writing.**

**You should now have a Java setup that works. To make sure, run java -version to see what version of Java is currently installed in your environment:**

```
# java -version

```

![install Java on Almalinux 8](images/image-1175.png)

**To install the full OpenJDK JDK, you need to install the package with the name -devel added to it. Java follows this common practise for development packages for other programming environments.**

```
# dnf install java-devel

```

![How to install Java on Almalinux 8](images/image-1174.png)

#### Step 2: Installing Other OpenJDK Releases

**OpenJDK has changed the way it numbers its versions so that they are more in line with Oracle Java releases. Like with 1.8.0, you can put the version number in the package name to install a newer version of OpenJDK. To install OpenJDK 17, for example, you can type yum install java-17-openjdk:**

```
# dnf install java-11-openjdk-devel

```

#### Step 3: Setting Your Default Java Version

**If you have more than one version of Java installed, you may want to make one the default (i.e. the one that will run when a user runs the java command). Also, some programmes need certain environment variables to be set in order to find out which version of Java to use.**

**The default Java version can be set by using the alternatives command, which uses symbolic links to manage the default commands. Use alternatives –config java: to see a list of the versions of Java that alternatives can handle.**

```
# alternatives --config java

```

![How to install Java on Almalinux 8](images/image-1171.png)

**Enter the number of the a selection to choose which Java executable to use by default. It will change the symbolic links on your system so that the java command points to the right set of libraries. You can run this command as many times as you want, and each time the output of java -version should change:**

```
# java -version

```

![version](images/image-1170.png)

## Conclusion

Hopefully, now you have learned how to install Java on Almalinux 8.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
