---
title: "How to Set Manual or static IP Address on Ubuntu server"
date: "2022-10-10"
---

<figure>

![How to Set Manual or static IP address on Ubuntu server](images/How-to-Set-Manual-or-static-IP-Address-on-Ubuntu-server-1024x576.png)

<figcaption>

How to Set Manual or static IP Address on Ubuntu server

</figcaption>

</figure>

In this article we will discuss about how to set Manual or static IP Address on Ubuntu server, there will come a moment in your career as a [Linux](https://utho.com/docs/tutorial/category/linux-tutorial/) system administrator when you will be tasked with the responsibility of configuring networking on your machine. On desktop computers, you are able to utilise dynamic IP addresses; but, in order to set up a server architecture, you will need to configure a static IP address (at least in most cases).

The following information on [Internet Protocol version 4](https://en.wikipedia.org/wiki/IPv4) (IPv4) will be used so that we may accomplish the objectives of this tutorial.

```
IP address: 192.168.10.10
Netmask: 255.255.255.0
Hostname: microhost.example.com
Domain name: example.com
Gateway: 192.168.10.1
DNS Server 1: 8.8.8.8
DNS Server 2: 4.4.4.4
```

Before doing anything, first check the network device name of your system, in which you want to set static ip.

```
# ifconfig 
```

<figure>

![Set Manual or static IP Address](images/image-331-1024x274.png)

<figcaption>

output of ifconfig file

</figcaption>

</figure>

To configure static IP address in Debian/ Ubuntu, open the following file:

```
# vi /etc/network/interfaces 
```

You will see the below lines in the above opened file.

```
auto eth0
iface eth0 inet dhcp
```

Change the line to the below code.

```
auto eth0
iface eth0 inet static 
  address 192.168.0.100
  netmask 255.255.255.0
  gateway 192.168.0.1
  dns-nameservers 4.4.4.4
  dns-nameservers 8.8.8.8
```

Save the file and exit the file.

Now make the required entry in /etc/resolv.conf file as follow

\[/console\]# vi /etc/resolv.conf \[/console\]

```
nameserver 8.8.8.8
nameserver 4.4.4.4 
```

Now just down your network device and then up the same to made the changes reflected.

```
# ifdown eth0  
```
# ifup eth0 
```

```

Now confirm whether your changes just reflected yet or not.

```
# ifconfig 
```

<figure>

![Set Manual or static IP Address](images/image-334.png)

<figcaption>

output of ifconfig file

</figcaption>

</figure>
