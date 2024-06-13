---
title: "Renew with Ease: Let's Encrypt Certificate Guide"
date: "2024-02-06"
---

This article covers the process of renewing Let’s Encrypt SSL certificates installed on your instance. Please note that it does not apply to Let’s Encrypt certificates managed by Utho for load balancers.  
  
Let’s Encrypt utilizes the Certbot client for installing, managing, and automatically renewing certificates. If your certificate doesn't renew automatically on your instance, you can manually trigger the renewal at any time by executing:

<table><tbody><tr><td>sudo certbot renew</td></tr></tbody></table>

If you possess multiple certificates for various domains and wish to renew a particular certificate, utilize:

<table><tbody><tr><td>certbot certonly --force-renew -d example.com</td></tr></tbody></table>

The "--force-renew" flag instructs Certbot to request a new certificate with the same domains as an existing one. Meanwhile, the "-d" flag enables you to renew certificates for multiple specific domains.  
  
To confirm the renewal of the certificate, execute:

<table><tbody><tr><td>sudo certbot renew --dry-run</td></tr></tbody></table>

If the command executes without errors, the renewal was successful.

Renewing Let's Encrypt certificates doesn't have to be daunting. By following the steps outlined in this comprehensive guide, you can ensure your certificates remain up-to-date and your websites stay secure. Whether it's automating the renewal process or manually triggering it when needed, maintaining [SSL](https://utho.com/docs/tutorial/revealing-ssl-crafting-a-web-connection-with-security/) certificates doesn't have to be a hassle. With the right tools and knowledge at your disposal, you can keep your online presence protected without any fuss.
