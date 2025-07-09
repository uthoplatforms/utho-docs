---
weight: 30
title: "Overview"
title_meta: "Overview"
description: "The Manage section of WAF allows you to create, configure, monitor, and control security rules that protect your web applications from common threats and vulnerabilities."
keywords: ["Web Application Firewall", "WAF", "security", "web protection"]
tags: ["utho platform","Web Application Firewall"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/networking/Web Application Firewall/getting-started/Overview"]
icon: guides
tab: true
---
# OVERVIEW

### What is a WAF (Web Application Firewall)?

A **Web Application Firewall (WAF)** is a security solution designed to monitor, filter, and block malicious HTTP(S) traffic directed at your web applications. It helps protect websites and APIs from common web-based threats such as SQL Injection, Cross-Site Scripting (XSS), Remote Code Execution (RCE), and other OWASP Top 10 vulnerabilities.

A WAF sits between your web application and the internet, analyzing incoming requests and outgoing responses to enforce security policies and mitigate attacks in real time.

### Purpose of a WAF

1. **Application Protection**: WAFs protect web applications from known vulnerabilities and exploit attempts without requiring changes to the application code.
2. **Threat Detection**: Detects suspicious patterns in web traffic, such as bot activity, scanners, or injection attempts.
3. **Zero-Day Mitigation**: Offers protection against unknown threats using behavior-based rules and correlation logic.
4. **Security Rule Management**: Allows users to configure both pre-defined and custom security rules to tailor protection to their specific application needs.
5. **Compliance Support**: Assists with meeting regulatory standards like PCI-DSS, HIPAA, or GDPR by securing sensitive web-facing applications.

### Key Features of a WAF

1. **Managed Rulesets**: Built-in set of rules to detect and block common attack vectors like SQLi, XSS, RFI, and more. Users can enable or disable these at a global or individual level.
2. **Custom Rules**: Fully configurable custom rule engine that supports IP blocks, rate limiting, user-agent filtering, URI pattern matching, header inspection, and more.
3. **GeoIP Filtering**: Block or allow traffic based on originating country codes.
4. **Rate Limiting**: Prevent brute-force or denial-of-service attacks by capping the number of requests per IP over a defined time window.
5. **Detailed Logging**: Logs all matched rules and actions taken, helping in audits and incident response.
6. **Granular Actions**: Choose actions like allow, deny, log, drop, or pass for each rule to balance security with functionality.
7. **Header Filtering**: Add protections against CSRF, Referer spoofing, Origin header manipulation, and other header-based exploits.
8. **Resource Binding**: Attach WAFs to Load Balancers, protecting the backend services and APIs behind them.
9. **One-click Restore**: Easily disable or modify rules with minimal downtime or risk to existing traffic.

### Use Cases of a WAF

1. **Website and API Security**: Protect your public-facing websites and backend APIs from injection attacks, bot abuse, and data leaks.
2. **E-commerce Protection**: Safeguard payment systems and customer data against unauthorized access or manipulation.
3. **Prevent Business Logic Abuse**: Stop users from exploiting application workflows (e.g., coupon abuse or login brute-forcing).
4. **Defense Against DDoS**: WAFs with rate-limiting and traffic shaping can mitigate layer 7 denial-of-service attacks.
5. **Security for Legacy Apps**: Provide modern security enforcement to legacy applications that cannot be updated or patched easily.
6. **Multi-tenant Environments**: Isolate and protect applications hosted on shared infrastructure.
7. **GDPR and PCI Compliance**: Ensure sensitive data is handled and protected as per industry compliance standards.

### How to Use WAF in the Utho Cloud Platform

#### 1. **Create a WAF**
- Go to the WAF section in the platform.
- Enter a **Name** for your WAF.
- Click **Create**. Your WAF will be provisioned and available for rule configuration.

#### 2. **Enable Managed Ruleset**
- Toggle the default managed ruleset to ON to apply instant protection.
- Optionally, enable or disable specific rules like SQL Injection, XSS, or Remote Code Execution from the list.

#### 3. **Add Custom Rules**
- Define rules to handle specific patterns or threats.
- Choose from types like IP Block, GeoIP, User-Agent Block, URI Pattern Match, Rate Limit, etc.
- Define action (allow/deny/log/drop/pass), severity, and description for traceability.

#### 4. **Attach Resources**
- Select a **Load Balancer** from your infrastructure to bind with the WAF.
- This ensures all incoming traffic to that load balancer is inspected and filtered as per your WAF rules.

#### 5. **Monitor and Manage**
- Review rule logs and traffic reports to observe threats being blocked or allowed.
- Adjust rulesets dynamically based on evolving attack vectors or business needs.

#### 6. **Destroy WAF (with Caution)**
- To permanently remove a WAF, enter its name to confirm destruction.
- Ensure all load balancers or attached resources are reassigned or protected through other means before deletion.

---

By using a WAF in your cloud infrastructure, you gain a robust layer of defense against both automated and manual attacks, improving the security posture of your applications significantly.
