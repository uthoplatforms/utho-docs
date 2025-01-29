---

title: "How to connect with Utho Kubernetes cluster using Linux"
date: "2024-12-12"
title_meta: "How to connect with Utho Kubernetes cluster using Linux"
description: "Guide on connecting to a Kubernetes cluster using Linux, including installing kubectl, configuring access with the kubeconfig file, and verifying cluster connection and pod status."
keywords: ["Kubernetes", "Linux", "kubectl", "kubeconfig", "Kubernetes cluster", "Utho k8s", "Kubernetes pods", "cluster access", "Kubernetes setup"]
tags: ["Kubernetes", "Linux setup", "kubectl installation", "cluster connection", "Kubernetes pods", "Utho Cloud"]
icon: "kubernetes"
lastmod: "2024-12-12T10:00:00+00:00"
draft: false
weight: 1
toc: true
tab: true

---

## **How to connect with Kubernetes cluster using Linux**

This document provides a step-by-step guide on connecting to a Kubernetes cluster using Linux, including installing kubectl, configuring access with the kubeconfig file, and verifying cluster connection and pod status.

---

### **Prerequisites**
- Access to the **Utho Cloud UI**.
- Access to the **Kubernetes cluster** provided by Utho Cloud.  
- The **kubeconfig** file downloaded from the Utho Kubernetes cluster.
- A **Linux-based system** with administrative privileges.

---
### **Deployment Steps**

### **Step 1: Installing `kubectl` Using Snap**

 The simplest way to install kubectl on your Linux system is by using
 Snap. Here’s how to do it:

#### 1\. Install kubectl via `Snap`:

 Run the following command to install kubectl:

```bash
sudo sna p install kubectl --classic
```
---

#### 2\. Verify Installation `version`:

 To verify that kubectl has been installed correctly,check the version
 of the client using the command below:
```bash
 kubectl version --client
```
 Download the kubeconfig file from the Utho k8s cluster. 

#### 3\.  How to Transfer `Cluster File`:
If you want to transfer cluster file from one Linux system to another Linux system.

```bash
 sudo rsync -av kubeconfig_mks_$CLUSTERID.yaml root@<server-ip>:~/kubeconfig_mks_$CLUSTERID.yaml
```
----
### **Step 2: Configuring Access to Your Kubernetes**

 To access and manage your Kubernetes cluster, you need to configure
 kubectl with the cluster configuration file `(kubeconfig)`.

#### 1\. Set the `KUBECONFIG` environment variable:

 Assuming the Kubernetes config file is located at **/root/kubeconfig_mks_$CLUSTERID.yaml**, use the
following command to point kubectl to the correct configuration file:

```bash
 export KUBECONFIG=/root/kubeconfig_mks_$CLUSTERID.yaml
```
---
#### 2\. Verify `Cluster` Connection:

 To ensure you’re connected to the cluster, run:

```bash
 kubectl cluster-info
```
---
### **Step** **3:** **Checking** **Running** `Pods` **in** **the** **Kubernetes**

#### 1\. Check Pods:

 To see the pods running in your Kubernetes cluster, use the following
 command:

```bash
 kubectl get nodes
```

```bash
 kubectl get pods --all-namespaces
```

 This command will list all running pods in every namespace.

---


```bash
 kubectl get pods
```
This command will show staus of running pods.

---
