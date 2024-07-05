---
title: "How to install Jenkins on CentOS 7"
date: "2023-02-12"
title_meta: "Guide to install Jenkins on Centos7"
description: " This guide provides step-by-step instructions for installing Jenkins, a popular open-source automation server for continuous integration and delivery (CI/CD), on your CentOS 7 system. "

keywords: ['Jenkins', 'CentOS 7', 'Java', 'continuous integration', 'CI/CD', 'build server', 'package manager', 'systemd']

tags: ["Jenkins", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-install-jenkins-on-centos-7/']
tab: true
---

<figure>

![How to install jenkins on centos 7](images/How-to-install-jenkins-on-centos-7-1024x576.png)

<figcaption>

How to install Jenkins on CentOS 7

</figcaption>

</figure>

You will learn how to install Jenkins on CentOS 7 or RHEL 7 in this article. Jenkins is a software platform that supports both Continuous Delivery (CD) and Continuous Integration (CI). It is used to automate software testing, development, delivery, and deployment. Ubuntu's Jenkins software generates a powerful management tool that enhances the development process.

## Prerequisites:

- A super user or any normal user with SUDO privileges

- Yum repository configured CentOS server

## 1: Install OpenJDK 8 package on Centos 7

J[ava Runtime Environment](https://www.javatpoint.com/java-jre) is necessary for Jenkins (JRE). For the Java environment, OpenJDK is used in this manual. The Java Runtime Environment is part of the development kit known as OpenJDK. Java may be installed in many versions on Ubuntu. Make sure Java 8 or Java 11 is specified as the default version if you decide to do this. For more information on how to install JDK on Centos server, you can [refer this.](https://utho.com/docs/tutorial/how-to-install-java-on-fedora-based-linux/)

Step 1.1: Verify that Java is already installed on your CentOS server

```
java -version
```
<figure>

![Java binary not found](images/image-675.png)

<figcaption>

Java binary not found

</figcaption>

</figure>

Since Java isn't already installed on our PC, we'll use OpenJDK to do it.

```
info !
```
Step 2 may be skipped if Java is already installed on your Ubuntu system

![Image](images/info.jpeg)

## 2\. Install Jenkins repository

Step 2.1: The next step is to turn on the repository for Jenkins. To do this, use the following curl command to bring in the GPG key:

```
curl --silent --location http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo | sudo tee /etc/yum.repos.d/jenkins.repo
```
And here's how to add the repository to your system:

```
rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
```
Step 2.2: And here's how to add the repository to your system: Once the repository is turned on, type: yum install latest stable Jenkins to install it.

```
yum install jenkins
```
Once the installation is done, you can start the Jenkins service by:

```
systemctl enable --now jenkins
```
## 3\. Adjust the Firewall 

Step 3.1: You need to port 8080 if you are installing Jenkins on a remote CentOS server that is protected by a firewall.  
Use these commands to open the port you need:

```
firewall-cmd --permanent --zone=public --add-port=8080/tcp
firewall-cmd --reload
```
## 4\. Setting Up Jenkins 

Open your browser and type your domain or IP address followed by port 8080 to set up your new Jenkins installation:

```
http://your_ip_or_domain:8080
```
You will see a screen like the one below, which will ask you to enter the Administrator password that was made during the installation:

<figure>

![Homepage of Jenkins after installation](images/image-804.png)

<figcaption>

Homepage of Jenkins after installation

</figcaption>

</figure>

Use the following command to get your terminal to show the password:

```
cat /var/lib/jenkins/secrets/initialAdminPassword
```
You should see an alphanumeric password with 32 characters.

```
9836b548f4e99a203ee98s68232a32
```

Copy the password from your terminal, paste it into the Administrator password field, and then click Continue.

<figure>

![Next step of installation](images/image-805.png)

<figcaption>

Next step of installation

</figcaption>

</figure>

On the next screen, you'll be asked if you want to install the recommended plugins or choose your own. If you click on the box that says Install suggested plugins, the process of installing them will start right away.

<figure>

![Installing required packages](images/image-806.png)

<figcaption>

Installing required packages

</figcaption>

</figure>

After the installation is done, you will be asked to set up the first administrative user. Fill in all the required information and click Save and Continue.

<figure>

![First Admin user created](images/image-808.png)

<figcaption>

First Admin user created

</figcaption>

</figure>

You will be asked to set the URL for the Jenkins instance on the next page. A URL will be automatically made and put in the URL field.

<figure>

![Final step of installing](images/image-809.png)

<figcaption>

Final step of installing

</figcaption>

</figure>

To finish setting up, click the Save and Finish button to confirm the URL.

<figure>

![Installed Jenkins](images/image-810.png)

<figcaption>

Installed Jenkins

</figcaption>

</figure>

Lastly, click on the button that says "Start using Jenkins." This will take you to the Jenkins dashboard, where you can log in as the admin user you made in one of the earlier steps.

If you’ve reached this point, you’ve successfully installed Jenkins on your CentOS7 system.
