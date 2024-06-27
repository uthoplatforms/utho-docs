---
title: "SSH Logins with Banner Messages (MOTD File)"
date: "2022-06-22"
title_meta: "SSH Logins with Banner Messages (MOTD File)"
description: "SSH Logins with Banner Messages (MOTD File)"
keywords:  ['ssh logins', 'linux', 'MOTD']
tags: ["linux"]
icon: "linux"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00" 
draft: false
toc: true
aliases: ['/Linux/ssh-logins-with-banner-messages-motd-file-support-internal']
---

One of the easiest ways to protect and secure SSH logins is by displaying warning messages to unauthorized users or displaying welcome or informational messages to authorized users.

There are two ways to display messages one is using the issue.net file and second one is using the MOTD file.

issue.net : Display a banner message before the password login prompt.

motd : Display a banner message after the user has logged in.

```
 # vi /etc/motd 
```

:: After executing the above command, a configuration file will open now you need to past what continent you want to display on the server login screen.

```
 ###############################################################

#    Welcome to Microhost.Com Test Server                                                       # 

#    All connections are monitored and recorded                                                #

#    Disconnect IMMEDIATELY if you are not an authorized user!                     #

#    Thanks Microhost Support 01204840000 Ext:3                                           #

############################################################## 
```

![](images/pasted-image-0-27.png)

:: Now check the server with a new session of SSH to verify that content is showing or not.

![](images/pasted-image-0-1-7.png)

Thank you.
