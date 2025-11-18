---

title: "Enable Kubernetes Autoscaling on Utho-Managed Cluster"
date: "2024-12-31"
title_meta: "Kubernetes Autoscaling with Utho Cluster Autoscaler"
description: "This guide provides a comprehensive step-by-step approach to enabling Kubernetes autoscaling on a Utho-managed cluster. It covers installation of the Utho Cloud Controller Manager, configuring the Cluster Autoscaler, and verifying scaling with stress testing and monitoring."
keywords: ["Utho", "Utho Cloud", "Kubernetes", "Autoscaling", "Cluster Autoscaler", "kubectl", "Cloud Controller Manager", "Kubernetes cluster", "Node scaling", "Utho Cloud Controller", "Stress test", "YAML configuration", "DevOps", "Kubernetes nodes"]
tags: ["Kubernetes", "Autoscaling", "Utho Cloud", "Cluster Autoscaler", "Cloud Controller Manager", "DevOps", "Infrastructure", "Node Scaling"]
icon: "kubernetes"
lastmod: "2024-12-31T10:00:00+00:00"
draft: false
weight: 1
toc: true
tab: true

---

# **Enable Kubernetes Autoscaling on Utho-Managed Cluster**

This document provides a comprehensive step-by-step guide to enable Kubernetes autoscaling on a Utho-managed cluster. It covers the installation of the Utho Cloud Controller Manager, configuring the Cluster Autoscaler, and verifying scaling with a stress test.

---

## Prerequisites

- A Kubernetes cluster version 1.20 or above set up on Utho Cloud with your connection configuration configured as the kubectl default.

- The `kubectl` command-line tool installed in your local environment and configured to connect to your cluster. For more information, see the [official documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/). If you are using a Utho Kubernetes cluster, refer to the **Connect to your Cluster** section when you create your cluster.

- Administrator access to your Utho Cloud account to generate API tokens for authentication.

- Access to a terminal or command-line interface with internet connectivity to execute the provided commands.

---

## Step 1 — Verify Kubernetes Nodes

Start by verifying the current state of your Kubernetes cluster and the available nodes:

```bash
kubectl get nodes
```

This command displays all nodes currently running in your cluster. Note the number of nodes available before proceeding with the autoscaler installation, as you will use this as a baseline to verify scaling behavior later.

**Expected Output:**

The output will display a list of all nodes with their status, roles, and resource information. Each node should show a status of `Ready`, indicating it is available and healthy.

---

## Step 2 — Deploy Utho Cloud Controller Manager

The Utho Cloud Controller Manager enables your Kubernetes cluster to interact with Utho Cloud infrastructure. Deploy it using the official Utho release manifest:

```bash
kubectl apply -f https://raw.githubusercontent.com/uthoplatforms/utho-cloud-controller-manager/main/docs/releases/latest.yml
```

This command deploys the controller manager to your cluster. The deployment creates necessary resources such as service accounts, cluster roles, and the controller manager pod in the kube-system namespace.

**Expected Output:**

The output confirms that the CCM resources are being applied and deployed. The controller manager communicates with Utho Cloud APIs to manage load balancers, routes, and other cloud infrastructure components.

**Verification:**

You can verify the CCM is running by checking the kube-system namespace:

```bash
kubectl -n kube-system get pods | grep cloud-controller
```

---

## Step 3 — Configure Cluster Autoscaler Secret

The Cluster Autoscaler requires authentication credentials to interact with your Utho Cloud account. First, download the secret configuration template:

```bash
curl -LO https://raw.githubusercontent.com/uthoplatforms/autoscaler/utho-autoscaler/cluster-autoscaler/cloudprovider/utho/examples/cluster-autoscaler-secret.yaml
```

This downloads the YAML configuration file to your local machine. Next, edit the file to add your Utho Cloud credentials:

```bash
nano cluster-autoscaler-secret.yaml
```

**Configuration Details:**

You will need to add two pieces of information to this file:

- **Cluster ID:** Obtain your cluster ID from the Utho Cloud console. This is typically your cluster name and uniquely identifies your cluster within your Utho account.

