---
title: "How to Verify Your Application is Listening on the Correct Port"
date: "2022-09-29"
---

![](images/How-to-Verify-Your-Application-is-Listening-on-the-Correct-Port_utho.jpg)

## Introduction

In this short article you will know to Verify Your Application is Listening on the Correct Port or not.

If you are troubleshooting a service and you are certain that it is operating normally, the next step in the process is to verify and make sure that there are no other issues. When you are debugging a service and you are certain that it is operating normally, the next step in the process is to check and make sure that the service is listening on the appropriate network port. Whenever you are debugging a service and you are certain that it is operating normally, the next step in the process is to check and make sure that the service is Only if you are satisfied that the service is operating normally is it required for you to go to the next stage. make sure that the service is configured to listen on the appropriate network port.

On a Linux server, the netstat command displays the services and ports that are actively listening, along with the specifics of any connections that are presently being made to those services and ports. It also displays any connections that have been made in the past to those services and ports. It is essential to pay attention to the addresses on which a network daemon is listening (including the port number), the daemon's process identifier (PID), and the programme name when performing fundamental troubleshooting on a network daemon. These are the details regarding the link.

**\-u** : Show UDP sockets.

**\-p** : Show the PID and name of the program to which each socket belongs.

**\-l** : Show only listening sockets.

**\-n** : Show numeric addresses.

**\-t** : Show TCP sockets.

Example:

```
# netstat -uplnt
```

![Application is Listening on the Correct Port](images/image-212.png)

Examine this data to ensure that your application is listening on the correct local address and socket. If you submit a ticket for further debugging, please include this [information](https://utho.com/docs).

Thank You 🙂
