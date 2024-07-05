---
slug: "Unleash Kubernetes: Terraform Your Way to an Unmanaged Cluster"
description: "Crafting Kubernetes: Deploying a Cluster with kubectl, Terraform, and utho's K8s Module"
keywords: ['terraform','kubernetes','orchestration','containers','k8s','kubectl','Kubernetes Terraform installer for utho Instances','terraform-utho-k8s']
tags: ["kubernetes", "terraform", "container"]
published: 2024-05-23
modified: 2024-05-23
modified_by:
  name: Utho
title: "Provision Unmanaged Kubernetes Cluster using Terraform"
title_meta: "Provision an Unmanaged Kubernetes Cluster using Terraform"
deprecated: true
authors: ["Utho"]
---

Terraform, HashiCorp's orchestration tool, offers the capability to deploy a Kubernetes cluster on utho. Utilizing utho's Terraform K8s module streamlines the process by creating a Kubernetes (K8s) cluster running on Ubuntu. This module simplifies many deployment steps compared to using kubeadm directly. Upon creating master and worker nodes, the module establishes SSH connections to these instances, installs kubeadm, kubectl, and other Kubernetes binaries to the /opt/bin directory. Additionally, it initializes kubeadm, incorporates worker nodes into the master, and configures kubectl for cluster control. Calico serves as the container networking interface. Moreover, a kubectl config file is installed locally to connect to the cluster's API server.

## Before You Begin

Before embarking on the deployment of a Kubernetes cluster with Terraform, ensure the following:

Familiarize yourself with Terraform. Review A Beginner's Guide to Terraform to grasp key concepts.

Understand Kubernetes concepts. Explore the A Beginner's Guide to Kubernetes series for an introduction. Additionally, acquaint yourself with kubeadm by reading Getting Started with Kubernetes: Use kubeadm to Deploy a Cluster on utho.

Obtain a personal access token for the utho API to use with Terraform. Refer to Getting Started with the utho API for guidance on acquiring a token. Ensure the token is configured with Read/Write access as it's needed for creating new utho servers.

Confirm that Terraform is installed on your computer. Visit Install Terraform for detailed instructions.

Verify that kubectl is installed on your computer. kubectl is essential for connecting to and managing the Kubernetes cluster. Failure to have kubectl installed locally will result in deployment issues using the Terraform module. Refer to Install kubectl for further guidance.

Please note that this guide was composed using Terraform version 0.12.24. The module necessitates a minimum of Terraform 0.10.

## Configure the Local Environment

To deploy a Kubernetes cluster using utho's K8s Terraform module, ensure you have the following:

- A local environment equipped with a kubectl instance.
- Python installed system-wide.
- SSH keys configured and managed by the SSH agent.
- Essential command-line utilities like sed and scp available.
- The module includes a script named preflight.sh which checks for these prerequisites in the local environment. If any of these tools are missing, it triggers a $var not found error. In this segment, you'll discover how to:

Install and configure kubectl.

- Set up the SSH agent.
- Establish an environment variable to store the API v4 token.
- Should you encounter errors indicating the absence of Python, scp, or sed, you can resolve them by utilizing the operating system's package manager to install the missing utilities.

                               {{< note type="secondary" title="Create a Python Alias" isCollapsible=true >}}

If Python is invoked using python3, create an alias for the command so that Terraform can execute scripts locally using Python as its interpreter. To do this, open the ~/.bashrc file using a text editor and add the following alias:

                                 alias python=python3

Next, reload the ~/.bashrc file to apply the changes.

                                 source ~/.bashrc

### Install kubectl

                                {{< content "how-to-install-kubectl" >}}

### SSH Agent

By default, Terraform utilizes the SSH agent of the operating system for connecting to a utho instance via SSH. Here's how to run the SSH agent and include the required SSH keys:

Start the SSH agent by executing the following command:

                                   eval `ssh-agent`

The output resembles:

                          {{< output >}}
                          Agent pid 11308
                          {{</ output >}}

Add the SSH keys to the agent. For detailed instructions, refer to creating an authentication key-pair. This command adds keys from the default location, ~/.ssh/.

                    ssh-add

The output is akin to:

                      {{< output >}}
                       Identity added: /home/example_user/.ssh/id_rsa (/home/example_user/.ssh/id_rsa)
                      {{</ output >}}

### utho API Token

Before running the project, create an access token for Terraform to connect to the utho API.
Prior to initiating the project, generate an access token for Terraform to establish connection with the utho API.

```bash
read -sp "utho Token: " TF_VAR_utho_token # Enter your utho Token (it will be hidden)
export TF_VAR_utho_token
```

Utilize the token and your access key to create the utho_TOKEN environment variable:

This variable must be provided for every Terraform apply, plan, and destroy command using -var utho_token=$utho_TOKEN, unless a terraform.tfvars file is created containing this secret token.

## Create Terraform Configuration Files