- **API Token:** Generate an API token from your Utho account settings. Navigate to your account dashboard, select API settings, and create a new token with appropriate permissions for cluster management. Keep this token secure and do not share it publicly.

Update the secret configuration with these values. The file will contain placeholder fields that you replace with your actual credentials.

Apply the secret to your cluster:

```bash
kubectl apply -f cluster-autoscaler-secret.yaml
```

**Expected Output:**

The output confirms that the secret resource has been created in your cluster.

The secret is now stored securely in your cluster and will be used by the Cluster Autoscaler to authenticate with Utho Cloud.

---

## Step 4 — Deploy the Cluster Autoscaler

Deploy the Cluster Autoscaler using the official Utho manifest:

```bash
kubectl apply -f https://raw.githubusercontent.com/uthoplatforms/autoscaler/utho-autoscaler/cluster-autoscaler/cloudprovider/utho/examples/cluster-autoscaler-deployment.yaml
```

This command deploys the Cluster Autoscaler pod, configures it with the necessary permissions, and sets up monitoring for cluster resource usage.

**Expected Output:**

The deployment creates several resources including a deployment, replica set, and pod for the Cluster Autoscaler.

**Verification:**

Verify that the Cluster Autoscaler is running in the `kube-system` namespace:

```bash
kubectl -n kube-system get pods | grep autoscaler
```

**Expected Output:**

The autoscaler pod should show a status of `Running`. Once this is confirmed, the Cluster Autoscaler is active and monitoring your cluster for scaling needs.

---

## Step 5 — Test Autoscaling with Stress Workload

To verify that autoscaling is working correctly, deploy a stress test workload that will require additional resources beyond your current cluster capacity:

```bash
kubectl apply -f https://raw.githubusercontent.com/uthoplatforms/autoscaler/utho-autoscaler/cluster-autoscaler/cloudprovider/utho/examples/stress-test.yaml
```

This command deploys multiple pod replicas designed to consume significant CPU and memory resources.

**Monitoring Workloads:**

Watch the pods being created by the stress test:

```bash
kubectl get pods -w
```

**Expected Behavior:**

Initially, you will see multiple pods in a `Pending` state. This occurs because there are insufficient resources (CPU and memory) on the current nodes to accommodate all the pods. The `-w` flag watches for real-time changes as pods are scheduled.

**Important Note:**

Do not be alarmed by the pending pods. This is the expected behavior and signals to the Cluster Autoscaler that scaling is needed. The autoscaler will detect this and provision additional nodes automatically.

---

## Step 6 — Monitor Node Scaling

Watch the nodes in your cluster as the autoscaler provisions new instances:

```bash
kubectl get nodes -w
```

**Expected Behavior:**

After a few moments, you should observe new nodes being created automatically. The Cluster Autoscaler communicates with Utho Cloud to provision additional resources and add them to your cluster. The `-w` flag shows real-time updates as nodes join the cluster.

**Scaling Completion:**

Once the new nodes are ready and added to the cluster, the pending pods will be scheduled and begin running on these new nodes. You can verify this by running:

```bash
kubectl get pods
```

This confirms that autoscaling has successfully provisioned additional resources to accommodate your workload.

---

## Conclusion

You have successfully enabled Kubernetes autoscaling on your Utho-managed cluster. Throughout this process, you accomplished the following:

- Verified your Kubernetes cluster nodes and confirmed their status before making any changes.
- Installed the Utho Cloud Controller Manager to enable seamless integration between Kubernetes and Utho Cloud infrastructure.
- Configured and applied the Cluster Autoscaler secret with your Utho Cloud credentials to enable authentication.
- Deployed the Cluster Autoscaler to monitor resource usage and automatically scale your cluster based on demand.
- Tested the autoscaling functionality with a stress workload to verify that pending pods trigger node scaling as expected.
- Observed new nodes being provisioned automatically in response to resource demand and workload requirements.

Your cluster is now configured to automatically scale based on workload demands, ensuring efficient resource utilization, improved application availability, and cost-effective infrastructure management. The Cluster Autoscaler will continue to monitor your cluster and adjust the number of nodes as needed to maintain optimal performance and resource efficiency.