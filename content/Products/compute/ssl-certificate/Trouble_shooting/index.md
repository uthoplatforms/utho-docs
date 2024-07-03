---
weight: 40
title: "Trouble_shooting"
Page_title: "How to Install an SSL Certificate on Your Cloud Server"
Page_cardtitle: "How to create SSL Certificate"
title_meta: "API Documentation for SSL Certificate on the Utho Platform"
description: "SSL certificates in Utho's cloud services play a crucial role in securing data transmissions and establishing trust between clients and cloud applications. These certificates enable encrypted connections (HTTPS) to protect sensitive information and ensure data integrity across Utho's cloud infrastructure."
keywords: ["SSL Certificate", "Trust"]
tags: ["utho platform","SSL Certificate"]
icon: "api"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/compute/ssl-certificate/Trouble_shooting/']
tab: true
--- 
# Troubleshooting
This section provides solutions to common issues users may encounter while using our SSL Certificate service. If you encounter problems, follow these steps to resolve them or contact our support team for assistance.
#### Common Issues
1. **SSL Certificate deployment failed:**
### Common Causes of SSL Certificate Failures
1. **Expired Certificate**

-SSL certificates have a validity period, typically one or two years. If the certificate is expired, browsers will reject the connection.
2. **Certificate Not Trusted**

- If the SSL certificate is issued by an unknown or untrusted Certificate Authority (CA), browsers may not trust it. This often occurs with self-signed certificates.
3. **Mismatched Domain Name**

- The domain name in the SSL certificate must match the domain name of the website. A mismatch will result in a certificate error.
4. **Incomplete Certificate Chain**

- A complete chain of trust from the SSL certificate back to a trusted root CA must be provided. Missing intermediate certificates can cause SSL errors.

5. **Revoked Certificate**
- Certificates can be revoked by the issuing CA if they are compromised or no longer valid. Browsers check for revocation status, and a revoked certificate will cause a failure.

6. **Incorrect System Time**
- If the client or server system time is incorrect, it can cause SSL certificate validation failures, especially if the time is outside the validity period of the certificate.

7. **Server Configuration Issues**
- Misconfigurations on the server, such as incorrect installation of the SSL certificate or misconfigured server settings, can lead to failures.

## How to Troubleshoot SSL Certificate Failures

1. **Check Expiry Date**
- Verify the SSL certificate's expiration date and renew it if necessary.

2. **Validate Trust Chain**
- Ensure that the SSL certificate is issued by a trusted CA and that the complete chain of trust is included.

3. **Verify Domain Name**
- Check that the domain name in the SSL certificate matches the website's domain name.

4. **Install Intermediate Certificates**
- Make sure all intermediate certificates are installed correctly on the server.

5. **Check for Revocation**
- Verify that the SSL certificate has not been revoked by the CA.

6. **Adjust System Time**
- Ensure that both client and server system times are accurate and within the validity period of the certificate.

7. **Update Protocols**
- Ensure the server supports modern TLS protocols and disable outdated ones.

8. **Review Signature Algorithm**
- Ensure the SSL certificate uses a supported and secure signature algorithm.

9. **Server Configuration Review**
- Double-check the server configuration to ensure the SSL certificate is installed correctly.

 10. **Review Security Software**
- Temporarily disable firewalls or antivirus software to see if they are causing the issue.


#### Support
- **Knowledge Base:** Access our online knowledge base for detailed articles and guides.
- **Community Forum:** Join our community forum to discuss issues and share solutions with other users.
- **Contact Support:** Reach out to our support team via email or phone for personalized assistance.

### Support
#### Customer Support
- **Email:** support@utho.com
- **Phone:**  01204840000
- **Support Hours:** 24/7  
#### Feedback
- **Feedback Form:** Fill out our online feedback form available on the website.
- **User Surveys:** Participate in periodic surveys to help us improve our services.


--- 