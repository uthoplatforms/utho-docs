---
title: "How to solve \"Cannot connect to CWP Admin Panel\""
date: "2023-03-21"
---

![](images/How-to-solve-Cannot-connect-to-CWP-Admin-Panel-1024x576.png)

Hostname SSL cert can be reason for not starting cwp when all ports are allowed in OS and [Cloud Firewall](https://utho.com/docs/tutorial/microhost-cloud-firewall/).

Step 1. Login to your linux server

Run The following command:

```
sh /usr/local/cwpsrv/htdocs/resources/scripts/generate_hostname_ssl
```

Step 2. Now run the follwoing command

```
sh /usr/local/cwpsrv/htdocs/resources/scripts/restart_cwpsrv
```

Now connecting your CWP through browser by hitting http://server\_ip:2030

Your [CWP](https://control-webpanel.com/) should now have no difficulty in connecting.

Thank you!
