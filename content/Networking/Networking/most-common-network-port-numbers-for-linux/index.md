---
title: "Most Common Network Port Numbers for Linux"
date: "2022-10-07"
---

A port is a logical address that is typically assigned to a specific service or running application on a computer , and more specifically, TCP/IP and UDP networks. It is a connection endpoint that directs traffic to a specific operating system service. Ports are software-based and are typically associated with the host's IP address.

A port's primary function is to ensure data transfer between a computer and an application. By default, specific services run on specific ports, such as web traffic on port 80 (443 for encrypted traffic), DNS on port 53, and SSH on port 22. Ports are typically linked to the IP addresses of the host systems that run the applications.

Port numbers range from **0-65535** and are divided into three network ranges as shown:

- Ports that range from **1 to 1023** are known as system ports or well-known ports. These are ports that are reserved for running privileged services on a system.
- Ports numbers in the range of 1024 to 49151 are referred to as registered ports and are mostly used by vendors for their applications. They are available for registration at [IANA](https://www.iana.org/) which is an authority that oversees global IP address allocation.
- Ports numbers between 49151 and 65535 are referred to as dynamic ports. They cannot be registered with IANA and are mostly used for customized services.

Commonly Use Network TCP Ports

<figure>

| **Port** | **Description** |
| --- | --- |
| 20 | FTP ( File Transfer Protocol ) port for data transfer between client and server. |
| 21 | FTP ( File Transfer Protocol ) port for establishing a connection between two hosts. It’s referred to as the command or control port. |
| 22 | SSH (Secure Shell) port. This is a secure remote login service where data is encrypted. |
| 23 | Telnet. This is a remote login service that is unencrypted. Data is sent in plain text and is hence considered insecure. It was deprecated in favor of SSH. |
| 25 | SMTP (Simple Mail Transfer Protocol). A protocol used by mail servers to send and receive mail. |
| 53 | DNS (Domain Name Service) Responsible for resolving a domain name to machine-readable IP addresses. |
| 67 (UDP) | Used by the DHCP server (Dynamic Host Configuration Protocol). |
| 68 (UDP) | Used by a DHCP client. |
| 80 | HTTP (Hyper Text Transfer Protocol) is used for unsecured web traffic. |
| 443 | HTTPS (Hyper Text Transfer Protocol Secure) is used for encrypted web traffic. |
| 110 | POP3 (Post Office Protocol). Protocol for unencrypted access to a mail server. |
| 995 | POP3S (Post Office Protocol Secure). Provides encryption for POP3 protocol. |
| 123 (UDP) | NTP (Network Time Protocol). |
| 137 | NetBIOS protocol used for File and Print Sharing. |
| 143 | IMAP (Internet Messaging Application Protocol) Manages electronic mail messages on the mail server. Does not provide encryption. |
| 161/162 | SNMP protocol is used for sending commands and messages. |
| 993 | IMAPS (Internet Messaging Application Protocol Secure) Secure protocol for IMAP and provides SSL/TLS encryption. |
| 445 | SMB (Server Message Block) Port. Used for file sharing. |
| 465 | SMTPS (Simple Mail Transfer Protocol Secure). Provides encryption for the SMTP Protocol. |
| 631 | Internet Printing Protocol. |

<figcaption>

Thankyou

</figcaption>



</figure>
