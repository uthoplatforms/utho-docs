# **Quick Start Guide for SSL Certificates in Utho Cloud**

### **Overview**

SSL certificates are essential for securing communications between your cloud resources and users, ensuring data is encrypted and transmitted securely. In Utho Cloud, you can easily manage SSL certificates by creating, viewing, and deleting them through the platform's intuitive interface.

---

### **1. Login to Utho Cloud Platform**

* Visit the **[Utho Cloud Platform Login](https://console.utho.com/login)** page.
* Enter your credentials and click  **Login** .
* If you’re not registered yet, sign up  **[here](https://console.utho.com/signup)** .

---

### **2. Navigate to SSL Certificates Listing Page**

* Once logged in, go to the **SSL Certificates Listing Page** or click [here](https://console.utho.com/ssl "SSL Listing Page").
* Here, you’ll see a list of all the SSL certificates associated with your account.

---

### **3. Add a New SSL Certificate**

* To add a new SSL certificate, click the **"Add SSL Certificate"** button at the top of the listing page.
* This will open a drawer where you’ll need to provide the following details:
  * **Name** : Choose a name for your SSL certificate to easily identify it.
  * **Certificate** : Enter the certificate content, typically in PEM format, including the certificate chain.
  * **Private Key** : Enter the private key that corresponds to the certificate.
  * **Certificate Chain (Optional)** : Optionally, enter the certificate chain if applicable.
* After filling out the details, click the **"Add SSL Certificate"** button.
* You’ll receive a **success toast notification** confirming that the SSL certificate was added.

---

### **4. View SSL Certificates**

* Once the SSL certificate is added, it will appear in the SSL certificates listing.
* You can view the following details for each certificate:
  * **Name** : The name you assigned to the certificate.
  * **Issuer** : Information about the certificate issuer, typically in the form of a Distinguished Name (DN).
  * **Expire At** : The expiry date and time of the SSL certificate.
  * **Created At** : The date and time the SSL certificate was created.
  * **Action** : The delete button to remove the certificate if needed.

---

### **5. Delete an SSL Certificate**

* To delete an SSL certificate, locate the certificate in the  **SSL Certificates Listing Page** .
* At the end of each certificate entry, click the **Delete** button.
* A confirmation **popup** will appear.
* Click **OK** to confirm the deletion, and the certificate will be removed permanently.
* After deletion, verify the removal by checking the list again.

---

### **Conclusion**

Managing SSL certificates in Utho Cloud is simple and straightforward. You can quickly add, view, and delete certificates through the SSL Certificates Listing Page. By following this guide, you can ensure that your cloud resources remain secure with the proper SSL certificates in place.
