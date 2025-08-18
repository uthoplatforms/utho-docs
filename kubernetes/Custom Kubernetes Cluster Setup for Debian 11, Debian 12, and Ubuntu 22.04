# Kubernetes Cluster Setup for Debian 11, Debian 12, and Ubuntu 22.04

This guide will help you quickly set up a Kubernetes cluster using kubeadm on Debian 11, Debian 12, or Ubuntu 22.04 servers.

---

## 1. Master Node Setup

Run this script on your **Master Node**:

```bash
curl -fsSL https://raw.githubusercontent.com/<your-github-username>/<your-repo>/main/master-node.sh | bash
After setup completes, your join command will be shown automatically.
````
You can also get the join command anytime by running:
```bash
kubeadm token create --print-join-command
```

2. Worker Node Setup
3. 
Run this script on each Worker Node:

```bash
curl -fsSL https://raw.githubusercontent.com/<your-github-username>/<your-repo>/main/worker-node.sh | bash
```

3. Verify Cluster Status
   
On the Master Node, check all nodes:
```bash
kubectl get nodes -o wide
```
Check all system pods:
```bash
kubectl get pods -n kube-system -o wide
```

4. Assign Worker Role Label
   
If your worker node is not automatically labeled, run:
```bash
kubectl label node <worker-node-name> node-role.kubernetes.io/worker=worker
```
Example:
```
kubectl label node workernode1 node-role.kubernetes.io/worker=worker
```
5. Troubleshooting
6. 
Node shows NotReady:

Check CNI plugin:
```bash
kubectl get pods -n kube-flannel
```
Reinstall Flannel if needed:
```bash
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml
```
Join Command Expired:
Generate a new one:
```bash
kubeadm token create --print-join-command

`````

<p align="center">
  <img src="https://img.shields.io/badge/Satyajit%20Barik-FF5733?style=for-the-badge&labelColor=000000&color=FF5733">
  <img src="https://img.shields.io/badge/DevOps%20Engineer-2E86C1?style=for-the-badge&labelColor=000000&color=2E86C1">
  <img src="https://img.shields.io/badge/Utho%20Platforms%20Private%20Limited-28B463?style=for-the-badge&labelColor=000000&color=28B463">
</p>
