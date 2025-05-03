---
weight: 40
title: "Add SSL Certificate"
title_meta: "Add SSL Certificate"
description: "Guide on how to add ssl certificate in utho cloud platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags:  ["utho cloud", "SSL certificate", "HTTPS", "website security", "encryption"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/compute/SSL Certificate/How Tos/Add SSL Certificate"]
icon: "globe"
tab: true
---




# **How to Add an SSL Certificate in Utho Cloud**

### **Overview**

Adding an **SSL certificate** in Utho Cloud allows you to secure your domain or cloud resources by encrypting the communication between clients and servers. Once added, the SSL certificate ensures that sensitive data, such as login credentials or financial transactions, are transmitted securely.

---

### **1. Login to Utho Cloud Platform**

* Visit the Utho Cloud Platform's **[ Login](https://console.utho.com/login)** page.
* Enter your credentials and click  **Login** .
* If you're not registered yet, sign up  **[here](https://console.utho.com/signup)** .

### **2. Navigate to SSL Certificates Listing Page**

* After logging in, go to the **SSL Certificates Listing Page** or click [here](https://console.utho.com/ssl "SSL Listing Page").
* On this page, you will see the list of all SSL certificates created in your account.

---

### **3. Add a New SSL Certificate**

* On the  **SSL Certificates Listing Page** , click on the **"Add SSL Certificate"** button at the top of the page.

  ![1744094699069](image/index/1744094699069.png)
* Clicking this button will open a drawer with the following input options for creating a new SSL certificate:

#### 1. **Name**

* This is the **name** of the SSL certificate. It helps you identify the certificate within your account.

#### 2. **Certificate**

* This is the **SSL certificate content** itself. It's the actual certificate provided by your certificate authority (CA), typically in the format shown:

  ```
  -----BEGIN CERTIFICATE-----
  MIIBIjANBgkqh...
  -----END CERTIFICATE-----
  ```

#### 3. **Private Key**

* This is the **private key** associated with the SSL certificate. It should be kept secure, as it is used to decrypt information sent to your server.

  ```
  -----BEGIN RSA PRIVATE KEY-----
  MIIEowIBAAKCAQEA...
  -----END RSA PRIVATE KEY-----
  ```

#### 4. **Certificate Chain (Optional)**

* This is the **certificate chain** provided by your certificate authority. It typically includes the intermediate certificates that help establish the trust of your certificate. You can add this in the **optional** section.

![1744094833282](image/index/1744094833282.png)

---

### **4. Save the SSL Certificate**

* After entering all the required information, click on the **"Add SSL Certificate"** button to submit the form.
* A **success toast notification** will appear, confirming that the SSL certificate has been successfully added.

  ![1744094861141](image/index/1744094861141.png)

---

### **5. Verify SSL Certificate Addition**

* To verify that your SSL certificate has been added, return to the  **SSL Certificates Listing Page** .
* The newly added SSL certificate should now appear in the list with its details, including its name, issuer, creation date, and expiration date.

  ![1744094892948](image/index/1744094892948.png)

---

### **Conclusion**

Adding an SSL certificate in Utho Cloud is a straightforward process. By following these steps, you can secure your cloud resources with SSL encryption, ensuring that all data transmitted remains safe and protected.
