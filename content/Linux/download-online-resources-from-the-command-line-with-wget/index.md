---
title: "Download Online Resources from the Command Line with wget"
date: "2020-06-06"
title_meta: "Download Online Resources from the Command Line with wget"
description: "Wget is an internet command line utility which recovers files to the local filesystem and stores them. You can download any file accessible by HTTP, HTTPS or FTP using wget. wget gives a number of user configuration options for downloading and saving of files. It also features a recursive download function to download a set of linked resources."

keywords:  ['cmd', 'wget']
tags: ["cmd"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/download-online-resources-from-the-command-line-with-wget']
tab: true
---

### What is wget ?

Wget is an internet command line utility which recovers files to the local filesystem and stores them. You can download any file accessible by HTTP, HTTPS or FTP using wget. wget gives a number of user configuration options for downloading and saving of files. It also features a recursive download function to download a set of linked resources.

### Using wget

The command wget uses the following fundamental syntax:

```
 [root@Microhost ~]# wget [ARGUMNET] [URL] 
```

If you use wget without any argument then it will download the file specified by the \[URL\] to the current directory

```
 [root@Microhost ~]# wget https://utho.com/docs/ai.zip 
```

This above command will download a zip file from Microhost.com website.

### Download Content to Standard Output

The -O option controls the file location and name in which the downloaded content is written. To download and save the file to the Text directory as micro.txt :

wget -O Text/micro.txt https://utho.com/docs/assets/ai.txt

We have completed the Wget command section:

Thank You :)
