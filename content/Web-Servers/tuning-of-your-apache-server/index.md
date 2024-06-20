---
title: "Tuning Of Your Apache Server"
date: "2020-06-01"
home: true
---

Your Apache configuration settings have a significant impact on the output of your Microhost Cloud server. There are several tools that can be used to further examine the performance of your Apache server and make educated choices on how to start tuning Apache configurations. The guide include an overview of some of the process monitoring and device resource usage that can be used to inspect how Apache affects the output of your Microhost Cloud Server. You will also learn about important Apache modules, such as Multi-Processing modules, which will allow you to make use of Apache's power and flexibility.

![](images/Tuning-Of-Your-Apache-Server_utho.jpg)

## Tools

\[ht\_message mstyle="alert" title="NOTE" " show\_icon="true" id="" class=""style="" \]The steps in this guide claim the rights of origin. Be sure to run the steps below either as root or with the sudo prefix. Please see our Users and Groups Guide for more detail on rights.\[/ht\_message\]

There are a number of tools that can help decide whether resource settings need to be modified, including the top command and the Siege load-testing system. Microhost cloud server's own Longview software will also aid in the control of servers. A good place to start is getting acquainted with your server's RAM and CPU use.

Discover consumption information with the `ps` command variations below. The `ps` command is used to produce a report about the processes running on your Microhost cloud server:

```
echo [PID] [MEM] [PATH] && ps aux | awk '{print $2, $4, $11}' | sort -k2rn | head -n 20 ps -eo pcpu,pid,user,args | sort -k 1 -r | head -20
```

## Apache mod\_status

The Apache Status module, `mod_status`, offers comprehensive status page output information about your server.

1\. Open the Configuration file for your website. This file is located on Debian / Ubuntu systems at `/etc/apache2/sites-available/hostname.abc.com.conf`, or `/etc/httpd/conf.d/vhost.conf` on CentOS/Fedora systems.

2\. Add the below to the `<virtual_hosts>` block:

```file {title="/etc/apache2/sites-available/hostname.abc.com.conf (Debian/Ubuntu)" lang="aconf"}
SetHandler server-status Order Deny,Allow Deny from all Allow from localhost
```

3\. Apache `mod_status` also offers an **ExtendedStatus** option, which provides additional details about every request that Apache has made. Edit your Apache configuration file to allow **ExtendedStatus**, and add the following line:

```file {title="/etc/apache2/apache2.conf (Debian/Ubuntu)" lang="aconf"}
ExtendedStatus On
```

\[ht\_message mstyle="alert" title="NOTE" " show\_icon="true" id="" class=""style="" \]Enabling ExtendedStatus takes extra machine resources\[/ht\_message\]

4\. Restart Apache:

- Debian/Ubuntu: ```
systemctl restart apache2
```
- CentOS/Fedora: ```
systemctl restart httpd
```

5\. To view the created file, you can download Lynx, a web browse in text mode:

- Debian/Ubuntu: ```
apt-get install lynx
```
- Fedora/CentOS: ```
yum install lynx
```

6\. Open the file:

```
lynx http://localhost/server-status
```

## Apache2Buddy

Similar to MySQLTuner, the Apache2Buddy script tests your Apache setup, and makes recommendations based on your Apache process memory and overall RAM. While it is a fairly simple program which focuses on the `MaxClients` directive, it is useful to use Apache2Buddy. The script can be executed with the following command:

```
curl -sL https://raw.githubusercontent.com/richardforth/apache2buddy/master/apache2buddy.pl | sudo perl
```

## Multi Processing Modules

Apache version 2.4 provides three Multi Processing Modules (MPMs) for the settings management. Every module produces processes for children, but varies in the way they interact with threads.

## Prefork

Upon launch the prefork module produces a number of child processes, each child manages just one thread. Since these systems deal with just one thread at a time, request speed may be affected if there are too many requests at the same time. If this occurs, certain requests will simply wait in line for action to be taken. You may increase the number of child processes that are spawned in order to manage this, but this increases the amount of RAM used. Prefork is the easiest module to use for non-thread-safe libraries.

## Worker

The child processes of the worker module spawn several threads per process with each thread ready to assume new requests. This enables the entry of a larger number of simultaneous requests, and in effect it is easier to use RAM on the server. Overall, the worker module offers higher performance, but is less secure than the prefork module and cannot be used with modules that are not thread safe.

## Event

The event module is available on Apache 2.4 only, and is based on the MPM worker. Like the worker, it creates multiple threads per child process, with a thread dedicated to KeepAlive connections which will be passed on to child threads once the request is made. This is good for multiple simultaneous connections, especially those that are not all simultaneously active but make the occasional request. In the case of SSL connections, the MPM event works the same as the worker.

## Module Values

After you have selected your MPM, the values within the configuration need to adjust. Such settings are found on Debian / Ubuntu in `/etc/apache2/apache2.conf`, and on CentOS / Fedora in `/etc/httpd/conf/httpd.conf`. Here is an example of MPM Prefork Module configuration:

```file {title="/etc/apache2/apache2.conf (Debian/Ubuntu)" lang="aconf"}
StartServers MinSpareServers MaxSpareServers MaxRequestWorkers MaxConnectionsPerChild 4500
```

Replace `<IfModule mpm_prefork_module>` with `<IfModule mpm_worker_module>` or `<IfModule mpm_event_module>` for use with the worker or event modules, respectively.

Next, the module settings you've added in the previous phase should be updated. In do so, you should bear in mind what every interest does, and how best in change it. It is recommended that you make small adjustments to the software settings and track the results afterwards.

The following sections provide an overview of any MPM module environment

## StartServers

The `StartServers` value shows the number of child processes generated at startup, and is managed dynamically depending on the load. Often there is little need to adjust this number, unless your server is regularly restarted and includes a large number of requests upon reboot.

## MinSpareServers

Sets minimum number of idle processes for child process. If there are less processes than the `MinSpareServer` number, then Apache 2.2 or lower produces more processes at a rate of one per second. With Apache 2.4, this rate increases exponentially starting with 1 and ending with 32 spawned children per second. The advantage of this value is that it can take an empty thread when a request comes in; should a thread not be available, Apache will need to spawn a new kid, take up resources and prolong the time it takes for the request to go through. Remember that too many idle processes on the server will have an adverse impact too. It would only be appropriate to tune this value on very busy sites. Changing the value to a large number isn't recommended.

## MaxSpareServers

This parameter sets maximum number of idle processes for children. If more idle processes exist than this number, then they will be terminated. This number shouldn't be set too high unless your website is incredibly busy, because even idle processes consume resources.

## MaxRequestWorkers

This parameter, formerly known as MaxClients (Apache 2.3.13 or lower), shows the maximum quantity of requests that can be served concurrently, with any amount going beyond the queued limit. The queue size is dependent on directive ListenBacklog. If MaxRequestWorkers is set too low, connections will ultimately be sent time-out to the queue; but, if set too high, this will cause the memory to swap. If that value is increased past 256, the value of ServerLimit must be increased as well.

One way to determine the best value for this is to divide the amount of RAM each Apache process uses by the available amount of RAM, leaving some space for other processes. Using Apache2Buddy, or the commands below, to help evaluate certain values.

The following command is given for determining the RAM each Apache method uses. The value of the Resident Set Size (RSS) shows the RAM currently being used in kilobytes by a device. On Debian or Ubuntu systems replace `httpd` with `apache2`:

```
ps -ylC httpd --sort:rss
```

To convert it to megabytes divide the number under the RSS column by 1024.

To get the memory usage information:

```
free -m
```

Use the top command to get a more detailed view of the resources which Apache uses.

## MaxConnectionsPerChild

`MaxConnectionsPerChild` restricts the amount of requests that a child process interacts with over his lifetime. After reaching the mark, the child cycle dies. The child cycle shall never expire if set to 0. With this the recommended value is a few thousand, to prevent memory leakage. Be mindful that setting this too low can slow the system down, as developing new processes takes on resources. `MaxRequestsPerChild` was named for this setting in versions lower than Apache 2.3.9.

## ServerLimit

The `ServerLimit` setting configures the maximum value for `MaxRequestWorkers` over the entire life of the Apache httpd method in the sense of the `prefork` module. When you need to increase `MaxRequestWorkers` over 256, then increasing the corresponding `ServerLimit`.

When using the `worker` and `event` modules, the maximum value for `MaxRequestWorkers` for the length of the Apache httpd phase is calculated by `ServerLimit` and `ThreadLimit`. Notice that unused shared memory will be set aside if `ServerLimit` is set to a value higher than the required.

## KeepAlive

KeepAlive allows connecting clients to make several requests using a single TCP connection, instead of opening one new one for each request. It decreases page load times and increases the use of the CPU for your web server, at the cost of increasing the usage of RAM on your computer. For the MaxConnectionsPerChild a KeepAlive connection will be counted as a single "order."

In the past, this setting was mostly disabled to maintain RAM use, but server resources have become less costly, and Apache 2.4 now allows the option by default. Enabling KeepAlive will improve the user experience of your site considerably, so be careful not to disable it without checking the effects of doing so. In your web server setup, or within a Virtual Host block, KeepAlive can be allowed or deactivated.

Thankyou...
