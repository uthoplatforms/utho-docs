---
weight: 40
title: "FAQ"
Page_title: "How DNS Management protect our server?"
Page_cardtitle: "How DNS Management integrates with cloud servers?"
title_meta: "API Documentation for DNS Management on the Utho Platform"
description: "DNS Management in Utho involves the administration and configuration of Domain Name System (DNS) settings for domains and services hosted or managed within the Utho platform. This includes tasks such as registering domain names, configuring DNS records, managing DNS zones, and ensuring reliable domain name resolution."
keywords: ["dns_management", "Routing"]
tags: ["utho platform","dns_management"]
icon: "api"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/networking/dns_management/FAQ/']
tab: true
---

# DNS Management FAQ
---
#### 1. What is DNS and why is it important in UTHO?
**DNS (Domain Name System)** translates human-readable domain names (like www.example.com) into IP addresses that computers use to identify each other on the network. In UTHO, DNS is crucial for directing traffic to your hosted applications, ensuring they are accessible to users.

#### 2. How do I configure DNS settings in UTHO?
To configure DNS settings in UTHO, you typically:
- Log in to your UTHO account.
- Navigate to the DNS management section.
- Create a hosted zone for your domain.
- Add DNS records (A, CNAME etc.) to map your domain to the appropriate resources (IP addresses, load balancers, etc.).

#### 3. What types of DNS records can I create in UTHO?
Common DNS records include:
- **A Record:** Maps a domain to an IPv4 address.
- **AAAA Record:** Maps a domain to an IPv6 address.
- **CNAME Record:** Maps a domain to another domain name.
- **MX Record:** Specifies mail servers for your domain.
- **TXT Record:** Allows you to associate text with a domain (often used for verification and security purposes).
- **SRV Record:** Defines services and ports for specific services.
- **NS Record:** Indicates the name servers for a domain.

#### 4. How can I ensure high availability and redundancy for my DNS in UTHO?
Utilize features such as:
- **Multiple DNS servers:** Distribute DNS servers geographically to ensure availability.
- **Failover configurations:** Automatically switch to a backup server if the primary one fails.
- **Health checks:** Monitor the health of your resources and update DNS records dynamically based on resource availability.

#### 5. How do I manage DNS propagation delays in UTHO?
DNS changes can take time to propagate across the internet, usually up to 48 hours. To manage this:
- Plan DNS changes during low-traffic periods.
- Lower the TTL (Time to Live) value for your DNS records before making changes to speed up propagation.
- After the changes are confirmed, increase the TTL value back to reduce DNS query load.

#### 6. What security measures should I implement for my DNS in UTHO?
Ensure the following:
- **DNSSEC (DNS Security Extensions):** Adds a layer of security to prevent DNS spoofing.
- **Access control:** Restrict who can manage your DNS records.
- **Regular audits:** Periodically review and update DNS records to ensure they are correct and secure.
- **DDoS protection:** Use UTHO features or third-party services to protect against DNS-based attacks.

#### 7. How can I migrate my DNS to UTHO?
To migrate DNS:
- Export your existing DNS records from your current provider.
- Import these records into the DNS service of UTHO.
- Update the name server records with your domain registrar to point to your new UTHO DNS servers.
- Monitor the migration and ensure all records resolve correctly before decommissioning the old DNS service.

#### 8. What is the cost of using DNS services in UTHO?
Costs vary by usage but typically include:
- **Hosted zone charges:** A monthly fee per domain.
- **Query charges:** Based on the number of DNS queries made to your domain.
- **Additional features:** Fees for advanced features like DNS health checks, routing policies, and traffic flow management.

#### 9. Can I use my custom domain with UTHO services?
Yes, you can use your custom domain by setting up DNS records in UTHO's DNS management service to point to your hosted resources.

#### 10. How do I troubleshoot DNS issues in UTHO?
Common steps include:
- **Check DNS records:** Ensure they are correctly configured.
- **Use diagnostic tools:** Tools like `nslookup`, `dig`, or UTHO-specific diagnostic tools.
- **Monitor DNS logs:** Review logs to identify and resolve issues.
- **Check TTL values:** Ensure they are not too high, delaying propagation of changes.

#### 11. What are some best practices for managing DNS in UTHO?
- **Automate DNS management:** Use infrastructure-as-code tools to automate DNS configurations.
- **Regularly update records:** Keep DNS records up to date to avoid downtime.
- **Monitor DNS performance:** Use monitoring tools to track DNS performance and resolve issues quickly.
- **Implement failover strategies:** Ensure high availability and redundancy for your DNS.


---