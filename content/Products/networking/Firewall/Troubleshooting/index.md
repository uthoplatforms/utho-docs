---
weight: 40
title: "Troubleshoot"
title_meta: "Understanding How Firewal Works on the Utho Platform"
description: "Learn how Utho makes Firewal deployment simple and easy so you easily anticipate your cloud infrastructure security"
keywords: ["firewall", "security"]
tags: ["utho platform","firewall"]
icon: "Troubleshoot"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/networking/Firewall/Troubleshooting/']
---

<!-- # Troubleshooting Guide for Utho Cloud Firewall -->

# Common Issues

<!-- ### 1. Unable to Create Firewall -->
**Problem:** You encounter errors when trying to create a new firewall.

**Solution:**
- **Check Permissions:** Ensure your account has the necessary permissions to create firewalls.
- **Network Configuration:** Verify that your virtual networks and subnets are properly configured in Utho Cloud.
- **Data Center Availability:** Ensure that the selected data center location supports firewall deployment.
- **Error Messages:** Review error messages for specific details that can help diagnose the issue.

<!-- ### 2. Firewall Rules Not Applied -->
**Problem:** Newly created or modified firewall rules are not being applied.

**Solution:**
- **Rule Priority:** Verify the priority of the rules. Lower priority rules may be overriding intended rules.
- **Syntax Errors:** Check for any syntax errors or conflicts in the rule definitions.
- **Rule Scope:** Ensure that the rules apply to the correct source and destination addresses and ports.
- **Firewall Status:** Confirm that the firewall is active and not disabled.

<!-- ### 3. Connectivity Issues -->
**Problem:** Users are experiencing connectivity issues after firewall deployment.

**Solution:**
- **Port Restrictions:** Review firewall rules to ensure that necessary ports are open for desired traffic.
- **Protocol Settings:** Check that the firewall rules allow the required protocols (TCP, UDP, etc.) for applications.
- **Security Groups:** Verify that security groups associated with instances allow traffic from required sources.
- **Logs and Monitoring:** Use firewall logs and monitoring tools to identify blocked or restricted traffic patterns.

<!-- ### 4. Performance Degradation -->
**Problem:** Performance of applications or services is degraded after firewall implementation.

**Solution:**
- **Traffic Inspection:** Reduce the level of deep packet inspection or other intensive firewall features if applicable.
- **Rule Optimization:** Simplify and optimize firewall rules to reduce processing overhead.
- **Network Segmentation:** Consider segmenting network traffic to distribute load and improve performance.
- **Hardware Requirements:** Ensure that firewall hardware resources meet or exceed recommended specifications.

## Error Messages

<!-- ### 1. Access Denied -->
**Error:** `Access Denied`

**Solution:**
- **Permissions:** Check if the user or application has the required permissions to access resources through the firewall.
- **Firewall Rules:** Review firewall rules to ensure that the action is allowed for the specified source and destination.

<!-- ### 2. Configuration Error -->
**Error:** `Configuration Error`

**Solution:**
- **Rule Validation:** Validate firewall rules for syntax errors or conflicts.
- **Rule Application:** Ensure that changes to firewall configurations are properly applied and saved.

## Support

If you continue to experience issues or need further assistance, please refer to the following resources:

<!-- ### Utho Cloud Documentation
- **Firewall Documentation:** [Utho Cloud Firewall Documentation](#) *(Replace with actual link)* -->

### Utho Cloud Support
- **Contact Utho Cloud Support:** Access support through the Utho Cloud Management Console or email support@utho.com.

### Community Resources
- **Utho Cloud Forums:** Participate in discussions and seek advice from the Utho Cloud community in the Utho Cloud Forums.

For immediate assistance, you can also contact Utho Cloud Support via your Utho Cloud Management Console.

By following these troubleshooting steps and solutions, you should be able to resolve common issues encountered with Utho Cloud Firewall and ensure effective protection of your network infrastructure.
