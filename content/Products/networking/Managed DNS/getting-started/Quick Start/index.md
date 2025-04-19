---
weight: 30
title: Manage Elastic Block Storage
title_meta: "Manage Elastic Block Storage on the Utho Platform"
description: "The Manage section of EBS allows you to configure settings, resize volumes, attach or detach them from instances, and destroy volumes when no longer needed."
keywords: ["Elastic Block Storage", "storage"]
tags: ["utho platform","Elastic Block Storage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false 
aliases: ['/products/Elastic Block Storage/manage-loadbalancer']
icon: guides
tab: true
---
## **Login or Sign Up**

1. Go to the **Utho Cloud Platform** [login](https://console.utho.com/login) page.
2. Enter your credentials and click  **Login** .
3. If you don't have an account, sign up [here](https://console.utho.com/signup).

---

## **Accessing Managed DNS**

1. Open the **Utho Cloud Platform** dashboard.
2. Click on **"Managed DNS"** in the sidebar.
3. You will be redirected to the **Managed DNS** listing page.
4. Click on **[Deploy DNS](https://console.utho.com/dns/deploy "E")** to open the deployment page.

On the  **Deploy Page** , configure the following:

Fill in the **Domain Name**  A new page will open. In this page, enter the domain name for which you want to create the DNS.

Click on **"Deploy DNS**   After filling in the domain name, click the "Deploy DNS" button.

**Benefits of Deploying DNS**

1. **Faster Website Access**: Deploying DNS ensures that your domain name resolves to the correct IP address, improving website loading speed.
2. **Improved Reliability**: Managed DNS providers offer redundancy, failover, and high availability, reducing the risk of downtime.
3. **Scalability**: Easily handle increased traffic by scaling DNS services without needing to manage the underlying infrastructure.
4. **Better Security**: DNS deployment includes features like DDoS protection, DNSSEC, and fraud prevention to secure your domain.
5. **Simplified Management**: Deploying DNS via a managed service simplifies the process of adding, updating, and managing DNS records.
6. **Geo-Localization**: Direct traffic to the closest server based on the user's location, improving website performance globally.

##### **Verify DNS Creation**

The DNS will be created, and you will be redirected  to the manage page also the newly created DNS will now be visible in the list on the homepage of the Managed DNS service.

# Managing DNS Service

1. **Click on "Manage"** Button  After creating the DNS, click the "Manage" button to open the management section.
2. Navigate to **Manage Section**  The "Manage" section will open, and you'll see two main subsections: **Resources** and **Destroy**.
3. **Resources Section** In the "Resources" section, you will see common headings displayed as columns:

   - **Type**: Specifies the kind of DNS record (e.g., A, CNAME, MX).
   - **Hostname**: Defines the subdomain or root domain for the DNS record.
   - **Value**: Contains the data associated with the record (e.g., IP address, domain name).
   - **TTL**: The time duration (in seconds) for which the DNS record is cached by resolvers.
   - **Action**: Provides options to modify or delete the record.
4. **Dropdown in Type Column**  In the **Type** column, there will be a dropdown menu. The available options in the dropdown may vary depending on the DNS service and your configuration, but common options include:

   When managing DNS records, you will encounter various **Type** values in the dropdown. These values represent different types of DNS records that you can configure for your domain. Below are the most common types:

   ---

   ## 1. A Record (Address Record) in Managed DNS

   An **A Record (Address Record)** is used in DNS to map a domain name to an **IPv4 address**. This type of record helps to direct internet traffic to the correct server by linking a domain name (like `example.com`) to its corresponding IP address.

   #### Columns in A Record:


   1. **Type**:

      - Specifies the record type, which in this case is `A`.
      - The Type column shows "A" to indicate that this record maps a domain name to an **IPv4 address**.
   2. **Hostname**:

      - Refers to the subdomain or root domain that the record applies to (e.g., `www`, `mail`, or `@` for the root domain).
      - Example: For `www.example.com`, the **hostname** would be `www`.
   3. **Value**:

      - The **IPv4 address** that the domain or subdomain points to (e.g., `192.168.1.1`).
      - This is the actual address to which the domain resolves when queried by DNS servers.
   4. **TTL (Time to Live)**:

      - Defines how long the DNS record is cached by DNS resolvers, in seconds. Once the TTL expires, the record is refreshed.
      - A lower TTL means more frequent DNS lookups, while a higher TTL reduces the frequency of lookups.
      - Example values might include `3600` seconds (1 hour) or `86400` seconds (1 day).
   5. **Action**:

      - This column provides options to perform actions on the A record.
      - Typically, options include **Add New** (to add the record) or **Delete** (to remove the record).

   ---

   ## 2. AAA Record (IPv6 Address Record) in Managed DNS

   An **AAA Record** is a type of DNS record used to map a domain name to an **IPv6 address**. Similar to the A record, which maps a domain to an IPv4 address, the AAAA record allows a domain to resolve to an IPv6 address, which is the newer standard for IP addressing.

   #### Explanation of Columns in AAAA Record:

   1. **Type**:

      - Specifies the record type, which in this case is `AAAA`.
      - The Type column indicates that this record maps a domain name to an **IPv6 address**.
   2. **Hostname**:

      - Refers to the subdomain or root domain that the record applies to (e.g., `www`, `mail`, or `@` for the root domain).
      - Example: For `www.example.com`, the **hostname** would be `www`.
   3. **Value**:

      - The **IPv6 address** that the domain or subdomain points to (e.g., `2001:0db8:85a3:0000:0000:8a2e:0370:7334`).
      - This is the actual IPv6 address that the domain will resolve to when queried by DNS servers.
   4. **TTL (Time to Live)**:

      - Defines how long the DNS record is cached by DNS resolvers, in seconds. Once the TTL expires, the record is refreshed.
      - A lower TTL leads to more frequent DNS lookups, while a higher TTL reduces the frequency of lookups.
      - Example values might include `3600` seconds (1 hour) or `86400` seconds (1 day).
   5. **Action**:

      - This column provides options to perform actions on the AAAA record.
      - Typically, options include **Edit** (to modify the record) or **Delete** (to remove the record).

   ---

   ## 3. CNAME Record (Canonical Name Record) in Managed DNS

   A **CNAME Record (Canonical Name Record)** is used to map a domain name to another domain name (an alias). It allows one domain to point to another, rather than directly linking to an IP address. This is commonly used for subdomains (like `www.example.com`) to point to the main domain (`example.com`) or other services.

   #### Explanation of Columns in CNAME Record:

   1. **Type**:

      - Specifies the record type, which in this case is `CNAME`.
      - The Type column indicates that this record creates an alias for a domain, pointing it to another domain name.
   2. **Hostname**:

      - Refers to the subdomain or domain name that you want to alias (e.g., `www`, `ftp`, `shop`).
      - This is the domain or subdomain you are redirecting to another canonical name.
   3. **Value**:

      - The **canonical (real) domain name** to which the hostname will point (e.g., `example.com`).
      - The **Value** column contains the destination domain name that the alias will resolve to.
      - **Note**: The value must always be a fully qualified domain name (FQDN), ending with a dot (`.`) in some systems.
   4. **TTL (Time to Live)**:

      - Defines how long the DNS record is cached by DNS resolvers, in seconds. Once the TTL expires, the record is refreshed.
      - A lower TTL results in more frequent DNS lookups, while a higher TTL reduces the lookup frequency.
      - Example values might include `3600` seconds (1 hour) or `86400` seconds (1 day).
   5. **Action**:

      - This column provides options to perform actions on the CNAME record.
      - Typically, options include **Edit** (to modify the record) or **Delete** (to remove the record).

   ---

   ## 4. MX Record (Mail Exchange Record) in Managed DNS

   An **MX Record (Mail Exchange Record)** is used to specify the mail servers responsible for receiving email on behalf of a domain. It directs email traffic to the appropriate mail server and includes a priority value to determine the order in which mail servers should be used.

   #### Explanation of Columns in MX Record:

   1. **Type**:

      - Specifies the record type, which in this case is `MX`.
      - The Type column indicates that this record is used for mail exchange, directing email to the correct mail server.
   2. **Hostname**:

      - Refers to the **domain name** for which the MX record is created (e.g., `example.com`).
      - This is typically set to `@` for the root domain or a subdomain (e.g., `mail.example.com` for custom mail servers).
   3. **Value**:

      - The **mail server domain name** that handles incoming email for the domain (e.g., `mail.example.com` or `mx.mailserver.com`).
      - This is the actual server that will receive and process email messages for the domain.
   4. **Priority**:

      - A number that determines the **priority** of the mail server. Lower values have higher priority. If multiple mail servers are defined, the mail server with the lowest priority number will be tried first.
      - This allows the configuration of backup mail servers in case the primary mail server is unavailable.
      - Example: `10` is a higher priority than `20`.
   5. **TTL (Time to Live)**:

      - Defines how long the DNS record is cached by DNS resolvers, in seconds. Once the TTL expires, the record is refreshed.
      - A lower TTL results in more frequent DNS lookups, while a higher TTL reduces the lookup frequency.
      - Example values might include `3600` seconds (1 hour) or `86400` seconds (1 day).
   6. **Action**:

      - This column provides options to perform actions on the MX record.
      - Typically, options include **Edit** (to modify the record) or **Delete** (to remove the record).

   ---

   ## 5. TXT Record (Text Record) in Managed DNS

   A **TXT Record** is used to store arbitrary text data in DNS. It is often used for purposes like domain ownership verification, email validation (e.g., SPF, DKIM), and other custom configurations. TXT records are versatile and can store any kind of textual information.

   #### Explanation of Columns in TXT Record:

   1. **Type**:

      - Specifies the record type, which in this case is `TXT`.
      - The Type column indicates that this record is used for storing **text-based information** in DNS.
   2. **Hostname**:

      - Refers to the **domain name** or subdomain that the TXT record is associated with (e.g., `example.com` or `@` for the root domain).
      - The **hostname** is where the text data will be applied, and it can be a specific subdomain or the main domain.
   3. **Value**:

      - The **text string** that will be stored in the record (e.g., `v=spf1 include:_spf.google.com ~all` for SPF records).
      - This value contains the actual textual data, which may be used for verification purposes, email authentication, or other services.
   4. **TTL (Time to Live)**:

      - Defines how long the DNS record is cached by DNS resolvers, in seconds. Once the TTL expires, the record is refreshed.
      - A lower TTL results in more frequent DNS lookups, while a higher TTL reduces the lookup frequency.
      - Example values might include `3600` seconds (1 hour) or `86400` seconds (1 day).
   5. **Action**:

      - This column provides options to perform actions on the TXT record.
      - Typically, options include **Edit** (to modify the record) or **Delete** (to remove the record).

   ---

   ## 6. SRV Record (Service Record) in Managed DNS

   An **SRV Record (Service Record)** is used to define the location of specific services (such as voice or messaging servers) on a domain. It allows you to direct traffic to the correct server for a particular service based on the protocol, port, and other criteria.

   #### Explanation of Columns in SRV Record:

   1. **Type**:

      - Specifies the record type, which in this case is `SRV`.
      - The Type column indicates that this record is used to map services to specific server locations.
   2. **Hostname**:

      - Refers to the **service** being provided (e.g., `_sip`, `_xmpp`, `_ldap`).
      - This identifies the service for which the SRV record is created, often prefixed with an underscore.
   3. **Value (Target)**:

      - The **target server hostname** or IP address where the service is hosted (e.g., `sipserver.example.com`).
      - This is the server that handles the specific service defined in the **Hostname** column.
   4. **Priority**:

      - A **priority value** that determines the order in which servers should be used. Lower numbers indicate higher priority. If multiple SRV records exist, the one with the lowest priority number will be tried first.
      - This allows for failover if the primary server is unavailable.
   5. **Weight**:

      - Defines the **relative weight** of records with the same priority. It is used to distribute traffic evenly between multiple servers.
      - Higher weight values indicate a higher likelihood of being selected.
   6. **Port**:

      - The **port number** on which the service is running (e.g., `5060` for SIP services).
      - This is the port on the target server that will accept incoming connections for the defined service.
   7. **TTL (Time to Live)**:

      - Defines how long the DNS record is cached by DNS resolvers, in seconds. Once the TTL expires, the record is refreshed.
      - A lower TTL results in more frequent DNS lookups, while a higher TTL reduces the lookup frequency.
      - Example values might include `3600` seconds.

   ---

   ## 7. PTR Record (Pointer Record) in Managed DNS

   A **PTR Record (Pointer Record)** is used in DNS for **reverse DNS lookups**. Unlike regular DNS records that map domain names to IP addresses, a PTR record maps an **IP address** to a **domain name**. PTR records are typically used to verify the origin of emails or other services to ensure authenticity.

   #### Explanation of Columns in PTR Record:

   1. **Type**:

      - Specifies the record type, which in this case is `PTR`.
      - The Type column indicates that this record is used for reverse DNS lookups, mapping an IP address to a domain name.
   2. **Hostname**:

      - Refers to the **IP address** (in reverse order) for which the PTR record is created.
      - For example, for the IP address `192.168.1.1`, the **Hostname** would be `1.1.168.192.in-addr.arpa`.
   3. **Value**:

      - The **domain name** that is associated with the IP address.
      - This is the fully qualified domain name (FQDN) that the PTR record will resolve to (e.g., `mail.example.com`).
      - The PTR record maps the reverse IP address to this domain name.
   4. **TTL (Time to Live)**:

      - Defines how long the DNS record is cached by DNS resolvers, in seconds. Once the TTL expires, the record is refreshed.
      - A lower TTL results in more frequent DNS lookups, while a higher TTL reduces the lookup frequency.
      - Example values might include `3600` seconds (1 hour) or `86400` seconds (1 day).
   5. **Action**:

      - This column provides options to perform actions on the PTR record.
      - Typically, options include **Edit** (to modify the record) or **Delete** (to remove the record).

   ---

   ## Common Fields Across All Types:

   - **Hostname**: This is typically the **subdomain** (e.g., `www`, `mail`) or **@** for the root domain.
   - **Value**: The actual data for the DNS record. The format depends on the record type (e.g., an IP address for A/AAAA, a domain name for CNAME, text data for TXT).
   - **TTL (Time to Live)**: This defines how long DNS resolvers should cache the record. A shorter TTL ensures quicker updates but increases DNS query traffic, while a longer TTL decreases the frequency of lookups.

   By selecting the appropriate **Type** from the dropdown, the fields like **Hostname**, **Value**, and **TTL** adjust based on the specific record type requirements. Understanding these values will help you correctly configure and manage your DNS settings.

## Actions

---

For each DNS record, an **Action** column will provide options to **Add New** or **Delete** the record.

## Destroy DNS Record in Managed DNS

1. **Navigate to the Destroy Section**:

   - On the Managed DNS dashboard, go to the **Destroy** section where you manage DNS deletion.
2. **Click on the Destroy Button**:

   - In the Destroy section, click the **Destroy** button next to the DNS record you wish to delete.
3. **Pop-up for Confirmation**:

   - A confirmation pop-up will appear to ensure that you want to permanently delete the DNS record.
4. **Confirm Deletion**:

   - Confirm the action in the pop-up by clicking **OK**.
5. **DNS Deletion**:

   - Once confirmed, the DNS record will be permanently deleted from the system.
