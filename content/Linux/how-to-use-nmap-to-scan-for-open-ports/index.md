---
title: "How To Use Nmap to Scan for Open Ports"
date: "2022-09-18"
---

![How To Use Nmap to Scan for Open Ports](images/How-To-Use-Nmap-to-Scan-for-Open-Ports-1024x576.png)

Many new system administrators find networking to be a big and confusing topic. To understand them, you have to learn about the different layers, protocols, interfaces, and tools and utilities.

Ports are the ends of logical communications in TCP/IP and UDP networking. A web server, an application server, and a file server can all run from the same IP address. In order for these services to talk to each other, they each listen and talk on a different port. When you connect to a server, you use the server's IP address and a port.

Most of the time, the software you use will tell you what port to use. For example, when you connect to https://microhost.com, you're connecting to the digitalocean.com server on port 443, which is the default port for secure web traffic. Since it is the default, your browser will automatically add the port.

In this tutorial, you'll learn more about ports. You'll use the netstat programme to find open ports, and then you'll use the nmap programme to find out how a machine's network ports are set up. When you're done, you'll be able to find common ports and look for open ports on your systems.

## Checking Open Ports

You can scan for open ports with a number of tools. Most Linux distributions have netstat installed by default.

By running the command with the following parameters, you can find out quickly which services you are running:

```
netstat -tunlp
```
This shows the service's port and listening socket, as well as the UDP and TCP protocols.

The nmap tool is another way to find out what ports are open.

## Using Nmap

Part of securing a network involves doing vulnerability testing. This means trying to infiltrate your network and discover weaknesses in the same way that an attacker might.

Out of all of the available tools for this, `nmap` is perhaps the most common and powerful.

You can install `nmap` on an Ubuntu or Debian machine by entering:

```
apt-get update
apt-get install nmap
```
A better port mapping file is one of the side effects of installing this software. If you look at this file, you can see a much more detailed list of the links between ports and services:

```
less /usr/share/nmap/nmap-services
```
This file has almost 20,000 lines, and it also has fields like the third one, which shows how often that port was found to be open during research scans on the Internet.

## Scanning Ports with nmap

With Nmap, you can find out a lot about a host. It can also make the people in charge of the target system think that someone is trying to do harm. Because of this, you should only test it on servers you own or where the owners have been told.

The people who made nmap set up a test server at scanme.nmap.org.

You can practise nmap on this or one of your own servers.

Here are some of the most common things you can do with nmap. We will run them all with sudo so that some queries don't return only part of the results. Some commands might take a long time to finish:

Look for the operating system of the host:

```
nmap -O target_name
```
For example to scan the google.com

```
nmap -O google.com
```
> Starting Nmap 6.40 ( http://nmap.org ) at 2022-09-18 12:10 EDT  
> Nmap scan report for google.com (142.250.201.46)  
> Host is up (0.35s latency).  
> rDNS record for 142.250.201.46: mrs08s20-in-f14.1e100.net  
> Not shown: 998 filtered ports  
> PORT STATE SERVICE  
> 80/tcp open http  
> 443/tcp open https  
> Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port  
> Device type: general purpose  
> Running (JUST GUESSING): FreeBSD 9.X (86%)  
> OS CPE: cpe:/o:freebsd:freebsd:9  
> Aggressive OS guesses: FreeBSD 9.1-PRERELEASE (86%)  
> No exact OS matches for host (test conditions non-ideal).
> 
> OS detection performed. Please report any incorrect results at http://nmap.org/submit/ .
> 
> Nmap done: 1 IP address (1 host up) scanned in 35.30 seconds

  
Nmap done: 1 IP address (1 host up) scanned in 35.30 seconds

You can also scan for your own machine, localhost.

```
nmap -O localhost
```
> Starting Nmap 6.40 ( http://nmap.org ) at 2022-09-18 12:13 EDT  
> Nmap scan report for localhost (127.0.0.1)  
> Host is up (0.000013s latency).  
> Other addresses for localhost (not scanned): 127.0.0.1  
> Not shown: 997 closed ports  
> PORT STATE SERVICE  
> 22/tcp open ssh  
> 25/tcp open smtp  
> 80/tcp open http  
> Device type: general purpose  
> Running: Linux 3.X  
> OS CPE: cpe:/o:linux:linux\_kernel:3  
> OS details: Linux 3.7 - 3.9  
> Network Distance: 0 hops
> 
> OS detection performed. Please report any incorrect results at http://nmap.org/submit/ .
> 
> Nmap done: 1 IP address (1 host up) scanned in 3.93 seconds

Scan without preforming a reverse DNS lookup on the IP address specified. This should speed up your results in most cases:

```
sudo nmap -n google.com
```
Output would be like this.

> Starting Nmap 6.40 ( http://nmap.org ) at 2022-09-18 09:39 EDT  
> Nmap scan report for google.com (142.250.201.46)  
> Host is up (0.35s latency).  
> Not shown: 998 filtered ports  
> PORT STATE SERVICE  
> 80/tcp open http  
> 443/tcp open https
> 
> Nmap done: 1 IP address (1 host up) scanned in 23.86 seconds

Scan a specific port instead of all common ports

```
nmap -p 80 google.com
```
> Starting Nmap 6.40 ( http://nmap.org ) at 2022-09-18 09:42 EDT  
> Nmap scan report for google.com (142.250.201.46)  
> Host is up (0.35s latency).  
> rDNS record for 142.250.201.46: mrs08s20-in-f14.1e100.net  
> PORT STATE SERVICE  
> 80/tcp open http
> 
> Nmap done: 1 IP address (1 host up) scanned in 1.17 seconds

To scan only TCP connection.

```
nmap -sT google.com
```
> Starting Nmap 6.40 ( http://nmap.org ) at 2022-09-18 11:32 EDT  
> Nmap scan report for google.com (172.217.19.142)  
> Host is up (0.35s latency).  
> rDNS record for 172.217.19.142: par03s12-in-f142.1e100.net  
> Not shown: 998 filtered ports  
> PORT STATE SERVICE  
> 80/tcp open http  
> 443/tcp open https
> 
> Nmap done: 1 IP address (1 host up) scanned in 19.61 seconds

To scan only UDP connection.

```
nmap -sU google.com
```
> Starting Nmap 6.40 ( http://nmap.org ) at 2022-09-18 11:48 EDT  
> Nmap scan report for google.com (142.250.201.14)  
> Host is up (0.35s latency).  
> rDNS record for 142.250.201.14: mrs08s19-in-f14.1e100.net  
> All 1000 scanned ports on google.com (142.250.201.14) are open|filtered
> 
> Nmap done: 1 IP address (1 host up) scanned in 20.84 seconds

Scan for every TCP and UDP open port:

```
nmap -sU -sT  -n -PN -p- 103.146.242.22
```
> Starting Nmap 6.40 ( http://nmap.org ) at 2022-09-18 11:58 EDT  
> Nmap scan report for 103.146.242.22  
> Host is up (0.00030s latency).  
> Not shown: 131065 closed ports  
> PORT STATE SERVICE  
> 22/tcp open ssh  
> 80/tcp open http  
> 37700/tcp open unknown  
> 50390/tcp open unknown  
> 52924/tcp open unknown
> 
> Nmap done: 1 IP address (1 host up) scanned in 7.19 seconds
