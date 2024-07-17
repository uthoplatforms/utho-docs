---
slug: sql server security part 2
description: 'Learn about the SQL Server security best practices and guidelines to keep your server and data safe. For example, selecting a SQL Server authentication mode.'
keywords: ['SQL server authentication', 'Restrict SQL traffic', 'SQL Server Patches', 'Backups', 'Auditing']
tags: ['database']
published: 2024-07-12
modified_by:
  name: Utho
title: "Part 2: SQL Server Security Best Practices"
title_meta: "SQL Server Security Best Practices, Part 2"
authors: ["Pawan Kumar"]
---

This article is the second installment in a series focusing on SQL Server security best practices. Part 1 addressed the physical security of SQL Server installations, as well as operating system security and application maintenance. It also covered disabling unnecessary features, enabling encryption, and implementing data masking.

The second part of this series describes how and why you should:

- Select an authentication mode.
- Secure the System Administrator (SA) account.
- Assign security-conscious accounts to SQL Server.
- Control SQL traffic.
- Apply patch updates.
- Establish backup strategies.
- Implement auditing measures.

## SQL Server Authentication

Protection of data stored with SQL Server depends upon the ability to authenticate access to specific sets of data. SQL Server provides two options for database authentication in a Windows or Linux environment:

- [*Windows/Linux Authentication*](#windows-or-linux-authentication-mode)
- [*SQL Server and Windows/Linux Authentication*](#sql-server-and-windowslinux-authentication-mode-mixed-mode) (also known as Mixed-mode)

You are prompted to select one of these SQL Server authentication modes during SQL Server setup.

{{< note respectIndent=false >}}
You can change the SQL Server authentication mode even after the initial installation decision has been made.
{{< /note >}}

### Windows or Linux Authentication Mode

In this mode, an installer accesses SQL Server using their Windows or Linux account. SQL Server verifies the account credentials directly with the Windows or Linux operating system. Authentication does not require a separate password prompt from SQL Server, nor does it perform password validation.

Windows or Linux authentication leverages Active Directory (AD) accounts, enabling centralized policy management for authentication. These policies can enforce password strength and complexity, password expiration, account lockout rules, and group memberships within Active Directory.

Windows or Linux-based authentication is the default mode and offers higher security compared to SQL Server Authentication (covered in the next section). It utilizes the Kerberos security protocol to support these security measures. Connections authenticated through Windows or Linux Authentication are often referred to as trusted connections because SQL Server trusts the credentials validated by the underlying Windows or Linux OS.

### SQL Server and Windows/Linux Authentication Mode (Mixed-Mode)

When using SQL Server Authentication, logins are created in SQL Server and are not based on Windows or Linux user accounts. Both the username and the password are created
by SQL Server and are stored within SQL Server. Users connecting using SQL Server Authentication must provide their credentials (username and password) every time that they connect to SQL Server.

This mode does not utilize the Windows or Linux Kerberos security protocol and is generally considered less secure than the Windows or Linux Authentication mode.

## System Administrator (SA) Account

When employing SQL Server (mixed-mode) authentication, SQL Server automatically generates a System Administrator (SA) user login with sysadmin privileges and permissions. To enhance SQL Server security, it is recommended to take the following steps:

1. Rename the SA login account to a different, more obscure, name.
1. Disable the account entirely, if you do not plan on using it.
1. For the SA (or renamed) account, select a complex password, consisting of lower/upper case letters, numbers, and punctuation symbols.
1. Do not allow applications to use the SA (or equivalently renamed) account in any of the application connection strings.

{{< note respectIndent=false >}}
Any other user-based (lower-privileged) SQL Server accounts should also use complex, unique passwords.
{{< /note >}}

## High-Privileged Operating System Accounts

SQL Server uses a Windows or Linux account to run its services. Typically one should not assign high-privileged, built-in accounts (or equivalents) such as *Network Service* or *Local System* to the various SQL Server services. This can increase the risk of nefarious database/server activity, should someone be able to log into these types of accounts.

Only assign the appropriate level of security-required accounts to SQL Server services. If not needed, any high-privileged operating system accounts on the server housing the SQL Server should be disabled as appropriate.

## Restrict SQL Traffic

Database servers typically require one or more connections from client servers. Access to these servers should be restricted to specific IP addresses to prevent unauthorized access. For SQL Server users needing direct database access, it's crucial to limit these connections to designated IP addresses, IP class blocks, or segments.

Implementing IP restrictions can be achieved through various solutions tailored to different platforms:

On Linux systems, iptables is commonly used for traffic control. Other options include UFW, nftables, and FirewallD.
Windows platforms utilize the built-in Windows Firewall or dedicated hardware firewalls.

For Utho Compute Instances hosting SQL Server, consider integrating the Utho Cloud Firewalls service, which offers IP filtering capabilities.

## SQL Server Patches (Service Packs)

Microsoft frequently releases service packs and cumulative updates for SQL Server to address known issues, bugs, and security vulnerabilities. It is strongly recommended to apply these patches to production SQL Server instances. However, before deploying a security patch to production systems, it's prudent to first test the patch in a controlled environment. This ensures that the patch functions correctly and that your database operates as expected with the updates applied.

## Backups

When managing SQL Server production instances, it's crucial to regularly backup the databases. A database backup captures the current state, structure, and stored data, serving as a safeguard against potential failures such as corruption, disk failures, power outages, and disasters.

Backups also play a role in non-failure scenarios where restoring the database to a specific point in time is necessary. It's recommended to perform and maintain full database backups on a scheduled basis, supplemented by incremental backups at regular intervals.

Securing backups is essential and often overlooked by database professionals. Key practices include:

Restricting access to backup files to authorized personnel only, limiting permissions to create, view, modify, and delete.
Encrypting backup files to protect sensitive data.

Storing backups off-site, considering the organization's needs and the criticality of the database data. Older backups should be archived appropriately.

## Auditing

Auditing is another key component of SQL Server security. A designated database administrator or database security team should regularly review SQL Server auditing logs for failed logins.

SQL Server provides a default login audit mechanism for reviewing all of the login accounts. These audit facilities record incoming requests by username and client IP address. Login failures can assist in discovering and eliminating suspicious database activity. The following types of activity can show up in the SQL Server audit logs:

- **Extended Events:** Extended Events is a lightweight performance monitoring system that enables users to collect data needed to monitor and troubleshoot problems in SQL Server.

- **SQL Trace:** SQL Trace is SQL Server's built-in utility that monitors and records SQL Server database activity. This utility can display server activity, create filters that focus on the actions of users, applications, or workstations, and can filter at the SQL command level.

- **Change Data Capture:** Change Data Capture (CDC) uses a SQL Server agent to record insert, update, and delete activity that applies to a specific table.

- **Triggers:** Application-based SQL Server Triggers can be written specifically to populate a user-defined audit table to store changes to existing records in specific tables.

- **SQL Server-Level Audit Specifications:** A Server Audit Specification defines which *Audit Action Groups* can be audited for the entire server (or *instance*). Some audit action groups consist of server-level actions such as the creation of a table or modification of a server role. These are only applicable to the server itself.

Hardware and/or software firewall logs (that is, external to SQL Server) should be regularly examined to monitor and detect any nefarious attempts at server penetration.

## Conclusion

Part two of this article series explored additional methods to enhance the security of SQL Server databases. These methods included selecting an authentication mode, limiting the permissions of the System Administrator (SA) account, assigning secure accounts for SQL Server operations, restricting SQL traffic, applying patch updates, implementing effective backup strategies, and utilizing auditing. For a recap of earlier security recommendations, you can revisit Part 1: SQL Server Security Best Practices.
