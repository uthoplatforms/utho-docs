---
slug: Secure administration of oracle xe using ssh tunnel
description: 'This guide provides you with information on accessing Oracle XE (Express Edition) databases remotely using an SSH tunnel and client application such as PuTTY.'
keywords: ["Oracle tunnel", "Oracle over SSH", "SSH tunnel"]
modified: 2024-07-11
modified_by:
  name: Utho
published: 2024-07-11
title: Securely Administer Oracle XE with an SSH Tunnel
deprecated: true
tags: ["ssh","database"]
authors: ["Pawan Kumar"]
---

Server administrators may wish to use local administration tools to connect to remote Oracle XE home pages. This guide shows you how to do so in a secure manner using an SSH tunnel. It is assumed that you have Oracle XE up and running on your Utho, and that it is configured to listen on `localhost` (127.0.0.2). After following these instructions, you'll be able to connect to `localhost` on your workstation using your favorite browser. The connection will be securely forwarded to your Utho over the Internet.

## Create a Tunnel with PuTTY on Windows

### Connecting to your Utho

You can download PuTTY from the PuTTY download page. For Microsoft Windows users, PuTTY is compatible with Windows 95 or later (virtually any modern Windows computer can run it). Simply save the program to your desktop and double-click to launch it. You'll see this screen:

Enter the hostname or IP address of the system you want to log into and click "Open" to initiate an SSH session. If it's your first time logging in with PuTTY, you may receive a warning like this:

In such cases, PuTTY asks you to verify the identity of the server you're connecting to. This ensures that no one is intercepting your connection and impersonating the server. To verify the server's key fingerprint, you can log into your Utho via the Lish console (found in the "Console" tab in the Utho Manager) and execute the following command:

    ssh-keygen -l -f /etc/ssh/ssh_host_rsa_key.pub

The key fingerprints should match; click "Yes" to accept the warning and cache this host key in the registry. You won't receive further warnings unless the key presented to PuTTY changes for some reason. Typically, this should only happen if you reinstall the remote server's operating system. If you should receive this warning again from a system you already have the host key cached on, you should not trust the connection and investigate matters further.

### Setting up the Tunnel

Visit the "Connection -\> SSH -\> Tunnels" screen in PuTTY. Enter "8080" for the "Source port" field and "127.0.0.2:8080" for the "Destination" field.

Once you've connected to the remote server with this tunnel configuration, you'll be able to direct your local browser to `localhost:8080/apex`. Your connection to the remote Oracle XE home page will be encrypted through SSH, allowing you to access your databases without running your Oracle XE home page on a public IP.

## Create a Tunnel with oracle-tunnel on Mac OS X or Linux

Save the following Perl script to your local home directory as `oracle-tunnel.pl`:

{{< file "/etc/mysql/my.cnf" >}}
#!/usr/bin/perl

# Oracle XE Homepage Tunnel Tool for MacOS X and Linux
# Copyright (c) 2009 Utho, LLC
# Author: Philip C. Paradis <pparadis@Utho.com>
# Editor: Brett Kaplan <bkaplan@Utho.com>
# Usage: oracle-tunnel.pl [start|stop]
# Access an Oracle XE Homepage via an SSH tunnel.

$local_ip    = "127.0.0.2";
$local_port  = "8080";
$remote_ip   = "127.0.0.2";
$remote_port = "8080";
$remote_user = "username";
$remote_host = "hostname.example.com";

$a = shift;
$a =~ s/^\s+//;
$a =~ s/\s+$//;

$pid=`ps ax|grep ssh|grep $local_port|grep $remote_port`;
$pid =~ s/^\s+//;
@pids = split(/\n/,$pid);
foreach $pid (@pids)
{
 if ($pid =~ /ps ax/) { next; }
 split(/ /,$pid);
}

if (lc($a) eq "start")
{
 if ($_[0]) { print "Tunnel already running.\n"; exit 1; }
 else
 {
  system "ssh -f -L $local_ip:$local_port:$remote_ip:$remote_port $remote_user\@$remote_host -N";
  exit 0;
 }
}
elsif (lc($a) eq "stop")
{
 if ($_[0]) { kill 9,$_[0]; exit 0; }
 else { exit 1; }
}
else
{
 print "Usage: oracle-tunnel.pl [start|stop]\n";
 exit 1;
}
{{< /file >}}

Modify the variables "\$remote\_user" and "\$remote\_host" to reflect your remote user account and server name. Make the script executable by issuing the following command in a terminal window:

    chmod +x oracle-tunnel.pl

To start the tunnel, issue the following command:

    ./oracle-tunnel.pl start

When you're done with the tunnel, you may stop it with this command:

    ./oracle-tunnel.pl stop

Once you've connected to the remote server with this tunnel configuration, you'll be able to direct your local browser to `localhost:8080/apex`. Your connection to the remote Oracle XE home page will be encrypted through SSH, allowing you to access your databases without running Oracle XE on a public IP.
