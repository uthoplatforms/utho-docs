---
title: "How to Real-Time Monitor TCP and UDP Ports"
date: "2022-10-09"
---

![](images/How-to-Real-Time-Monitor-TCP-and-UDP-Ports_utho.jpg)

##### **Description**

This article will show you How to Watch TCP and UDP Ports in Real-time, Each network service that is running on a [Linux](https://utho.com/docs/tutorial/category/linux-tutorial/) system uses a specific protocol (the most common ones being the [TCP](https://en.wikipedia.org/wiki/Transmission_Control_Protocol) (Transmission Control Protocol) and UDP (User Datagram Protocol)) and a port number in order to communicate with other processes or services. A port is a logical construct that identifies a particular process/application or a type of network service. In terms of software, especially at the level of the operating system, a port is a logical construct that identifies a

On a Linux machine, we will demonstrate how to use a socket summary to list and monitor or observe operating TCP and UDP ports in real-time on a Linux machine.

## **List All Open Ports in Linux**

To generate a list of all open ports on a Linux machine, you may use the netstat command or the ss programme in the following manner.

It is also very important to point out that the netstat command has been deprecated, and that the ss command has been implemented to provide more extensive network data in its place.

```
# sudo netstat -tulpn 
```

Or

```
# sudo ss -tulpn 
```

![Watch TCP and UDP Ports in Real-time](images/image-319.png)

The State column in the output of the preceding command indicates whether a port is in a listening state (LISTEN) or not

\*In the command that was just shown, the flag:

- \-t – enables listing of TCP ports.

- \-l – prints only listening sockets.
- \-n – shows the port number.
- \-p – show process/program name.  
    

## **Real-time monitoring of TCP and UDP open ports**

To monitor TCP and UDP ports in real time, use the netstat or ss tools in conjunction with the watch application, as illustrated.

```
 # sudo watch netstat -tulpn 
```

Or

```
 # sudo watch ss -tulpn 
```

![How to Watch TCP and UDP Ports in Real-time](images/image-320-1024x173.png)

To exit, use Ctrl+C.

Hopefully now you have a better understanding of Real-Time Monitor TCP and UDP Ports.

##### **Thank You**
