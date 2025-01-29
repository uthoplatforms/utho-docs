---

title: "Deploy Nginx Ingress on Custom Kubernetes Cluster using Utho Cloud Controller"
date: "2024-12-31"
title_meta: "Nginx Ingress with kubernetes cluster"
description: "This guide provides a comprehensive step-by-step approach to setting up Nginx Ingress with a Kubernetes cluster. It includes configuration of Utho CSI, deployment of persistent storage, and verification of resources for seamless integration of ingress and storage solutions."
keywords: ["Utho","Utho Cloud","Kubernetes", "Nginx Ingress", "kubectl", "StorageClass", "pod deployment","Kubernetes cluster", "YAML configuration", "Utho CSI", "Persistent Storage", "Kubernetes pod", "Helm Chart", "Ubuntu", "Snap installation", "PersistentVolumeClaim"]
tags: ["Kubernetes", "StorageClass", "Utho Cloud", "Nginx", "Ingress", "Utho CSI", "PersistentVolume", "Helm", "DevOps"]
icon: "kubernetes"
lastmod: "2024-12-31T10:00:00+00:00"
draft: false
weight: 1
toc: true
tab: true

---

# **Nginx Ingress with kubernetes cluster**

This document provides a step-by-step guide to configure Nginx Ingress with a Kubernetes cluster.

---

## Prerequisites

- A Kubernetes cluster above version 1.20, set up with your connection configuration configured as the kubectl default. This setup will use a Utho Kubernetes cluster.

- The `kubectl` command-line tool installed in your local environment and configured to connect to your cluster. For more information, see the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/). If you are using a Utho Kubernetes cluster, refer to the **Connect to your Cluster** section when you create your cluster.

- The `Helm` package manager available in your development environment [official documentation](https://helm.sh/docs/intro/install/).

- A fully registered domain name with available A records. This tutorial will use `hw1.your_domain` throughout. You can purchase a domain name on [Namecheap](https://www.namecheap.com), get one for free on [Freenom](https://www.freenom.com), or use the domain registrar of your choice. These A records will be directed to a Load Balancer that you will create in Step 2.

---

## Step 1 — Setting Up Hello World Deployments

Before you deploy the Nginx Ingress, you will deploy a “Hello World” app called `hello-kubernetes` to have some Services to which you’ll route the traffic. To confirm that the Nginx Ingress works properly in the next steps, you’ll deploy it twice, each time with a different welcome message that will be shown when you access it from your browser.

### First Deployment

Create the first deployment configuration file:

```bash
nano hello-kubernetes-first.yaml
```

Add the following configuration:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-kubernetes-first
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: hello-kubernetes-first
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-kubernetes-first
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello-kubernetes-first
  template:
    metadata:
      labels:
        app: hello-kubernetes-first
    spec:
      containers:
      - name: hello-kubernetes
        image: paulbouwer/hello-kubernetes:1.10
        ports:
        - containerPort: 8080
        env:
        - name: MESSAGE
          value: Hello from the first deployment!
```

Apply the configuration:

```bash
kubectl create -f hello-kubernetes-first.yaml
```

Verify the Service:

```bash
kubectl get service hello-kubernetes-first
```

---

### Second Deployment

Create the second deployment configuration file:

```bash
nano hello-kubernetes-second.yaml
```

Add the following configuration:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello-kubernetes-second
spec:
  type: ClusterIP
  ports:
  - port: 80
    targetPort: 8080
  selector:
    app: hello-kubernetes-second
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello-kubernetes-second
spec:
  replicas: 3
  selector:
    matchLabels:
      app: hello-kubernetes-second
  template:
    metadata:
      labels:
        app: hello-kubernetes-second
    spec:
      containers:
      - name: hello-kubernetes
        image: paulbouwer/hello-kubernetes:1.10
        ports:
        - containerPort: 8080
        env:
        - name: MESSAGE
          value: Hello from the second deployment!
```

Apply the configuration:

```bash
kubectl create -f hello-kubernetes-second.yaml
```

Verify the Services:

```bash
kubectl get service
```

Both `hello-kubernetes-first` and `hello-kubernetes-second` should now be up and running.

---

## Step 2 — Installing the Kubernetes Nginx Ingress Controller

Add the Nginx Ingress Helm repository:

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

Install the Nginx Ingress Controller:

```bash
helm install nginx-ingress ingress-nginx/ingress-nginx --set controller.publishService.enabled=true
```

Verify the Load Balancer creation:

```bash
kubectl --namespace default get services -o wide -w nginx-ingress-ingress-nginx-controller
```

---

## Step 3 — Exposing the App Using an Ingress

Create the Ingress Resource file:

```bash
nano hello-kubernetes-ingress.yaml
```

Add the following configuration: done't forget to add your domains

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: hello-kubernetes-ingress
spec:
  ingressClassName: nginx
  rules:
    - host: "hw1.your-domain-name.com"
      http:
        paths:
          - pathType: Prefix
            path: "/"
            backend:
              service:
                name: hello-kubernetes-first
                port:
                  number: 80
    - host: "hw2.your-domain-name.com"
      http:
        paths:
          - pathType: Prefix
            path: "/"
            backend:
              service:
                name: hello-kubernetes-second
                port:
                  number: 80
```

Apply the configuration:

```bash
kubectl apply -f hello-kubernetes-ingress.yaml
```

Verify that accessing your domains works.

---

## Step 4 — Securing the Ingress Using Cert-Manager

Create a namespace for Cert-Manager:

```bash
kubectl create namespace cert-manager
```

Add the Jetstack Helm repository:

```bash
helm repo add jetstack https://charts.jetstack.io
helm repo update
```

Install Cert-Manager:

```bash
helm install cert-manager jetstack/cert-manager --namespace cert-manager --version v1.10.1 --set installCRDs=true
```

Create the production ClusterIssuer:

```bash
nano production_issuer.yaml
```

Add the following configuration: done't forget to add your email

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    email: your_email_address
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: letsencrypt-prod-private-key
    solvers:
    - http01:
        ingress:
          class: nginx
```

Apply the configuration: done't forget to add your domains

```bash
kubectl apply -f production_issuer.yaml
```

Update your Ingress to use TLS:

```yaml
annotations:
  cert-manager.io/cluster-issuer: letsencrypt-prod
...
spec:
  tls:
  - hosts:
    - hw1.your-domain-name.com
    - hw2.your-domain-name.com
    secretName: hello-kubernetes-tls
```

Apply the updated Ingress configuration:

```bash
kubectl apply -f hello-kubernetes-ingress.yaml
```

---

## Conclusion

You have successfully set up the Nginx Ingress Controller and Cert-Manager on your Kubernetes cluster using Helm. Your applications are now accessible via your domains and secured with free TLS certificates from Let’s Encrypt.
