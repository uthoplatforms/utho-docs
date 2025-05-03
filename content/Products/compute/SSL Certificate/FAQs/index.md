---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "FAQs on SSL in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "SSL certificate", "HTTPS", "website security", "encryption"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/compute/SSL Certificate/FAQs"]
icon: "globe"
tab: true
---

# FAQ for SSL Certificates in Utho Cloud Platform

## **1. What is an SSL certificate and why do I need it? 🤔**
An SSL certificate is a digital certificate used to establish a secure connection between a client (usually a web browser) and a server. It encrypts data to protect it during transmission and is necessary to ensure secure communication over HTTPS. Without it, sensitive data like passwords or payment details could be intercepted.

## **2. How do I add an SSL certificate in Utho Cloud? 🛡️**
To add an SSL certificate:
1. [**Login**](https://console.utho.com/login) to the Utho Cloud platform.
2. Navigate to the SSL Certificates [**Listing Page**](https://console.utho.com/ssl).
3. Click on the "Add SSL Certificate" button.
4. Fill in the required details (name, certificate content, private key, and certificate chain).
5. Save the certificate.

## **3. How can I view my SSL certificates in Utho Cloud? 👀**
To view your SSL certificates:
1. [**Login**](https://console.utho.com/login) to the Utho Cloud platform.
2. Go to the SSL Certificates [**Listing Page**](https://console.utho.com/ssl).
3. You’ll see a list of all your SSL certificates, with details like the certificate’s name, issuer, expiration date, and creation date.

## **4. Can I delete an SSL certificate in Utho Cloud? ❌**
Yes, you can delete an SSL certificate:
1. Go to the SSL Certificates  [**Listing Page**](https://console.utho.com/ssl).
2. Find the certificate you want to delete.
3. Click the "Delete" button next to it.
4. Confirm the deletion in the popup.

## **5. How do I update my SSL certificate if it expires? 🔄**
When an SSL certificate is about to expire, you’ll need to generate and add a new one:
1. [**Login**](https://console.utho.com/login)  to the Utho Cloud platform.
2. Navigate to the SSL Certificates [**Listing Page**](https://console.utho.com/ssl).
3. Click on the "Add SSL Certificate" button.
4. Enter the updated certificate details and save the new certificate.
5. The old certificate will be replaced, and your site or service will remain secure.

## **6. What happens if I don't renew my SSL certificate? ⚠️**
If you don't renew your SSL certificate before it expires, your website or cloud resource will no longer be secure. Visitors will see warnings in their browsers about the insecure connection, and sensitive data transmitted between users and your server could be at risk.

## **7. How can I check the expiration date of my SSL certificate? 📅**
You can view the expiration date of your SSL certificate in the SSL Certificates [**Listing Page**](https://console.utho.com/ssl). Each certificate will display an "Expire At" field, showing the exact date and time the certificate will expire.

## **8. What should I do if I accidentally delete my SSL certificate? 😱**
Unfortunately, once an SSL certificate is deleted, it cannot be recovered. You will need to create and upload a new certificate to maintain secure communication.