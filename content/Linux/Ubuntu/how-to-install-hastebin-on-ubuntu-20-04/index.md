---
title: "How to Install Hastebin on Ubuntu 20.04"
date: "2022-11-06"
title_meta: "Guide to install Hastebin on Ubuntu 20.04"
description: "Learn how to install Hastebin on Ubuntu 20.04 with this comprehensive guide. Follow these step-by-step instructions to set up Hastebin, a self-hosted pastebin tool, on your Ubuntu system for sharing code snippets and text.
"
keywords: ["install Hastebin Ubuntu 20.04", "Hastebin setup Ubuntu", "Ubuntu 20.04 Hastebin installation guide", "Hastebin server Ubuntu", "Ubuntu Hastebin tutorial", "Hastebin installation steps Ubuntu", "self-hosted Hastebin Ubuntu", "Hastebin pastebin tool Ubuntu"]

tags: ["Hastebin", "ubuntu"]
icon: "Ubuntu"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Ubuntu/how-to-install-hastebin-on-ubuntu-20-04/']
tab: true
---

<figure>

![How to Install Hastebin on Ubuntu 20.04](images/How-to-Install-Hastebin-on-Ubuntu-20.04_utho.jpg)

<figcaption>

How to Install Hastebin on Ubuntu 20.04

</figcaption>

</figure>

## Introduction

In this article, you will learn how to install Hastebin on [Ubuntu 20.04](https://utho.com/docs/tutorial/how-to-install-git-on-ubuntu-20-04/).

Hastebin is the most visually beautiful and user-friendly pastebin that has ever been created.The act of sharing one's source code is beneficial, and doing so ought to be a very simple process. We find ourselves in a variety of scenarios where we need to exchange some code with another person, and in those cases, we use pastebins.

Since Node.js forms the foundation of the Hastebin server software, setting up a self-hosted instance of Hastebin on your own server is a simple process. You can add and manage snippets through the web interface; however, the unique Hastebin command-line application makes it feasible to submit snippets to the server from the terminal. This is the case even though the web interface is available.

## Install Hastebin Server

1\. Use a root SSH login to access your server.

2\. Get started using haste-server by cloning its GitHub project.

```
# git clone https://github.com/seejohnrun/haste-server.git
```

3\. Change to the `haste-server` directory.

```
# cd haste-server
```

4\. Install the required packages with npm.

```
# apt install npm
```

```
# npm install
```

```
# npm update
```

5\. Port 7777 is the default for Hastebin. In order to access the internet, you must switch to port 80 for HTTP.

Edit `config.js`.

```
# vi config.js
```

Change this line from 7777 to 80:

```
"port": "7777",
```

This is how it should appear once you're done:

```
"port": "80",
```

You can use the escape:wq command to save your work and close the file.

## Install PM2

Node.JS applications can make use of [PM2](https://en.wikipedia.org/wiki/PM2), which is a process manager. If your programme goes down, PM2 will detect it and restart it automatically.

1\. Install PM2.

```
# npm install pm2 -g
```

2\. Start your Hastebin server.

```
# pm2 start server.js
```

3\. You can save your PM2 settings and have it launch automatically.

```
# pm2 save
```

```
# pm2 startup
```

## Test the Hastebin Server

To access an empty page where you can insert code, simply point your browser to the IP address of your server.

![install Hastebin on Ubuntu](images/image-468-1024x334.png)

## Conclusion

Hopefully, you have learned how to install Hastebin on Ubuntu 20.04.

Thank You 🙂
