---
weight: 40
title: "Troubleshoot"
title_meta: "Understanding How VPC Works on the Utho Platform"
description: "Learn how Utho makes VPC deployment simple and easy so you easily anticipate your cloud infrastructure costs"
keywords: ["vpc", "security"]
tags: ["utho platform","vpc"]
icon: "Troubleshoot"
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/networking/vpc/Troubleshooting/']
---

# Common Issues
### Subnetting and IP Address Management
- **Subnet Overlaps**: Accidental overlap of IP ranges between subnets can cause connectivity issues or unexpected routing behavior.
- **IP Address Exhaustion**: Poorly planned IP address allocation can lead to running out of available IP addresses within a subnet.

### Security Configuration
- **Inadequate Security Group Rules**: Misconfigured security groups can lead to unintended exposure of resources or overly restrictive access, impacting application functionality.
- **Network Access Control Lists (ACLs)**: Misconfigurations in ACL rules can block legitimate traffic or allow unauthorized access.

### Connectivity Issues
### Internet Connectivity

- **Issue**: Instances in VPC cannot access the internet.
- **Resolution**: Check route tables and ensure there is an Internet Gateway (IGW) attached and routes are properly configured.

### Subnet Configuration

- **Issue**: Instances in a subnet cannot communicate with each other or other subnets.
- **Resolution**: Verify subnet CIDR blocks and Network Access Control Lists (ACLs) for proper configurations.

### Error Messages
### Route Table Misconfigurations

- **Issue**: Incorrect routes leading to traffic not reaching the intended destination.
- **Resolution**: Audit route tables for correct entries and update as necessary.

### NAT Gateway/Elastic IP

- **Issue**: Instances in private subnets cannot initiate outbound connections to the internet.
- **Resolution**: Ensure NAT Gateway is properly configured in the public subnet with appropriate Elastic IP assignment.

### Support
For further assistance, please contact our support team at support@utho.com.