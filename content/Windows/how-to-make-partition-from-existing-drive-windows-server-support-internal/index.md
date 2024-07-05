---
title: "How to make partition from existing drive Windows Server"
date: "2022-06-22"
Title_meta: GUIDE TO Create Partition from Existing Drive on Windows Server

Description: This guide provides step-by-step instructions on how to create a new partition from an existing drive on Windows Server. Learn how to use Disk Management tools to shrink an existing volume, allocate free space for a new partition, and format it for storage or organizational purposes.

Keywords: ['create partition', 'Windows Server', 'Disk Management', 'shrink volume', 'allocate space', 'format partition']

Tags: ["Create Partition", "Windows Server", "Disk Management", "Shrink Volume", "Allocate Space", "Format Partition"]
icon: "windows"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Windows/how-to-make-partition-from-existing-drive-windows-server-support-internal']
tab: true
---

![](images/How-to-make-partition-from-existing-drive-windows-server-Support-Internal-1024x576.png)

**How to make a partition from an existing drive on Windows Server**

**Step1: Firstly, we need to login into the server using Remote Desktop Connection (RDP Client) or type the following commands on the RUN prompt mstsc**

![](images/pasted-image-0-7.png)

**Step2: After executing the mstsc command on the RUN prompt, type the server IP address which you need to connect to.**

![](images/pasted-image-0-8.png)

**Step3: Now you need to login to the server with admin privileges to check the current disk size, and thenyou need to create a new disk size from the existing one.**

![](images/pasted-image-0-1-1.png)

**As you see in the above screenshot, there is only 1 logical drive present on the server. Now we are going to create a new drive from the server's existing drive.**

**Note:** _To edit disks on a server, you need to access the server with admin rights._

**Step5: Need to go to disk management by typing the following command: diskmgmt.msc**

![](images/pasted-image-0-2-1.png)

Or  

**Step 5:** **You have to open Server Manager in the server start option, then click on the tools section, then select “Computer Management," and then you need to select "Disk Management" on the screen.**

![](images/pasted-image-0-3-1.png)

![](images/pasted-image-0-4-1.png)

![](images/pasted-image-0-5-1.png)

**Step6: After either of the above 2 methods, you will go on the below screen.**

![](images/pasted-image-0-6-1.png)

**Step7: Now we are ready to add a new partition on the server. Just follow these steps below.**

![](images/pasted-image-0-8-1.png)

**Step8: Now we need to click on Shrink Volume, as we are going to make a partition from the existing drive of the server.**

![](images/pasted-image-0-9.png)

**Step 9: Now we have to enter the amount of space we need to give to the new drive.**

![](images/pasted-image-0-10.png)

**Step 10:** **Now, by clicking on the shrink button, we are going to create a new partition from the drive.**

![](images/pasted-image-0-11.png)

**Step11: Now we need to allocate the new partition from the unallocated zone. Follow the below screenshot.**

![](images/pasted-image-0-12.png)

![](images/pasted-image-0-13.png)

![](images/pasted-image-0-14.png)

![](images/pasted-image-0-15.png)

![](images/pasted-image-0-16.png)

![](images/pasted-image-0-17.png)

**Step12: After doing all the above steps, we are now able to access the newly allocated partition on the server, which is shrinked from the existing partition.**

![](images/pasted-image-0-18.png)

![](images/pasted-image-0-19.png)

Thank you :)