Within the directory where Terraform is installed, establish a new directory to store the configuration files for the K8s cluster.

                                     cd terraform
                                     mkdir k8s-cluster

Using a text editor, craft the primary configuration file for the cluster and name it main.tf. Append the following contents to the file:

                                {{< file "~/terraform/k8s-cluster/main.tf">}}
                                  terraform {
                                 required_providers {
                                       utho = {
                              source = "utho/utho"
                                    version = "1.16.0"
                                                }
                                               }
                                              }
                                    module "k8s" {
                          source             = "utho/k8s/utho"
                                      version            = "0.1.2"
                             utho_token       = var.utho_token
                        server_type_master = var.server_type_master
                           server_type_node   = var.server_type_node
                               cluster_name       = var.cluster_name
                                k8s_version        = var.k8s_version
                                     region             = var.region
                                       nodes              = var.nodes
                                        }
                                                    {{</ file >}}

This file encompasses the primary configuration parameters of the cluster. The essential configurations are source, which invokes utho's k8s module, and utho_token, which grants access to utho resources for viewing, creating, and destroying.

The remaining configurations are optional and possess default values. However, in this scenario, Terraform's input variables are utilized, enabling reuse of the main.tf configuration across different clusters.

Create an input variables file named variables.tf containing the example content.

                             {{< file "~/terraform/k8s-cluster/variables.tf">}}
                                                     variable "utho_token" {
                                             description = " utho API token"
                                                                            }

                                              variable "server_type_master" {
                                                default     = "g6-standard-2"
                                            description = " utho API token"
                                                                          }

                                                   variable "cluster_name" {
                                            description = " utho API token"
                                            default     = "example-cluster-1"
                                                                           }

                                              variable "server_type_node" {
                                          description = " utho API token"
                                              default     = "g6-standard-1"
                                                                        }

                                                variable "k8s_version" {
                                       description = " utho API token"
                                                default     = "v1.14.0"
                                                                     }

                                                   variable "region" {
                          description = "Values: us-east, ap-west, etc."
                                                default     = "us-east"
                                                                    }

                                                    variable "nodes" {
                                     description = " utho API token"
                                                      default     = 3
                                                                    }
                                                        {{</ file >}}

The provided example file establishes input variables referenced in the main configuration file. Values for these variables are assigned in a separate file in the subsequent step. The default settings of the k8s module can be overridden, as demonstrated in the example file. For further information regarding input variables, please refer to the Input Variables section in the A Beginner's Guide to Terraform.

Create an input variables values file to furnish the main configuration file with values that deviate from the defaults set in the input variable file.

                        {{< file "~/terraform/k8s-cluster/terraform.tfvars">}}
                                         server_type_master = "g6-standard-4"
                                            cluster_name = "example-cluster-2"
                                                                 {{</ file >}}

In this instance, the master node of the cluster utilizes a g6-standard-4 utho plan, rather than the default g6-standard-2, and the cluster_name is designated as example-cluster-2, instead of example-cluster-1.

### Deploy the Kubernetes Cluster using Terraform

Navigate to the ~/terraform/k8s-cluster/ directory and initialize Terraform to install the utho K8s module.

                                      terraform init

Confirm that Terraform generates the resources of the cluster as expected before effectuating any actual modifications to the infrastructure. Execute the plan command for this purpose:

                                      terraform plan

This command produces a report delineating the actions Terraform will undertake to establish the Kubernetes cluster.

If content with the generated report, proceed to create the Kubernetes cluster by executing the apply command. This command will prompt for confirmation before proceeding.

                                        terraform apply -var-file="terraform.tfvars"

After a brief interval, once Terraform has completed applying the configuration, it will present a report detailing the actions taken. Subsequently, the Kubernetes cluster will be primed for connection.

### Connect to the Kubernetes cluster with kubectl

Once Terraform completes the deployment of the Kubernetes cluster, the ~/terraform/k8s-cluster/ directory should include a file named default.conf. This file holds the kubeconfig file. Employ kubectl in conjunction with this file to access the Kubernetes cluster.

Set the path of the kubeconfig file to the $KUBECONFIG environment variable. In the given command example, the kubeconfig file resides in the Terraform directory established at the outset of this guide. Ensure to update the command with the actual location of the default.conf file:

                              export KUBECONFIG=~/terraform/k8s-cluster/default.conf

Note: Conventionally, kubeconfig files are stored in the ~/.kube directory. By default, kubectl searches for a kubeconfig file named config within the ~/.kube directory. Additional kubeconfig files can be specified by configuring the $KUBECONFIG environment variable.

Inspect the nodes in the cluster using kubectl.

                                 kubectl get nodes

Note: If the kubectl commands fail to return the anticipated resources and information, the client might be assigned to an incorrect cluster context. Refer to the Troubleshooting Kubernetes guide for instructions on switching cluster contexts.

