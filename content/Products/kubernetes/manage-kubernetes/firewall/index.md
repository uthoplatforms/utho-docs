---
weight: 30
title: "Firewall"
title_meta: "Manage kubernetes on the Utho Platform"
description: "Manage your kubernetes instance using simply clicks on utho platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/kubernetes/manage-kubernetes/firewall']
icon: "firewall"
tab: true
---
Utho's firewall feature enables users to configure and manage firewall settings for their cloud server, offering flexibility in network security.

### Attaching a Firewall

Users have the option to attach existing firewalls or create new ones for their kubernetes:![1718888595343](image/index/1718888595343.png)

1. **Attach Existing Firewall** : Users can select from a list of pre-existing firewalls and attach one to their kubernetes.
2. **Create New Firewall** : If no suitable firewall exists, users can create a new one tailored to their specific security requirements. During the creation process, users define firewall rules and settings to enforce network traffic policies.

### Managing Attached Firewalls

Once a firewall is attached to the kubernete, users can:

![1718871303262](image/index/1718871303262.png)

* **View Attached Firewalls** : See a list of all firewalls currently attached to their kubernetes.
* **Detach Firewalls** : For each attached firewall, there is an option to detach it from the kubernetes. This action removes the firewall's rules and configuration from affecting the kubernetes's network traffic.

### Workflow

1. **Create or Select Firewall** : Depending on the user's needs, either create a new firewall or select an existing one.
2. **Attach Firewall** : Once chosen, attach the firewall to the kubernetes to implement specified security measures.
3. **Manage Firewalls** : Regularly review and manage attached firewalls to ensure optimal security and network performance.

Utho's firewall management ensures that kubernetes remain secure against unauthorized access and maintain efficient network traffic management according to user-defined policies.
