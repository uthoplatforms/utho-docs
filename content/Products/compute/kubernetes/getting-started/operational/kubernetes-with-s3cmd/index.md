---

title: "How to connect object storage with Kubernetes cluster using s3cmd"
date: "2024-12-31"
title_meta: "Utho Cloud object storage with Kubernetes cluster using s3cmd"
description: "This comprehensive guide explains how to connect Kubernetes with s3cmd, a command-line tool for managing UTHO object storage and compatible object storage services. The step-by-step instructions cover setting up kubectl, configuring access to your Kubernetes cluster, creating a pod using a YAML configuration, and installing and testing s3cmd inside the Kubernetes pod. This tutorial is ideal for users looking to manage object storage directly from Kubernetes pods."
keywords: ["Utho","Utho Cloud","Kubernetes", "s3cmd", "kubectl", "object storage", "pod deployment", "Kubernetes cluster", "YAML configuration", "S3 storage", "Amazon S3", "Kubernetes pod", "object storage management", "Ubuntu", "Snap installation", "kubeconfig"]
tags: ["Kubernetes", "Object Storage", "Utho Cloud", "s3cmd"]
icon: "kubernetes"
lastmod: "2024-12-31T10:00:00+00:00"
draft: false
weight: 1
toc: true
tab: true

---

## **How to connect object storage with Kubernetes cluster using s3cmd**

This document provides a step-by-step guide to configure and object storage with Kubernetes cluster using s3cmd.

---

### **Prerequisites**
- Access to the **Utho Cloud UI**.
- Need **kubernetes cluster**.  
- Need **Object Storage**.

---
### **Deployment Steps**

### **Step 1: Installing `kubectl` Using Snap**

 The simplest way to install kubectl on your Ubuntu system is by using
 Snap. Here’s how to do it:

#### 1\. Install kubectl via `Snap`:

 Run the following command to install kubectl:

```bash
sudo snap install kubectl --classic
```
---

#### 2\. Verify Installation `version`:

 To verify that kubectl has been installed correctly,check the version
 of the client using the command below:
```bash
 kubectl version --client
```
 Download the cluster file from k8s cluster

#### 3\.  How to Transfer `Cluster File`:
If you want to transfer cluster file from Linux to Linux.

```bash
 sudo rsync -av kubeconfig_mks_749759.yaml root@<server-ip>:~/kubeconfig_mks_749759.yaml
```
----
### **Step 2: Configuring Access to Your Kubernetes**

 To access and manage your Kubernetes cluster, you need to configure
 kubectl with the cluster configuration file `(kubeconfig)`.

#### 1\. Set the `KUBECONFIG` environment variable:

 Assuming the Kubernetes config file is located at /root/kubeconfig_mks_749759.yaml, use the
following command to point kubectl to the correct configuration file:

```bash
 export KUBECONFIG=/root/kubeconfig_mks_749759.yaml
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

### **Step** **4:** **Now** **we** **need** **to** **deploy** **k8s** **pod**

#### 1.we need to write `s3k82.yaml` file for `Pod`

```bash
vim s3k82.yaml
```
Inside the s3k82.yaml file.
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: s3cmd-pod
  labels:
    app: s3cmd
spec:
  containers:
  - name: s3cmd-container
    image: python:3.9-alpine
    command: ["/bin/sh", "-c"]
    args:
    - apk add --no-cache py3-pip bash;
      pip3 install s3cmd;
      while true; do sleep 30; done;
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    volumeMounts:
    - name: config-volume
      mountPath: /root/.s3cfg
      subPath: s3cfg
  volumes:
  - name: config-volume
    configMap:
      name: s3cmd-config
---
apiVersion: v1
kind: ConfigMap
metadata:
  name: s3cmd-config
data:
  s3cfg: |
    [default]
       access_key = YOUR_ACCESS_KEY
       secret_key = YOUR_SECRET_KEY
       host_base = innoida.utho.io
       host_bucket = %(bucket)s.innoida.utho.io
       use_https = True
       signature_v2 = False
```
---

#### 2.Save the `YAML` content to a file, for example:  `s3k82.yaml`

 Run the following command to apply the configuration: 

```bash
 kubectl apply -f s3k82.yaml
```

#### 3.Verify Deployment

 After deploying, you can check the pod status to ensure it's running:

```bash
 kubectl get pods
```

#### 4. To access the running pod

```bash
 kubectl exec -it s3cmd-pod -- /bin/bash
```
 Now you are entered in: `s3cmd2-pod:/#`

---

#### 5. Test Connection to Object Storage:

To check the list of Utho object storage:
```bash
s3cmd ls
```
Example upload command:
```bash
s3cmd put -r s3cmd.txt s3://mytesting
```

To download a file from a bucket:
```bash
s3cmd get s3:///mytesting/s3cmd.txt
```

To remove a file from a bucket:
```bash
s3cmd del s3:///mytesting/s3cmd.txt
```