Now equipped to manage the cluster using kubectl. For further guidance on utilizing kubectl, consult the Kubernetes Overview of kubectl.

### Persist the Kubeconfig Context

Subsequent terminal windows do not inherit the context specified in the preceding instructions. To persist this context information across new terminals, configure the KUBECONFIG environment variable in the shell configuration file.

Note: For Windows users, refer to the official Kubernetes documentation for instructions on persisting a context.

The following instructions are tailored for the Bash shell and are analogous in other shells:

Navigate to the $HOME/.kube directory.

                               cd $HOME/.kube

Establish a directory named configs within $HOME/.kube. This directory serves to store kubeconfig files.

                               mkdir configs

Copy the default.conf file to the $HOME/.kube/configs directory.

                               cp ~/terraform/k8s-cluster/default.conf $HOME/.kube/configs/default.conf

Note: Optionally, assign a different name to the copied file to differentiate it from other files in the configs directory.

Access the Bash profile (~/.bashrc) in a text editor and append the configuration file to the $KUBECONFIG PATH variable.

If an export KUBECONFIG line is already present, append the configuration file path to the end of this line; if not, add this line to the end of the file.

                           export KUBECONFIG=$KUBECONFIG:$HOME/.kube/config:$HOME/.kube/configs/default.conf

Close the terminal window and launch a new window to apply the changes to the $KUBECONFIG variable.

Employ the config get-contexts command for kubectl to examine the available cluster contexts:

                             kubectl config get-contexts

The output should resemble the following:

                             {{< output >}}
                            CURRENT   NAME                                 CLUSTER             AUTHINFO           NAMESPACE
                            kubernetes-admin@example-cluster-1   example-cluster-1   kubernetes-admin
                              {{</ output >}}

If your context isn't already selected (indicated by an asterisk in the current column), switch to this context using the config use-context command. Provide the full name of the cluster, including the authorized user and the cluster name:

                             kubectl config use-context kubernetes-admin@example-cluster-1

The output should resemble the following:

                         {{< output >}}
                          Switched to context "kubernetes-admin@example-cluster-1".
                         {{</ output>}}

You're now prepared to interact with the cluster using kubectl. Verify the ability to interact with the cluster by retrieving a list of Pods. Utilize the get pods command with the -A flag to view all pods running across all namespaces:

                           kubectl get pods -A

The output should resemble the following:

    {{< output >}}
    NAMESPACE            NAME                                               READY   STATUS    RESTARTS   AGE
    kube-system          calico-node-5bkc6                                  2/2     Running   0          17m
    kube-system          calico-node-gp5ls                                  2/2     Running   0          17m
    kube-system          calico-node-grpnj                                  2/2     Running   0          17m
    kube-system          calico-node-qd85t                                  2/2     Running   0          17m
    kube-system          ccm-utho-mjgzz                                   1/1     Running   0          17m
    kube-system          coredns-fb8b8dccf-5tlbm                            1/1     Running   0          17m
    kube-system          coredns-fb8b8dccf-7tpgf                            1/1     Running   0          17m
    kube-system          csi-utho-controller-0                            3/3     Running   0          17m
    kube-system          csi-utho-node-gfd8m                              2/2     Running   0          17m
    kube-system          csi-utho-node-hrfnd                              2/2     Running   0          16m
    kube-system          csi-utho-node-q6fmd                              2/2     Running   0          17m
    kube-system          etcd-mytestcluster-master-1                        1/1     Running   0          16m
    kube-system          external-dns-7885f88564-tvpjf                      1/1     Running   0          17m
    kube-system          kube-apiserver-mytestcluster-master-1              1/1     Running   0          16m
    kube-system          kube-controller-manager-mytestcluster-master-1     1/1     Running   0          16m
    kube-system          kube-proxy-cs9tm                                   1/1     Running   0          17m
    kube-system          kube-proxy-qljn5                                   1/1     Running   0          17m
    kube-system          kube-proxy-sr5h8                                   1/1     Running   0          17m
    kube-system          kube-proxy-ww2tx                                   1/1     Running   0          17m
    kube-system          kube-scheduler-mytestcluster-master-1              1/1     Running   0          16m
    kube-system          kubernetes-dashboard-5f7b999d65-jk99z              1/1     Running   0          17m
    kube-system          metrics-server-58db9f9647-tz8f8                    1/1     Running   0          17m
    reboot-coordinator   container-linux-update-agent-6kgqm                 1/1     Running   0          16m
    reboot-coordinator   container-linux-update-agent-7nck5                 1/1     Running   0          17m
    reboot-coordinator   container-linux-update-agent-nhlxj                 1/1     Running   0          17m
    reboot-coordinator   container-linux-update-agent-vv8db                 1/1     Running   0          17m
    reboot-coordinator   container-linux-update-operator-5c9d67d4cf-78wbp   1/1     Running   0          17m
    {{</ output >}}
