
# Connecting to Utho's Kubernetes Cluster via Kubeconfig

## Introduction

This guide will help you connect to Utho's Kubernetes cluster using the `kubeconfig` file on macOS, Windows, and various Linux distributions.

## Prerequisites

- Kubernetes command-line tool (kubectl) installed.
- A `kubeconfig` file downloaded from Utho's Kubernetes User Interface.

### Installing kubectl

#### macOS

```bash
brew install kubectl
```

#### Windows

Use Chocolatey:

```powershell
choco install kubernetes-cli
```

Or use Scoop:

```powershell
scoop install kubectl
```

#### Linux

Use `snap`:

```bash
sudo snap install kubectl --classic
```

Or use `apt` for Debian-based distributions:

```bash
sudo apt-get update
sudo apt-get install -y kubectl
```

Or use `yum` for Red Hat-based distributions:

```bash
sudo yum install -y kubectl
```

## Configuring kubectl with kubeconfig

### macOS

1. **Locate your kubeconfig file**: Ensure you have the `kubeconfig` downloaded from Utho's Kubernetes User Interface.

2. **Set the KUBECONFIG environment variable**: Open your terminal and set the `KUBECONFIG` variable to the path of your `kubeconfig` file.

```bash
export KUBECONFIG=/path/to/your/kubeconfig
```

3. **Verify the configuration**: Run the following command to verify that your configuration is working.

```bash
kubectl get nodes
```

### Windows

1. **Locate your kubeconfig file**: Ensure you have the `kubeconfig` downloaded from Utho's Kubernetes User Interface.

2. **Set the KUBECONFIG environment variable**:
   - Open Command Prompt or PowerShell.
   - Set the `KUBECONFIG` environment variable.

```powershell
$env:KUBECONFIG="C:\path\to\your\kubeconfig"
```

3. **Verify the configuration**: Run the following command to verify that your configuration is working.

```powershell
kubectl get nodes
```

### Linux

1. **Locate your kubeconfig file**: Ensure you have the `kubeconfig` downloaded from Utho's Kubernetes User Interface.

2. **Set the KUBECONFIG environment variable**: Open your terminal and set the `KUBECONFIG` variable to the path of your `kubeconfig` file.

```bash
export KUBECONFIG=/path/to/your/kubeconfig
```

3. **Verify the configuration**: Run the following command to verify that your configuration is working.

```bash
kubectl get nodes
```

## Permanent Configuration

To avoid setting the `KUBECONFIG` environment variable every time, you can add the export command to your shell profile.

### macOS and Linux

Add the following line to your `~/.bashrc`, `~/.zshrc`, or `~/.profile` file:

```bash
export KUBECONFIG=/path/to/your/kubeconfig
```

### Windows

Set the `KUBECONFIG` variable permanently via the System Properties:

1. Right-click on `This PC` and select `Properties`.
2. Click on `Advanced system settings`.
3. Click on `Environment Variables`.
4. Under `User variables` or `System variables`, click `New`.
5. Set `Variable name` to `KUBECONFIG` and `Variable value` to the path of your `kubeconfig` file.

## Troubleshooting

- **Issue**: `kubectl` command not found.
  - **Solution**: Ensure `kubectl` is installed and available in your system's PATH.

- **Issue**: Permission denied errors.
  - **Solution**: Ensure your `kubeconfig` file has the correct permissions. You can change the permissions with the following command:

```bash
chmod 600 /path/to/your/kubeconfig
```

## Conclusion

You should now be able to connect to Utho's Kubernetes cluster using the `kubeconfig` file. For further assistance, please contact Utho support.
