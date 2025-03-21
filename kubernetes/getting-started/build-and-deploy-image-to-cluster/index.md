---
weight: 11
title: Build and Deploy Image on kubernetes Cluster  
description: "Learn how to build and deploy image on kubernetes cluster"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/compute/kubernetes/getting-started/build-and-deploy-image-to-cluster']
icon: 'kubernetes'
tab: true
---
This guide walks you through building and deploying an application to your Kubernetes cluster, including sample outputs for verification.

---

## Prerequisites

To follow this guide, you must:

* [Install docker](https://docs.docker.com/engine/install/ "https://docs.docker.com/engine/install/"), the docker command-line tool
* [Install kubectl](https://kubernetes.io/docs/tasks/tools/ "https://kubernetes.io/docs/tasks/tools/"), the Kubernetes command-line tool.

#### **1. Build Your Container Image**

1. **Build the Image** :

```bash
   docker build -t <registry-url>/<image-name>:<tag> .
```

**Sample Output** :

```
   Sending build context to Docker daemon   2.56MB
   Step 1/5 : FROM node:14
   ---> 4f1b1b8bdb1b
   Step 2/5 : WORKDIR /usr/src/app
   ---> Running in 54b0f3b74d5a
   Step 3/5 : COPY package*.json ./
   ---> c6161f4b5dcd
   ...
   Successfully built 12e4f23a1cb4
   Successfully tagged <registry-url>/<image-name>:<tag>
```

1. **Push the Image** :

```bash
   docker push <registry-url>/<image-name>:<tag>
```

**Sample Output** :

```
   The push refers to repository [<registry-url>/<image-name>]
   12e4f23a1cb4: Pushed
   abcdef123456: Pushed
   latest: digest: sha256:abcdef1234567890 size: 1573
```

---

#### **2. Access Your Kubernetes Cluster**

1. **Verify the Cluster Info** :

```bash
   kubectl cluster-info
```

**Sample Output** :

```
   Kubernetes control plane is running at https://123.45.67.89:6443
   CoreDNS is running at https://123.45.67.89:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```

---

#### **3. Create a Deployment Manifest**

* Prepare a YAML file for your application’s deployment and service.
  **`deployment.yaml`**
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: my-app
    labels:
      app: my-app
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: my-app
    template:
      metadata:
        labels:
          app: my-app
      spec:
        containers:
        - name: my-app-container
          image: <registry-url>/<your-image-name>:<tag>
          ports:
          - containerPort: 3000
  ---
  apiVersion: v1
  kind: Service
  metadata:
    name: my-app-service
  spec:
    selector:
      app: my-app
    ports:
      - protocol: TCP
        port: 80
        targetPort: 3000
    type: LoadBalancer
  ```

---

#### **4. Apply the Manifest:**

```
   kubectl apply -f deployment.yaml
```

**Sample Output** :

```
   deployment.apps/my-first-app created
   service/my-first-app-service created
```

---

#### **5. Verify Deployment**

1. **Check Pods** :

```bash
   kubectl get pods
```

**Sample Output** :

```
   NAME                           READY   STATUS    RESTARTS   AGE
   my-first-app-5b987c6bd7-abc12  1/1     Running   0          1m
   my-first-app-5b987c6bd7-def34  1/1     Running   0          1m
```

1. **Monitor Logs** :

```bash
   kubectl logs <pod-name>
```

**Sample Output** :

```
   Server running at http://0.0.0.0:8080
```

1. **Check Services** :

```bash
   kubectl get services
```

**Sample Output** :

```
   NAME                  TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
   my-first-app-service  LoadBalancer   10.96.56.23     123.45.67.89  80:30382/TCP   1m
```

---

#### **6. Access Your Application**

1. **Verify Access with Curl** :

```bash
   curl http://<external-ip>
```

**Sample Output** :

```
   Welcome to My First Kubernetes App!
```

---

#### **7. Update Your Application**

1. **Update the Deployment** :

```bash
   kubectl set image deployment/my-first-app my-first-app=<registry-url>/<new-image>:<new-tag>
```

**Sample Output** :

```
   deployment.apps/my-first-app image updated
```

1. **Check Rollout Status** :

```bash
   kubectl rollout status deployment/my-first-app
```

**Sample Output** :

```
   deployment "my-first-app" successfully rolled out
```

---

#### **8. Clean Up Resources**

1. **Delete the Deployment and Service** :

```bash
   kubectl delete -f deployment.yaml
```

**Sample Output** :

```
   deployment.apps "my-first-app" deleted
   service "my-first-app-service" deleted
```

1. **Verify Cleanup** :

```bash
   kubectl get all
```

**Sample Output** :

```
   No resources found in default namespace.
```

---

With these steps and outputs, you can confidently build, deploy, and verify your application in a Kubernetes cluster managed on Utho.
