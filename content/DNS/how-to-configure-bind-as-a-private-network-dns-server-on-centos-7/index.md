---
title: "How To Configure BIND as a Private Network DNS Server on CentOS 7"
date: "2020-05-23"
---

A DNS or Domain Name System is a distributed database, allows the association of zone records, for example, IP addresses with domain names. If a computer, like your laptop or phone, has to communicate over the internet, they use each other IP addresses with a remote computer, like a web server. People don't remember IP addresses very well, but they remember the words and phrases in domain names well. Domain names can be used by the DNS system when interfacing with the computer, but computers can still use IP addresses when communicating with each other.

In this guide we examine how a DNS server that is the authoritative DNS server for your domain names can be installed and configured. This allows you to fully control your DNS and immediately modify your DNS records whenever you need to do so.

## Prerequisites

Before starting please follow the below instructions.

- A CentOS 7 server.
- A domain name.
- Root or sudo enabled user on the server.

## Installation of DNS Server

This guide is used for BIND's DNS server. BIND is one of the most used and oldest online DNS servers.

You should make sure your server updates the latest packages before installing BIND:

```
 [root@Microhost]# yum update -y 
```

The Debian repositories default for BIND are available and will be installed as follows:

```
 [root@Microhost]# yum install bind bind-utils 
```

Bind is now installed on the server.

## Global BIND Settings

BIND functioning as a DNS server is divided into two sections. The first is to define the global parameters to make BIND work as we want. The second step is to create the domain-based DNS data to be used by BIND. This is called "area information" or "area records."

The global parameters are configured in this section.

In **/etc/named.conf** we are going to edit the first configuration file and configure how bind works. Open your favorite text editor for this file.

```
 [root@Microhost]# vi /etc/named.conf 
```

Add the below line while editing the domain name according to your requirement at the bottom of the file.

```
zone    "exmaple.com"  {
        type master;
        file    "/var/named/forward.example.com";
 };

zone   "10.100.51.198.in-addr.arpa"  {
       type master;
       file    "/var/named/everse.example.com";
 };
```

The following lines are used in this file:

- zone – this is the domain name or IP address for which BIND responds to requests.
- Type master – BIND reads the local storage zone information and provides the relevant domain information for the zone line.
- File – the zone information file.

Two sections have the same syntax as you can see in this file. The first section contains the domain name example.com known as the forward DNS record. This means that domain information is converted to IP addresses.

The second latter is the server IP address' reverse dns or PTR record. This turns the other way round, i.e. Domain name to IP addresses. The reverse record zone line looks a bit odd, since the IP address is in reverse. The IP address of 198.51.100.10, which forms the reverse record.

Inverse records are important as many safety systems, such as spam filters, are less likely to accept e-mails sent from a non-reverse IP address.

Now that the global setup of BIND is set, the area files can be created that hold the DNS information forward and reverse.

## Configuration of Zone file

```
 [root@Microhost]# vi /var/named/forward.example.com 
```

You can use the below content as the template.

```
$TTL 1d
@               IN      SOA     dns1.example.com.    hostmaster.example.com. (
                1        ; serial
                6h       ; refresh after 6 hours
                1h       ; retry after 1 hour
                1w       ; expire after 1 week
                1d )     ; minimum TTL of 1 day
;
;
;Name Server Information 
@               IN      NS      ns1.example.com.
ns1             IN      A       198.51.100.10
;
;
;Mail Server Information
example.com.    IN      MX      10      mail.example.com.
mail            IN      A       198.51.100.20
;
;
;Additional A Records:   
www             IN      A       198.51.100.30
site            IN      A       198.51.100.30
;
;
;Additional CNAME Records:
slave           IN      CNAME   www.example.com.
```

\[ht\_message mstyle="alert" title="NOTE" " show\_icon="true" id="" class=""style="" \]Whenever you will use the domain name in the zone file always use **. dot** sign at the end of domain name.\[/ht\_message\]

You can modify or add the records according to your requirements using the above template.

This line means:

- @ – The domain of the file named.conf.local, i.e. example.com, will replace this.
- IN – INTERNET type records in this case.
- SOA – The Start Of Authority record is the record. For this domain this is the authoritative record.
- dns1.example.com. – the DNS record nameserver. – The name server.
- hostmaster.example.com. – the name server manager's email address. The @ symbol is substituted by a dot.

A reverse zone file must be created. Open the text editor and create the file:

```
 [root@Microhost]# vi /var/named/reverse.example.com 
```

```
$TTL 1d
@               IN      SOA     dns1.example.com.    hostmaster.example.com. (
                1        ; serial
                6h       ; refresh after 6 hours
                1h       ; retry after 1 hour
                1w       ; expire after 1 week
                1d )     ; minimum TTL of 1 day
;
;
;Name Server Information 
@               IN      NS      ns1.example.com.
ns1             IN      A       198.51.100.10
;
;
;Reverse IP Information
10.100.51.198.in-addr.arpa.      IN      PTR       ns1.example.com.
20.100.51.198.in-addr.arpa.      IN      PTR       mail.example.com.
30.100.51.198.in-addr.arpa.      IN      PTR       www.example.com.
```

You can use the above file content as the template according to your requirement .

We can check the zone file configuration error using the following command.

BIND provides two tools to ensure that its configuration files have no errors preventing BIND from starting. 

The first checks the global settings files and uses the following:

```
 [root@Microhost]# named-checkconf /etc/named.conf 
```

For the second tool use the below command.

```
 [root@Microhost]# named-checkzone (DOMAIN-NAME) (ZONE-FILE) 
```

## Configure Systemd To Keep Dns Server Running

Make a copy of the system service BIND file which we are going to edit.

```
 [root@Microhost]#cp /lib/systemd/system/named.service /etc/systemd/system/ 
```

In future system updates, this guarantees that the edits are not lost. Then, in an editor, open the file:

```
 [root@Microhost]#vi /etc/systemd/system/named.service
```

Add the given lines into the file.

```
Restart=always
RestartSec=3
```

Reload the system daemon file and restart the Dns server.

```
 [root@Microhost]#systemctl daemon-reload
```

```
 [root@Microhost]#systemctl restart named.service
```

We have completed the installation of BIND DNS SERVER.

Thank You :)
