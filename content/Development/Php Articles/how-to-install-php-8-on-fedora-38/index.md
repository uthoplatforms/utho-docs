---
    title: "How to install PHP 8 on Fedora 38"
date: "2023-06-28"
title_meta: "GUIDE TO Insatll PHP 8 on Fedora 38"
description: "A guide on how to install PHP, a popular API client tool, on Fedora, a Linux distribution."

keywords: ['Fedora', 'PHP', 'installation', 'API client', 'testing', 'Linux', 'development']


Tags: ["PHP 8", "fedora"]
icon: "fedora"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/Fedora/how-to-install-php-8-on-fedora-38/']
tab: true
---

![How to Test Internet Speed on Fedora](images/How-to-install-PHP-8-on-Fedora-38-1024x576.jpg)

## Introduction

In this article, you will learn how to install PHP 8 on Fedora 38.

[PHP](https://en.wikipedia.org/wiki/PHP)(a made up word for PHP: Hypertext Preprocessor) is an embedded scripting language in HTML that is widely used due to its flexibility and ease of use in web development.

PHP is an open-source server-side programming language that may be used to construct a wide range of various things, such as websites, applications, and customer relationship management systems. PHP is a server-side programming language.

It is a programming language that may be used for a variety of purposes and is rather popular. Additionally, it can be included into HTML. Because PHP can work with HTML, it has remained one of the most widely used programming languages in the development community. This is due to the fact that PHP contributes to the simplification of the HTML code[.](https://en.wikipedia.org/wiki/PHP)

#### Step 1: Update Fedora system

**Get the most recent updates for the packages that are currently installed.**

```
# dnf update -y

```

#### Step 2: Add REMI Repository

**To add the REMI repository to your Fedora system, run the commands that are given below.**

```
# dnf install https://rpms.remirepo.net/fedora/remi-release-$(rpm -E %fedora).rpm

```

![How to install PHP 8 on Fedora 38](images/image-1194.png)

#### Step 3: Enable the PHP 8 module

```
# dnf module enable php:remi-8.0

```

![How to install PHP 8 on Fedora 38](images/image-1195.png)

#### Step 4: Install PHP 8 on Fedora

**With this command, PHP 8 and the most widely used PHP modules will be installed. You can install more PHP extensions with the dnf package manager if you need them.**

```
# dnf install php

```

![How to install PHP 8 on Fedora 38](images/image-1196.png)

**Verify the currently installed version of PHP by using the following command.**

```
# php -v

```

![install PHP 8 on Fedora](images/image-1197.png)

## Conclusion

Hopefully, now you have learned how to install PHP 8 on Fedora 38.

**Also Read:** [How to Use Iperf to Test Network Performance](https://utho.com/docs/tutorial/how-to-use-iperf-to-test-network-performance/)

Thank You 🙂
