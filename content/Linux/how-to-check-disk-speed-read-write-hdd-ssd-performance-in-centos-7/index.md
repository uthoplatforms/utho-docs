---
title: "How to check Disk Speed (Read/Write) HDD, SSD Performance in CentOS 7"
date: "2022-06-22"
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
