---
title: "How to install PHP 8.2 on Ubuntu 22.04"
date: "2023-04-22"
---

![How to install PHP 8.2 on Ubuntu 22.04](images/How-to-install-PHP-8.2-on-Ubuntu-22.04_utho.jpg)

## Introduction

In this article, you will learn how to install PHP 8.2 on Ubuntu 22.04.

[PHP](https://en.wikipedia.org/wiki/PHP), or Hypertext Preprocessor, is a widely used server programming language that is best recognised for its ability to create dynamic and interactive web pages. The first step in becoming proficient in programming is becoming familiar with the basics of the language of your choice.

The PHP scripting language is a general-purpose scripting language that can be used for web development.

##### Step 1: Update Packages

**When we initially start using a new system, the very first thing we need to do is update our repositories so that they are always up to date. Additionally, execute the upgrade command.**

```
apt update

```

![update](images/image-998.png)

##### Step 2: Add the Ondrej sury repository to the PPA

**In order for PHP 8.2 to function properly on Ubuntu 22.04, we need to add the Ondrej sury PPA into the system. At the present time, this individual is responsible for maintaining the PHP repository. Because this PPA is not presently being reviewed, installing from it does not guarantee that all requirements will be met.**

**In our terminal, use the following command to add this PPA.**

```
add-apt-repository ppa:ondrej/php

```

![add repo](images/image-999.png)

**After the installation is finished, we will need to do another update of the repository in order for the changes to take effect.**

```
apt update

```

##### Step 3: Install PHP 8.2

**On our Ubuntu 22.04 Linux system, we should now be able to successfully install PHP 8.2. The commands that need to be executed are as follows:**

```
apt install php8.2 -y

```

![How to install PHP 8.2 on Ubuntu 22.04](images/image-1000.png)

**With the following command, you can find out which version of PHP is currently in use:**

```
php --version

```

![php version](images/image-1001.png)

##### Step 4: Add Extensions for PHP 8.2

**In addition, you are able to install multiple packages at the same time. The following is a list of some of the most typical modules that you will probably want to install on your system:**

```
apt-get install -y php8.2-cli php8.2-common php8.2-fpm php8.2-mysql php8.2-zip php8.2-gd php8.2-mbstring php8.2-curl php8.2-xml php8.2-bcmath

```

**The Apache configurations for PHP are kept in /etc/php/8.2/apache2/php.ini. With this command, you can see a list of all PHP plugins that are loaded:**

```
php -m

```

![How to install PHP 8.2 on Ubuntu 22.04](images/image-1002.png)

## Conclusion

Hopefully, now you have learned how to install PHP 8.2 on Ubuntu 22.04.

**Also Read:** [How to Install NGINX Web Server on Ubuntu 22.04 LTS](https://utho.com/docs/tutorial/how-to-install-nginx-web-server-on-ubuntu-22-04-lts/)

Thank You 🙂
