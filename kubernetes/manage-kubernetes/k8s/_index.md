---
weight: 10
title: k8s node
title_meta: "Manage Kubernetes on the Utho Platform"
description: "Learn how Utho makes Kubernetes management simple and easy so you easily anticipate your kubernetes infrastructure costs"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/compute/kubernetes/manage-kubernetes/k8s']
icon: 'kubernetes'
tab: true
---
**Adding Node Pools:![1718892225434](image/_index/1718892225434.png)** If your application or workload requires different configurations, click on the "Add node pool" button to create additional node pools with varying specifications.

![1718892289005](image/_index/1718892289005.png)

1. **Node Pool Configuration:**

   * A node pool represents a group of nodes within a Kubernetes cluster that all have the same configuration. Configure your node pool(s) with the following details:
     * **Pool Name:** Provide a name for the node pool to distinguish it from others.
     * **Node Size:** Select the size (CPU and memory configuration) for nodes within this pool.
     * **Desired Count:** Specify the initial number of nodes you want in this pool.
2. **Selecting Node Size:**

   * Click on the "Node Size" option to view available plans. A drawer will open displaying various plans with detailed specifications (CPU, memory, storage).![1718892320118](image/_index/1718892320118.png)

**List Node Pools:**

here user can view the node pool list attached with the kubernetes cluster

![1718892649402](image/_index/1718892649402.png)

**Update Node Pool Configuration:**

when user click on **Open scale pool** then this node pool configuration will open where you can update the node pool

![1718892560270](image/_index/1718892560270.png)

* Update  your node pool(s) configuration with the following details:
  * **Pool Name:** Provide a name for the node pool to distinguish it from others.
  * **Node Size:** Select the size (CPU and memory configuration) for nodes within this pool.
  * **Desired Count:** Specify the initial number of nodes you want in this pool
