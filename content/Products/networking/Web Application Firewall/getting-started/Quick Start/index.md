---
weight: 30
title: "Quick Start"
title_meta: "Quick Start"
description: "Learn how to quickly deploy and configure a Web Application Firewall (WAF) on Utho Cloud to protect your applications from malicious traffic."
keywords: ["Web Application Firewall", "WAF", "security"]
tags: ["utho platform","Web Application Firewall"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ["/products/networking/Web Application Firewall/getting-started/Quick Start"]
icon: guides
tab: true
---
## **Login or Sign Up**

1. Go to the **Utho Cloud Platform** [login](https://console.utho.com/login) page.
2. Enter your credentials and click **Login**.
3. If you don't have an account, sign up [here](https://console.utho.com/signup).

---

## **Accessing WAF**

1. From the Utho Cloud dashboard, click on **"WAF"** in the sidebar.
2. You'll be redirected to the **WAF listing** page.
3. Click on **[Create WAF](https://console.utho.com/waf/deploy)** to begin the setup process.

---

## **Create WAF**

1. **WAF Name**: Enter a unique name to identify your WAF instance.
2. Click on **Create WAF** to deploy.

Your WAF will now appear in the list of deployed WAFs.

---

## **Attach Load Balancer**

1. Click on the deployed WAF name to open its management page.
2. Navigate to the **Resources** section.
3. Click **Attach Load Balancer** and select from available load balancers.
4. Click **Attach** to link the WAF with your application’s load balancer.

All attached load balancers will be protected by the rules configured in this WAF.

---

## **Manage Rules**

### **Managed Ruleset**
1. Navigate to the **Manage Rules** tab.
2. Toggle categories like **SQL Injection**, **Cross-Site Scripting (XSS)**, or **Remote Code Execution**.
3. These are maintained by Utho and provide ready-to-use security protections.

### **Custom Ruleset**
1. Create your own **Custom Ruleset** for tailored filtering logic.
2. Within it, define rules using match types like:
   - **IP Address**
   - **GeoIP Country**
   - **User-Agent**
   - **URI Pattern**
   - **Header Match**
   - **Rate Limit**

Each rule supports actions like **Allow**, **Deny**, **Drop**, **Log**, and **Pass**.

---

## **Destroy WAF**

1. Scroll to the bottom of the WAF Manage page.
2. Click the **Destroy WAF** button.
3. Enter the WAF name to confirm.
4. Click **Destroy** to permanently delete the WAF and all associated data.

⚠️ This action is irreversible.

---

## **Verify Configuration**

Once your WAF is configured:
- Confirm that the correct load balancers are attached.
- Ensure at least one rule is active (managed or custom).
- Check the **status** of your WAF to be **Active**.

---

## Summary

| Feature            | Purpose                                                                 |
|--------------------|-------------------------------------------------------------------------|
| **WAF Name**       | Identifies your firewall instance                                       |
| **Attach LB**      | Protects your web apps via Load Balancer integration                    |
| **Managed Ruleset**| Built-in protections for common web attacks                             |
| **Custom Ruleset** | Fine-grained control over request filtering                             |
| **Destroy WAF**    | Removes the WAF and all configurations after user confirmation          |

For more details, visit the [WAF Overview](https://docs.utho.com/products/security/WAF/getting-started/Overview) or [FAQs](https://docs.utho.com/products/security/WAF/faqs).
