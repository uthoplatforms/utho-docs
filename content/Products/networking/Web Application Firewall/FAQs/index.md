---
weight: 40
title: "FAQs"
title_meta: "WAF Frequently Asked Questions"
description: "Get answers to the most common questions related to Web Application Firewall (WAF) on Utho Cloud."
keywords: ["WAF", "FAQs", "Web Application Firewall", "security"]
tags: ["utho platform","Web Application Firewall"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/networking/Web Application Firewall/FAQs"]
icon: guides
tab: true
---
# **FAQs – Web Application Firewall (WAF)**

---

### **1. What is a Web Application Firewall (WAF)?**

A WAF protects your web applications by monitoring, filtering, and blocking malicious HTTP/S traffic between your application and the internet. It helps defend against common attacks like SQL injection, Cross-Site Scripting (XSS), file inclusion, and more.


### **2. Do I need to configure anything after creating a WAF?**

Yes. After creating a WAF, you should:
- Attach a Load Balancer or resource.
- Enable/disable the pre-configured **Managed Ruleset**.
- Optionally, create **Custom Rulesets** with tailored logic for your use case.


### **3. What is a Managed Ruleset?**

The Managed Ruleset is a pre-built collection of security rules maintained by Utho to block known web threats. You can enable/disable the entire ruleset or manage individual rule categories (e.g., SQL Injection, XSS, Remote Code Execution).


### **4. What are Custom Rulesets and Custom Rules?**

Custom Rulesets are rule collections you create yourself. Within them, you can define **Custom Rules** based on:
- IP addresses
- GeoIP country codes
- User-Agent patterns
- URI patterns
- HTTP methods
- Header values
- Rate limits
- And more...


### **5. Can I use both Managed and Custom Rules at the same time?**

Yes. Both rule types can be enabled simultaneously. Requests are evaluated against all active rules in both the Managed and Custom sets.


### **6. How do I block traffic from a specific country?**

1. Create or use a Custom Ruleset.
2. Add a rule with type **GeoIP Country Block**.
3. Enter the ISO country code (e.g., `CN` for China, `RU` for Russia).
4. Choose action: `Deny`, `Drop`, etc.


### **7. What happens if I disable the entire Managed Ruleset?**

All pre-configured protections will be turned off. Your WAF will only rely on any active **Custom Rulesets** you’ve defined. This is **not recommended** unless you're building a fully tailored WAF configuration.


### **8. How is rate limiting handled?**

You can define rate-limiting rules in custom rules:
- Format: `<requests>:<seconds>` (e.g., `10:60`)
- If the rule is matched, action (e.g., `Log`, `Deny`) is triggered once the request count exceeds the limit in the given time frame.


### **9. How can I monitor WAF activity?**

Currently, WAF rules can log matched requests (if action `Log` is used). Full logging, analytics, and monitoring dashboards will be added in upcoming versions of the platform.


### **10. Can I re-enable a WAF after destroying it?**

No. Once a WAF is destroyed, it cannot be recovered. All configurations, rulesets, and attachments are permanently deleted. You'll need to create a new WAF from scratch.


### **11. Are changes to WAF rules applied immediately?**

Yes, rule changes (enable/disable, edit, or add new) are applied in real-time. There's no need to restart or redeploy the WAF.

### **12. Will the WAF impact performance?**

The WAF is designed to have minimal latency impact. It operates at the edge of your Load Balancer and filters only HTTP/S traffic.


### **13. How many Load Balancers can be attached to one WAF?**

You can attach multiple Load Balancers to a single WAF. However, all attached Load Balancers will be subject to the same WAF rules.


### **14. Can I export or import WAF configurations?**

Not yet. This feature is in development. For now, you can manually recreate or document your custom rule configurations.


Need more help? Visit our [Support Center](https://support.utho.com) or reach out to our [Support Team](mailto:support@utho.com).
