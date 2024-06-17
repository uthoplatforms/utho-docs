---
title: "Archiving and Compressing files with GNU Tar and GNU Zip"
date: "2020-04-20"
---

## tar Command

**tar** is a software tool for collecting multiple files into one file in other word we can say "archive". However, **gzip** is a software application used for compression and decompression. We use file compression techniques for saving disk space. This report offers a summary of the use of tar and gzip :

TAR's intricacy is not based on its fundamental form, but on a number of options and settings that can be used to create archives and to interact with them.

For example: We have a tar file named latest-archive.tar . We will use below given command to extract the content of the tar file into present working directory.

```
[root@Microhost ~]# tar -xf latest.tar
```

Use the following command to create archive(wordpress.tar.gz) file of all files in the Wordpress directory:

```
 [root@Microhost ~]# tar -c wordpress > wordpress.tar.gz
```

By default, tar sends archive file contents to the standard output, which you can use to continue processing your created archive. You can opt for the -f option to bypass default output. The command following is the same as the previous command:

```
[root@Microhost ~]# tar -cf wordpress.tar.gz wordpress
```

## gzip Command

A simple and standard method for compressing individual files is provided with gzip and the accompanying Gunzip command. As tar does not allow compression of the files which it archive, the gzip tools can only operate on single files. As tar does not. In the file test.txt the next command is added and comprised in test.txt.gz:

```
[root@Microhost ~]# gzip test.txt
```

This file can be decompressed using one of the following commands:

```
[root@Microhost ~]# gunzip test.txt.gz
[root@Microhost ~]# gzip -d test.txt.gz
```

You can add the -v flag to improve the compression rate of verbosity and output statistics:

```
[root@Microhost ~]# gzip -v test.txt
```

gzip accepts standard input to compress the output of a text stream:

```
[root@Microhost ~]# cat test.txt | gzip > test.txt.gz
```

The compression algorithm used by gzip in compressing files can be configured to use more compression and save time. A number argument between -1 and -9 controls this ratio. The default setup is -6. Also, gzip includes the helpful mnemonics –-fast (i. e. —1) and —best (i.e. -9):

```
[root@Microhost ~]# gzip --best -v test.txt
[root@Microhost ~]# gzip --fast -v test.txt
[root@Microhost ~]# gzip -3 -v test.txt
[root@Microhost ~]# gzip -8 -v test.txt
```

## Extracting Files from a tar Archive

To extract files from a `tar` archive, issue the following command:

```
[root@Microhost ~]# tar -xzvf latest.tar.gz
```

The specified options have the following effects: **\-x** extracts archive contents, **\-z** filters the archive through the compression gzip tool, **\-v** allows the verbose output to print a list of files as they're extracted from the archive and **\-f** specifies that tar will read input from the subsequently specified file latest.tar.gz

## Compressing Log Files

Some files are generated, especially log files that can increase to a large size, by long-term daemons, such as web or e-mail servers. While these files are not deleted, they can grow unmanageably large within a short period of time. Since they are plain texts, compression is effective; however, it makes no sense to use a tool such as tar. It makes sense to use gzip straight away in these cases:

```
[root@Microhost ~]# gzip /var/log/mail.log
```

The file is called mail.log.gz to replace the original /var/log/mail.log. To access this file's contents:

```
[root@Microhost ~]# gunzip /var/log/mail.log.gz
```

However, you do not need to uncompress a file to access its contents in most cases. The tool gzip includes tools for accessing "gzipped" files with standard Unix tools. you can access file contents of gzip with following utilities zcat (Cat equivalent), zgrep (Grep equivalent) and zless (Lower Equivalent).

Thank You :)
