---
title: "How to Extract and Download Tar Files with a Single Command"
date: "2022-10-09"
---

![How to Extract and Download Tar Files with a Single Command](images/How-to-Extract-and-Download-Tar-Files-with-a-Single-Command_utho.jpg)

###### **Description**

In this article, you will learn how to extract and download tar files. Tar ([Tape Archive](https://en.wikipedia.org/wiki/Tar_(computing))) is a common Linux file-archiving format. It can be used for compression alongside gzip (tar.gz) or bzip2 (tar.bz2). It is the most extensively used command-line application for creating compressed archive files (packages, source code, databases, and much more) that can be readily moved from one system to another or across a network.

In this post, we will demonstrate how to download and extract tar archives using the well-known command line downloaders wget and cURL.

## How to Use the Wget Command to Download and Extract Files

The following example demonstrates how to download and unpack the most recent GeoLite2 country databases (used by the GeoIP [Nginx](https://utho.com/docs/tutorial/how-to-install-nginx-and-php-fastcgi-on-fedora/) module) into the current directory.

```
# wget -c http://geolite.maxmind.com/download/geoip/database/GeoLite2-Country.tar.gz -O - | tar -xz 
```

It will be written to standard output and piped to tar, and the tar flag -x enables the extraction of archive files, and the -z flag decompresses compressed archive files created by gzip. The wget option -O specifies a file to which the documents are written, and here we use -, which means they will be written to standard output.

To extract tar files to a specific directory, which in this example is /etc/nginx/, use the -C switch and the following instructions.

**NOTE:** If extracting files to a directory that requires root rights, use tar with the sudo command.

```
# sudo wget -c http://geolite.maxmind.com/download/geoip/database/GeoLite2-Country.tar.gz -O - | sudo tar -xz -C /etc/nginx/ 
```

You also have the option of using the following command, in which case the archive file will be downloaded onto your machine prior to your being able to extract it.

```
# sudo wget -c http://geolite.maxmind.com/download/geoip/database/GeoLite2-Country.tar.gz && tar -xzf GeoLite2-Country.tar.gz 
```

Use this command to extract a compressed archive file to a specified location:

```
# sudo wget -c http://geolite.maxmind.com/download/geoip/database/GeoLite2-Country.tar.gz && sudo tar -xzf GeoLite2-Country.tar.gz -C /etc/nginx/ 
```

## The cURL Command Walkthrough: How to Download and Extract Files

Using cURL, you can do things like download archives and unpack them in your current working directory, as demonstrated above.

```
# sudo curl http://geolite.maxmind.com/download/geoip/database/GeoLite2-Country.tar.gz | tar -xz 
```

Use the following command to extract a file to a different directory while it is being downloaded.

```
# sudo curl http://geolite.maxmind.com/download/geoip/database/GeoLite2-Country.tar.gz | sudo tar -xz -C /etc/nginx/ 
```

And that's it! In this brief but informative tutorial, we demonstrated how to download and extract archive files with just a single command on your computer.

**Thankyou**
