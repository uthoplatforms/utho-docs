---
title: "How to check Disk Speed (Read/Write) HDD, SSD Performance in CentOS 7"
date: "2022-06-22"
title_meta: "Guide for check Disk Speed (Read/Write) HDD, SSD Performance in Centos 7"
description: "Discover how to check disk speed (read/write) and evaluate HDD, SSD performance in CentOS 7. This guide provides commands and tools to measure disk performance, including benchmarks for both HDDs and SSDs on CentOS 7, helping you assess and optimize storage performance for better system efficiency.
"
keywords: ["CentOS 7 disk speed test", "check HDD performance CentOS 7", "SSD benchmark CentOS 7", "disk speed test tool CentOS 7", "CentOS 7 disk speed command", "measure disk performance CentOS 7", "CentOS 7 disk speed benchmark", "test disk read write speed CentOS 7"]

tags: ["Disk Speed", "CentOS"]
icon: "CentOS"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/Linux/Cent OS/how-to-check-disk-speed-read-write-hdd-ssd-performance-in-centos-7/']
tab: true
---

![](images/How-to-check-Disk-Speed-Read_Write-HDD-SSD-Performance-in-CentOS-7.png)

Step 1. Login into the server using root credentials on putty. 

![](images/BB1-2.png)

Step 2 : Run the following command to test the WRITE speed of a disk.

```
# sync; dd if=/dev/zero of=tempfile bs=1M count=1024; sync
```

Step 3 : Run the following command to test the READ speed of a disk.

```
 dd if=tempfile of=/dev/null bs=1M count=1024 
```

![](images/AA13.png)

Thank you!!
