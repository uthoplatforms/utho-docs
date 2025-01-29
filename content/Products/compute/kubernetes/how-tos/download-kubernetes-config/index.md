---
weight: 10
title: Download Kubernetes Config file
title_meta: "Download Kubernetes Config file"
description: "Learn how Utho makes Kubernetes management simple and easy so you easily anticipate your kubernetes infrastructure costs"
keywords: ["Kubernetes", "Instances",  "scaling", "server"]
tags: ["utho platform","Kubernetes"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ['/products/kubernetes/how-tos/download-kubernetes-config']
icon: 'api'
tab: true
---
This document explains how clients can download a Kubernetes configuration file (`kubeconfig`) using an API and validate it for correctness before use.

## 1. Downloading the Kubeconfig File

### API Endpoint

* **URL:** `https://api.utho.com/v2/kubernetes/{cluster_id}/download`
* **Method:** `GET`
* **Headers:**
  * `Authorization: Bearer <API_TOKEN>`
  * `Accept: application/yaml`

### Request Example

```sh
curl -H "Authorization: Bearer YOUR_API_TOKEN" \
     -H "Accept: application/yaml" \
     -o kubeconfig.yaml \
     -X GET "https://api.utho.com/v2/kubernetes/750031/download"
```

### Response

* If successful, the API will return a Kubernetes configuration file in YAML format.
* If unauthorized or the cluster does not exist, the API will return an error response.

**Example File** :

```
apiVersion: v1
kind: Config
clusters:
- name: my-cluster
  cluster:
    server: https://my-cluster.example.com
    certificate-authority-data: BASE64_ENCODED_CA_DATA
contexts:
- name: my-context
  context:
    cluster: my-cluster
    user: my-user
current-context: my-context
users:
- name: my-user
  user:
    client-certificate-data: BASE64_ENCODED_CERT_DATA
    client-key-data: BASE64_ENCODED_KEY_DATA
```

## 2. Validating the Kubeconfig File

After downloading, you should validate the `kubeconfig.yaml` file to ensure it is correctly formatted and functional.

### 2.1 Syntax Validation

To check if the file is properly formatted:

```sh
yq eval '.' kubeconfig.yaml  # Requires yq tool
```

Or using Python:

```python
import yaml

try:
    with open("kubeconfig.yaml", "r") as file:
        yaml.safe_load(file)
    print("Valid YAML file.")
except Exception as e:
    print(f"Invalid YAML: {e}")
```

### 2.2 Checking Kubernetes Context

Ensure that the kubeconfig file is correctly configured:

```sh
kubectl config view --kubeconfig=kubeconfig.yaml
```

### 2.3 Testing Connection to the Cluster

Verify access to the cluster using:

```sh
export KUBECONFIG=kubeconfig.yaml
kubectl get nodes
```

If you receive node details, the kubeconfig is valid and functional.

## 3. Troubleshooting Issues

| Error                   | Possible Causes              | Solution                     |
| ----------------------- | ---------------------------- | ---------------------------- |
| `Invalid YAML format` | Corrupted or incomplete file | Re-download and check syntax |
| `Unauthorized`        | Invalid API token            | Ensure correct API token     |
| `Unable to connect`   | Network/firewall issues      | Verify cluster connectivity  |
