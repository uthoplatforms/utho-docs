---
title: "What is IOSTAT command and how to use it"
date: "2022-08-28"
---

<figure>

![What is IOSTAT command and how to use it](images/iostat-1.png)

<figcaption>

What is IOSTAT command and how to use it

</figcaption>

</figure>

```
Description:
```
In this tutorial, we will learn what the IOSTAT command and how to use it. With the iostat command, you can see how busy your system's input/output devices are by comparing the amount of time they are active to their average transfer rates.  
The iostat command makes reports that can be used to change how the system is set up so that the input/output load is more evenly spread across the physical discs.

## **How to install iostat command in Linux**

```
Prerequisites: To install the iostat command you must be either super user or a normal user with SUDO privileges. 
```

The iostat command does not come pre-installed with Linux distributions, but it is part of the default package. This means that the package manager of the Linux distribution can be used to install it.

In Fedora/ Redhat/ Centos:

```
 # yum install sysstat -y 
```

In Ubuntu/ Debian:

```
 # apt install sysstat -y 
```

## **How to use IOSTAT command:**

To generate the default or basic report using iostat, you can simply type iostat in your terminal and hit enter

```
# iostat 
```

<figure>

![output of iostat comand](images/Screenshot-from-2022-08-25-08-50-47.png)

<figcaption>

output of iostat comand

</figcaption>

</figure>

The iostat command makes reports that are divided into two sections: the CPU Utilization report and the Device Utilization report.

**CPU Utilization report:** The CPU Utilization Report shows how well the CPU is working based on different parameters. Here's what these parameters mean:

- %user: CPU utilization in percentage that was used while running user processes.

- %nice: CPU utilization in percentage that was used while running user processes with nice priorities.

- %system: CPU Utilization in percentage that was used while running kernel.

- %iowait: Show the percentage of time that the CPU or CPUs were idle during which the system had an outstanding disk I/O request.

- %steal: Show the percentage of time spent in involuntary wait by the virtual CPU or CPUs while the hypervisor was servicing another virtual processor.

- %idle: Show the percentage of time that the CPU or CPUs were idle and the system did not have an outstanding disk I/O request.

**Disk Utilization Report:**

The Device Utilization Report is the second report that is made by the iostat command. The device report gives information about statistics for each physical device or partition. On the command line, you can enter statistics to be shown for block devices and partitions.  
If you don't enter a device or partition, statistics are shown for every device the system uses, as long as the kernel keeps statistics for it.

In this report too, it show the report on different parameters. Here's what these parameters mean:

- Device: This column gives the [device (or partition)](https://utho.com/docs/tutorial/how-to-increase-and-decrease-the-lvm-size/) name as listed in the /dev directory.

- tps: The number of transfers per second that were sent to the device. If tps is high, it means the processor is working hard.

- KB\_read/s: Indicate the amount of data read from the device

- kB\_wrtn/s: Indicate the amount of data written to the device

- kB\_dscd/s: It displays the rate of data discarded by the CPU per second

- kB\_read: It displays the amount the data read

- kB\_wrtn: It displays the amount the data written

- KB\_dscd: It display the amount the data discarded

You can find the other useful paramethers you can find in the output related to iostat command.

- rps: Indicates the number of read transfers per second.

- Krps: here, K represent is kilo which means 1000. So it defines as above but of value of 1000.

To display the report only for [CPU Utilization](https://en.wikipedia.org/wiki/CPU_time#:~:text=The%20CPU%20usage%20is%20used,has%20entered%20an%20infinite%20loop.):

```
# iostat -c 
```  

To display the report only for Disk Utilization:

```
# iostat -d 
```  

To display a continuous device report at two-second intervals.

```
# iostat -d 2
```  

If you want to generate n reports in a given time, use the below format to execute the command

```
iostat interval-time numbers_of_time
```  

For example, to generate 6 report for every 2 seconds, but for only devices

```
# iostat -d 2 6 
```  

To generate a report for a specific device.

```
# iostat sda # or any other device
```  

To generate a report for a specific device with all its partition.

```
# iostat -p sda # or any other device
```

<figure>

![](images/Screenshot-from-2022-08-26-09-20-35.png)

<figcaption>

output some advance

</figcaption>

</figure>

In conclusion, you have learned that what is IOSTAT command and how to use it.
