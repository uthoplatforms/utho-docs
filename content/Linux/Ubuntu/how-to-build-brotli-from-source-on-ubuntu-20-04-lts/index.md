---
title: "How to Build Brotli From Source on Ubuntu 20.04 LTS"
date: "2022-11-12"
keywords: ["build Brotli from source", "Ubuntu 20.04 LTS", "Brotli compression", "compile Brotli", "source installation", "Ubuntu tutorial", "Brotli setup"]
title_meta: "Learn how to build Brotli from source on Ubuntu 20.04 LTS with this step-by-step guide. Follow these instructions to compile and install Brotli compression library."

---

![How to Build Brotli From Source on Ubuntu 20.04 LTS](images/How-to-Build-Brotli-From-Source-on-Ubuntu-20.04-LTS_utho.jpg)

## Introduction

In this article, you will learn how to build Brotli from source on Ubuntu 20.04 LTS.

[Brotli](https://en.wikipedia.org/wiki/Brotli), a compression algorithm, claims to be more effective at compressing web pages than its predecessor, GZIP, and to have shorter compression times. It is available at no cost, has widespread support across current web servers, and can be used by anyone.Brotli is a compression format that is widely supported by today's web servers.

A pre-defined dictionary of frequently used words is one of the characteristics that helps Brotli improve its compression efficiency. Because the server and the client both have access to this dictionary, we can save some space.

Brotli and GZIP, two compression formats, both help reduce page size by compressing files. Faster page loads are the result of less files.

## Before getting started

```
# lsb_release -ds
```

![command output](images/image-471.png)

## Create a new time zone

```
# sudo dpkg-reconfigure tzdata
```

Please update your system to the most recent version.

```
# sudo apt update
```

## Create Brotli

Get the necessary software and build tools installed.

```
# sudo apt install -y build-essential gcc make bc sed autoconf automake libtool git apt-transport-https tree
```

Get a copy of the Brotli repository.

```
# git clone https://github.com/google/brotli.git
```

Access the Brotli code repository.

```
# cd brotli
```

You should make a reference page for using Brotli commands.

\[consoel\]# sudo cp ~/brotli/docs/brotli.1 /usr/share/man/man1 && sudo gzip /usr/share/man/man1/brotli.1\[/console\]

Look in the user guide.

```
# man brotli
```

Execute the ./bootstrap command to create the Autotools configure file.

```
# ./bootstrap
```

Once you've run the preceding command, you'll have access to the standard C programme [construction](https://utho.com/docs/tutorial/how-to-test-internet-connection-speed-in-ubuntu-20-04/) stages, including configure, make, and make install.

Use the ./configure --help command for further information.

Now, make Brotli.

```
# ./configure --prefix=/usr --bindir=/usr/bin --sbindir=/usr/sbin --libexecdir=/usr/lib64/brotli --libdir=/usr/lib64/brotli --datarootdir=/usr/share --mandir=/usr/share/man/man1 --docdir=/usr/share/doc
```

```
# make
```

```
# sudo make install
```

A successful build allows you to verify the version.

```
# brotli --version
```

![command output](images/image-472.png)

Hopefully, you have learned how to build Brotli from source on Ubuntu 20.04 LTS.

Thank You 🙂
