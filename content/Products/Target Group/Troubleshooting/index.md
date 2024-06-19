---
weight: 40
title: "Troubleshoot"
title_meta: "Understanding How TargetGroup Works on the Utho Platform"
description: "Learn how Utho makes TargetGroup deployment simple and easy so you easily anticipate your cloud infrastructure costs"
keywords: ["targetgroup", "security"]
tags: ["utho platform","targetgroup"]
icon: "Troubleshoot"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/TargetGroup/Troubleshooting/']
---

# Troubleshooting Guide for Utho Cloud TargetGroup

## Common Issues

### 1. TargetGroup Not Distributing Traffic
**Problem:** The TargetGroup is not distributing traffic to the endpoints.

**Solution:**
- **Server Health:** Ensure all endpoints in the TargetGroup are healthy and passing health checks. Unhealthy endpoints are automatically removed from the pool.
- **Load Balancer Configuration:** Verify that the load balancer is correctly configured to route traffic to the TargetGroup.
- **Network Configuration:** Check network settings to ensure there are no misconfigurations preventing traffic flow.
- **Traffic Distribution Policy:** Review the traffic distribution policy to ensure it is set up as expected (e.g., round-robin, least connections).

### 2. Endpoints Failing Health Checks
**Problem:** Endpoints in the TargetGroup are failing health checks.

**Solution:**
- **Health Check Settings:** Review and adjust health check settings (protocol, path, interval, timeout, and threshold) to ensure they are correctly configured.
- **Endpoint Status:** Ensure that endpoints are operational and configured to respond to health check requests.
- **Logs and Monitoring:** Use logs and monitoring tools to identify specific reasons for health check failures and troubleshoot accordingly.

### 3. TargetGroup Creation Fails
**Problem:** Unable to create a TargetGroup.

**Solution:**
- **Permissions:** Ensure your account has the necessary permissions to create and manage TargetGroups.
- **Resource Limits:** Check if you have reached any resource limits for your Utho Cloud account.
- **Configuration Details:** Verify that all required details (name, protocol, port, endpoints) are correctly entered.

### 4. Endpoint Connectivity Issues
**Problem:** Endpoints in the TargetGroup are experiencing connectivity issues.

**Solution:**
- **Network Configuration:** Verify that network settings, including subnets and routing tables, are correctly configured to allow traffic to and from the endpoints.
- **Firewall Rules:** Ensure that firewall rules are not blocking traffic to or from the endpoints.
- **Endpoint Status:** Confirm that endpoints are up and running without internal issues.

## Error Messages

### 1. "Endpoint Unreachable"
**Error:** `Endpoint Unreachable`

**Solution:**
- **Network Configuration:** Check network settings and ensure endpoints are reachable from the load balancer.
- **Firewall Rules:** Verify that firewall rules allow traffic between the load balancer and the endpoints.
- **Endpoint Status:** Confirm that endpoints are operational and not experiencing downtime.

### 2. "Health Check Failed"
**Error:** `Health Check Failed`

**Solution:**
- **Health Check Configuration:** Review and adjust health check settings to ensure they correctly reflect the health of endpoints.
- **Endpoint Status:** Ensure that endpoints are configured to respond to health check requests and are operational.
- **Logs and Monitoring:** Use logs and monitoring tools to identify specific issues causing health check failures.

## Support

If you continue to experience issues or need further assistance, please refer to the following resources:

<!-- ### Utho Cloud Documentation
- **TargetGroup Documentation:** [Utho Cloud TargetGroup Documentation](#) *(Replace with actual link)* -->

### Utho Cloud Support
- **Contact Utho Cloud Support:** Access support through the Utho Cloud Management Console or email support@utho.com.

### Community Resources
- **Utho Cloud Forums:** Participate in discussions and seek advice from the Utho Cloud community in the Utho Cloud Forums.

For immediate assistance, you can also contact Utho Cloud Support via your Utho Cloud Management Console.

By following these troubleshooting steps and solutions, you should be able to resolve common issues encountered with Utho Cloud TargetGroup and ensure effective performance and reliability of your grouped endpoints.
