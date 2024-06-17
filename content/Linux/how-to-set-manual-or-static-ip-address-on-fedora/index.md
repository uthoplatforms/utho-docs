---
title: "How to Set Manual or static IP Address on Fedora"
date: "2022-11-08"
---

![How to Set Manual or static IP Address on Fedora](images/How-to-Set-Manual-or-static-IP-Address-on-Fedora-1024x576.png)

In this article we will discuss How to Set Manual or static IP Address on Fedora,

There will come a moment in your career as a [Linux](https://utho.com/docs/tutorial/category/linux-tutorial/) system administrator when you will be tasked with the responsibility of configuring networking on your machine. On desktop computers, you are able to utilise dynamic IP addresses; but, in order to set up a server architecture, you will need to configure a static IP address (at least in most cases).

The following information on [Internet Protocol version 4](https://en.wikipedia.org/wiki/IPv4) (IPv4) will be used so that we may accomplish the objectives of this tutorial.

```
IP address: 192.168.10.10
Netmask: 255.255.255.0
Hostname: microhost.example.com
Domain name: example.com
Gateway: 192.168.10.1
DNS Server 1: 8.8.8.8
DNS Server 2: 4.4.4.4

```

To configure static IP address, open the /etc/sysconfig/network-scripts/ifcfg-eth0

> Note: The filename ifcfg-eth may varies from CentOS to Redhat linux. In CentOS it is available named as ifcfg-eth0, whereas it will be available only as eth0.
> 
> Also, the file name will be depend on your device name available on your Linux machines such as ens0 or so on.

```
vim /etc/sysconfig/network-scripts/ifcfg-eth0
```
> DEVICE="eth0"  
> HWADDR="00:08:a2:0a:ba:b8"  
> BOOTPROTO=dhcp  
> ONBOOT=yes  
> UUID="41171a6f-bce1-44de-8a6e-cf5e782f8bd6"  
> IPV6INIT=yes  
> TYPE=Ethernet  
> NAME="eth0"

Now make changes like shown below, please do not forget to make changes according to your need

> DEVICE=eth0  
> HWADDR=00:16:3e:65:de:88  
> BOOTPROTO=static  
> ONBOOT=yes  
> NM\_CONTROLLED=no  
> IPADDR=192.168.1.10  
> NETMASK=255.255.255.0  
> GATEWAY=192.168.1.1  
> DNS1=8.8.8.8  
> DNS2=4.4.44

Please note that, You only need to change the following points.

1. IPADDR
2. GATEWAY
3. DNS1
4. DNS2

The rest of the entries should be left unchanged.

Next edit `resolve.conf` to change or update the DNS nameserver

```
vi /etc/resolv.conf
```
> nameserver 8.8.8.8  
> nameserver 8.8.4.4

Once you have made your changes restart the networking manager service with:

```
systemctl restart NetworkManager
```
or you can just down and up the network interface using the below command

```
ifdown eth0 # ifup eth0
```
Now to check the configured ip on your machine, run the below command

```
ifconfig
```
<figure>

![output of the command](images/image-330.png)

<figcaption>

output of the command

</figcaption>

</figure>

I hope you are now fully able to set a manual or static IP address on Fedora
