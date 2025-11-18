# README: Kubernetes Autoscaling on Utho-Managed Cluster

## Overview

This repository contains comprehensive documentation and guides for enabling Kubernetes autoscaling on a Utho-managed cluster. The Cluster Autoscaler automatically adjusts the number of nodes in your cluster based on resource demand, ensuring optimal performance and cost efficiency.

---

## Contents

This package includes the following files:

### 📄 Main Documentation
- **`kubernetes-autoscaling-utho.md`** - Complete step-by-step guide for setting up Kubernetes autoscaling on Utho clusters

### 📋 Quick Reference
This README file provides quick access information and troubleshooting tips.

---

## Quick Start

### Prerequisites
- Kubernetes cluster v1.20+ on Utho Cloud
- `kubectl` command-line tool installed and configured
- Helm package manager (optional but recommended)
- Administrator access to Utho Cloud account
- API token from Utho Cloud console

### Installation Summary

```bash
# 1. Verify nodes
kubectl get nodes

# 2. Deploy Cloud Controller Manager
kubectl apply -f https://raw.githubusercontent.com/uthoplatforms/utho-cloud-controller-manager/main/docs/releases/latest.yml

# 3. Configure and apply autoscaler secret
curl -LO https://raw.githubusercontent.com/uthoplatforms/autoscaler/utho-autoscaler/cluster-autoscaler/cloudprovider/utho/examples/cluster-autoscaler-secret.yaml
nano cluster-autoscaler-secret.yaml  # Add your cluster ID and API token
kubectl apply -f cluster-autoscaler-secret.yaml

# 4. Deploy Cluster Autoscaler
kubectl apply -f https://raw.githubusercontent.com/uthoplatforms/autoscaler/utho-autoscaler/cluster-autoscaler/cloudprovider/utho/examples/cluster-autoscaler-deployment.yaml

# 5. Verify autoscaler is running
kubectl -n kube-system get pods | grep autoscaler
```

---

## Detailed Steps

For complete, step-by-step instructions with detailed explanations, please refer to the main documentation file: **`kubernetes-autoscaling-utho.md`**

---

## Getting Your API Token

1. Log in to your Utho Cloud account
2. Navigate to **Account Settings** → **API Tokens**
3. Click **Create New Token**
4. Name your token (e.g., "Kubernetes Autoscaler")
5. Select appropriate permissions for cluster management
6. Copy the token immediately (you won't be able to see it again)
7. Use this token in the Cluster Autoscaler secret configuration

---

## Finding Your Cluster ID

Your cluster ID is typically your cluster name. To find it:

1. Log in to Utho Cloud console
2. Navigate to **Kubernetes** → **Clusters**
3. Select your cluster
4. Copy the cluster ID/name from the cluster details page

---

## Verification Commands

### Check Cluster Status
```bash
kubectl get nodes -o wide
```

### Verify Cloud Controller Manager
```bash
kubectl -n kube-system get pods | grep cloud-controller
```

### Monitor Cluster Autoscaler
```bash
kubectl -n kube-system get pods | grep autoscaler
kubectl -n kube-system logs -f deployment/cluster-autoscaler
```

### Check Node Scaling
```bash
kubectl get nodes -w
```

### Monitor Pending Pods
```bash
kubectl get pods -w --all-namespaces
```

---

## Testing Autoscaling

To verify autoscaling is working:

```bash
# Deploy stress test workload
kubectl apply -f https://raw.githubusercontent.com/uthoplatforms/autoscaler/utho-autoscaler/cluster-autoscaler/cloudprovider/utho/examples/stress-test.yaml

# Watch pods (should see some in Pending state)
kubectl get pods -w

# In another terminal, watch nodes scale
kubectl get nodes -w

# After scaling completes, verify pods are running
kubectl get pods
```

---

## Troubleshooting

### Issue: Pods remain in Pending state

**Solution:** 
- Check autoscaler logs: `kubectl -n kube-system logs -f deployment/cluster-autoscaler`
- Verify secret is applied correctly: `kubectl -n kube-system get secrets | grep autoscaler`
- Ensure API token has sufficient permissions in Utho Cloud

### Issue: Autoscaler pod is not running

**Solution:**
- Verify the deployment was applied: `kubectl -n kube-system get deployment cluster-autoscaler`
- Check pod events: `kubectl -n kube-system describe pod <pod-name>`
- Review deployment logs: `kubectl -n kube-system logs deployment/cluster-autoscaler`

### Issue: Nodes not scaling up

**Solution:**
- Verify Cloud Controller Manager is running
- Check autoscaler logs for error messages
- Verify API token and cluster ID are correct in the secret
- Ensure your Utho account has sufficient resources/quota available

### Issue: Nodes scaling too aggressively

**Solution:**
- Adjust autoscaler configuration parameters in the deployment
- Set resource requests and limits on your pods for better scheduling decisions
- Review the autoscaler documentation for tuning options

---

## Important Notes

⚠️ **Security:**
- Never commit API tokens to version control
- Store tokens securely and rotate them regularly
- Limit token permissions to minimum required scope

⚠️ **Cost Management:**
- Monitor your cluster scaling to understand cost implications
- Set resource requests/limits on pods for better bin packing
- Consider using node auto-repair features

⚠️ **Best Practices:**
- Always specify resource requests and limits for your pods
- Test autoscaling behavior before production deployment
- Monitor cluster autoscaler logs regularly
- Keep your cluster updated with latest Utho releases

---

## Common Commands Reference

| Command | Purpose |
|---------|---------|
| `kubectl get nodes` | List all cluster nodes |
| `kubectl get pods -A` | List all pods across namespaces |
| `kubectl -n kube-system get pods` | List system pods |
| `kubectl -n kube-system logs -f deployment/cluster-autoscaler` | View autoscaler logs |
| `kubectl describe node <node-name>` | Get detailed node information |
| `kubectl top nodes` | View node resource usage |
| `kubectl top pods` | View pod resource usage |

---

## Related Documentation

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)
- [Kubernetes Autoscaler GitHub](https://github.com/kubernetes/autoscaler)
- [Utho Cloud Documentation](https://docs.utho.com/)
- [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)

---

## Support & Resources

- **Utho Cloud Support:** https://support.utho.com
- **Kubernetes Community:** https://kubernetes.io/community/
- **GitHub Issues:** Report issues or suggest improvements

---

## Version Information

- **Documentation Version:** 1.0
- **Last Updated:** December 31, 2024
- **Kubernetes Version:** 1.20+
- **Utho Autoscaler Version:** Latest

---

## Next Steps

1. Follow the complete guide in `kubernetes-autoscaling-utho.md`
2. Deploy the Utho Cloud Controller Manager
3. Configure and apply the Cluster Autoscaler secret
4. Deploy the Cluster Autoscaler
5. Test with a stress workload
6. Monitor and optimize your scaling configuration

For detailed instructions on each step, please refer to the main documentation file.

---

**Happy Scaling! 🚀**