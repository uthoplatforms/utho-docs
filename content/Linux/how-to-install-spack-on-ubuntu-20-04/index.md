---
title: "How to Install Spack on Ubuntu 20.04"
date: "2022-11-12"
---

![How to Install Spack on Ubuntu 20.04](images/How-to-Install-Spack-on-Ubuntu-20.04_utho.jpg)

## Introduction

In this article, you will learn How to Install Spack on Ubuntu 20.04.

[Spack](https://en.wikipedia.org/wiki/Package_manager) is a management tool for packages that was developed to accommodate different versions and configurations of software across a broad number of operating systems, hardware platforms, and other types of environments. It was developed for huge supercomputing facilities, the kind of places where a large number of users and application teams share [common installs](https://utho.com/docs/tutorial/how-to-install-tinycp-on-ubuntu-20-04/) of software on clusters with unusual architectures and make use of libraries that do not have a standard application binary interface (ABI). Because installing a new version of Spack does not disrupt previously established installations, many configurations are able to live on the same computer without causing any problems.

First and foremost, Spack is easy to understand. It provides a straightforward syntax that makes it possible for users to concisely declare version numbers and configuration parameters. Spack is also easy to use for package writers; package files are written in pure Python, and specs enable package authors to maintain a single file for multiple distinct versions of the same product. Spack's other benefit is that it is efficient.

## 1\. Install Dependencies

```
# apt update
```

```
# apt install build-essential -y
```

## 2\. Clone Spack Repository

Make a copy of the Spack repository in /.spack/Spack (or another location of your choosing).

```
# git clone https://github.com/spack/spack ~/.spack/Spack
```

## 3\. Add Shell Support

Spack can be used anywhere by just adding it to the PATH environment variable:

```
# . ~/.spack/Spack/share/spack/setup-env.sh
```

To use the spack command every login, append that command to ~/.bash\_profile:

```
# echo '. ~/.spack/Spack/share/spack/setup-env.sh' >> ~/.bash_profile
```

Then run `source ~/.bash_profile` or log out to make changes take effect.

## 4\. Clear Environment

For Spack to function properly, a pristine setting is necessary, as it compiles and instals packages from their source code. Make sure you have nothing unnecessary in your PATH.

```
# echo $PATH /home/cus/.spack/Spack/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

## 5\. Test Spack

Verify the Spack installation.

```
# spack -V
```

![command output](images/image-469.png)

## Next Steps

Spack allows you to easily install numerous robust scientific programmes, such as pngwriter.

```
# spack install pngwriter
```

```
# spack load pngwriter
```

## Conclusion

Hopefully, you have learned how to Install Spack on Ubuntu 20.04.
