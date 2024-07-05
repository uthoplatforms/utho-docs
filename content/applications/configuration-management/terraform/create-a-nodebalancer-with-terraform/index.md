---
slug: "Balancing Acts: Terraforming Your NodeBalancer"
description: 'Unlock the power of Terraform with our step-by-step guide on setting up a NodeBalancer and Nodes for your Utho system. Master installation and configuration effortlessly'
keywords: ['terraform','nodebalancer','node','balancer','provider','utho']
published: 2024-04-03
modified: 2024-04-03
modified_by:
  name: Utho
title: "Create a NodeBalancer with Terraform"
authors: ["Utho"]
---
Terraform simplifies managing your infrastructure by turning it into code. With it, you can speed up deployments, collaborate on configurations, and manage resources efficiently. In this guide, you'll learn how to use Terraform to set up a NodeBalancer, which distributes traffic across two Uthos.

{{< note type="alert" >}}
Please note that following the configurations and commands in this guide will add billable resources to your account. Keep an eye on your account in the Utho Cloud Manager to avoid unexpected charges. Refer to the Billing and Payments guide for more information.

If you want to stop being billed for the resources created in this guide, simply [remove them](#optional-remove-the-nodebalancer-resources) them once you've completed your tasks.
{{< /note >}}

## Before You Begin

1. Before proceeding, ensure you have Terraform installed on your development environment and are familiar with configuring Terraform resources using the Utho provider. For guidance on Terraform installation and usage, refer to Terraform’s official documentation.

{{< note respectIndent=false >}}
Please note that the Utho Provider for Terraform has been updated and now requires Terraform version 0.12 or higher. To safely upgrade to Terraform version 0.12, consult [Terraform’s official documentation](https://www.terraform.io/upgrade-guides/0-12.html). For a comprehensive list of new features and version incompatibility notes, refer to [Terraform v0.12’s changelog](https://github.com/hashicorp/terraform/blob/v0.12.0/CHANGELOG.md).

The examples provided in this guide were written with compatibility for [Terraform version 0.11](https://www.terraform.io/docs/configuration-0-11/terraform.html) but will be updated soon to align with newer versions.
    {{< /note >}}

1. To get started with Terraform, you'll need an API access token. Follow the steps outlined in the Getting Started with the Utho API guide to obtain your token.

2. Create a new directory named `terraform_nodebalancer` on your computer. This directory will hold all the files related to your Terraform project. Make sure to run all commands from within this directory.

Note: Avoid creating this project directory inside another Terraform project directory, including any you might have created while following Use Terraform to Provision Utho Environments.

## Generate a Terraform Configuration File

### Create a Provider Block

To start creating a Terraform configuration file, begin by setting up a *provider block*. This block informs Terraform about the provider to utilize. For the Utho provider, the only necessary configuration is the API access token.

In your Terraform project directory, create a file named `nodebalancer.tf`. This file will be where you define your configurations. You'll be updating this file as you progress through the guide.

Inside `nodebalancer.tf`, add the provider blocks.

      {{< file "nodebalancer.tf" >}}

      terraform {
      required_providers {
      utho = {
      source = "utho/utho"
      version = "1.16.0"
      }
      }
      }

      provider "utho" {
      token = var.token
      }
      {{< /file >}}

In this provider block, variable interpolation is used to retrieve the API token value. Later in the guide, you'll create input variables in a separate file called `variables.tf` as explained in the [Define Terraform Variables](#define-terraform-variables) section. Any input variables defined in variables.tf can be accessed using dot notation from the `var` dictionary. Throughout this guide, you'll utilize variable interpolation and reference variables using dot notation.

### Create a NodeBalancer Resource

Define a NodeBalancer resource within the `nodebalancer.tf` file:

    {{< file "nodebalancer.tf" >}}
    ...

    resource "utho_nodebalancer" "example-nodebalancer" {
    label = "examplenodebalancer"
    region = var.region
    }

    ...
    {{< /file >}}

The `utho_nodebalancer` resource requires two labels. The first label, `example-nodebalancer`, is used internally by Terraform, while the second label, `examplenodebalancer`, serves as a reference for your NodeBalancer in tools like the Manager and the Utho CLI. The region for this NodeBalancer is specified using the `region` variable.

### Creating NodeBalancer Configuration Resources
Apart from the NodeBalancer resource, you need to define at least one NodeBalancer Configuration resource. This resource outlines ports, protocols, health checks, session stickiness, and other settings that the NodeBalancer will utilize. In this example, a NodeBalancer configuration for HTTP access on port 80 will be created. However, you can also set up one for HTTPS access on port 443 if you have [SSL/TLS certificates](/docs/guides/install-lets-encrypt-to-create-ssl-certificates/).

    {{< file "nodebalancer.tf" >}}
    ...

    resource "utho_nodebalancer_config" "example-nodebalancer-config" {
    nodebalancer_id = utho_nodebalancer.example-nodebalancer.id
    port = 80
    protocol = "http"
    check = "http_body"
    check_path = "/healthcheck/"
    check_body = "healthcheck"
    check_attempts = 30
    check_timeout = 25
    check_interval = 30
    stickiness = "http_cookie"
    algorithm = "roundrobin"
    }

    ...
    {{< /file >}}

The NodeBalancer Config resource requires a NodeBalancer ID, which is provided using a variable `utho_nodebalancer.example-nodebalancer.id`. Since the `nodebalancer_id` argument refers to a NodeBalancer that hasn't been created yet, you can use this variable as a temporary placeholder. Terraform will know to create the NodeBalancer resource first before creating any other resources that depend on it. This allows you to design complex infrastructure that references its own components without worrying about the order of appearance in the Terraform configuration or whether the resources already exist.

In terms of settings, the health check is configured as `http_body`, which means it checks for a specific string defined by `check_body` within the body of the page specified at `check_path`. After 30 consecutive failed checks, a node will be taken out of rotation. Each check waits up to 25 seconds for a response before being considered a failure, with 30 seconds between checks. Additionally, session stickiness is enabled using `http_cookie`, ensuring users are directed to the same server via a session cookie. The load balancing algorithm is set to `roundrobin`, distributing users evenly across backend nodes based on the last server accessed.

{{< note >}}
Refer to the [NodeBalancer Reference Guide](/docs/products/networking/nodebalancers/guides/configure/) for a comprehensive list of NodeBalancer configuration options.
{{< /note >}}

### Generating NodeBalancer Node Resources
The next step in configuring a NodeBalancer using Terraform is to create the NodeBalancer Node resource. This resource holds details about each individual node and how they relate to the NodeBalancer and NodeBalancer Configuration resources.

    {{< file "nodebalancer.tf" >}}
    ...

    resource "utho_nodebalancer_node" "example-nodebalancer-node" {
    count = var.node_count
    nodebalancer_id = utho_nodebalancer.example-nodebalancer.id
    config_id = utho_nodebalancer_config.example-nodebalancer-config.id
    label = "example-node-${count.index + 1}"
    address = "${element(utho_instance.example-instance.*.private_ip_address, count.index)}:80"
    mode = "accept"
    }

    ...
    {{< /file >}}

Configuring the count argument of this resource will use the `node_count` input variable, defined later in this guide, to determine the number of Nodes to provision.

When provisioning multiple nodes, Terraform enters a loop where the node creation step is repeated. To keep track of the iteration in this loop, utilize the `count.index` variable. By using `{count.index + 1}` in the node's label argument, you ensure sequential labeling of each node, starting from one.

`utho_instance.example-instance.*.private_ip_address` refers to the private IP addresses of the Utho instances yet to be created. These Utho Instance resources will be labeled example-instance in the next step. Terraform can access certain attributes for each resource it creates, including `private_ip_address`. Since two Utho instances will be created simultaneously, Terraform groups these attributes in a list. The `element()` function allows retrieving a single item from the list using `count.index`. Thus, the first `NodeBalancer` Node references the private IP address of the first Utho instance, and the second NodeBalancer Node references the private IP address of the second instance.

### Setting up a Utho Instance Resource

Now that the NodeBalancer is configured, you'll need to define a Utho Instance resource. This resource helps Terraform know which instances to create to meet our needs NodeBalancer example.

    {{< file "nodebalancer.tf" >}}
    ...

    resource "utho_instance" "example-instance" {
    count  = var.node_count
    label  = "example-instance-${count.index + 1}"
    group = "nodebalancer-example"
    tags = ["nodebalancer-example"]
    region = var.region
    type = "g6-nanode-1"
    image = "utho/ubuntu18.10"
    authorized_keys = [chomp(file(var.ssh_key))]
    root_pass = random_string.password.result
    private_ip = true

    provisioner "remote-exec" {
        inline = [
            # install NGINX
            "export PATH=$PATH:/usr/bin",

            "apt-get -q update",
            "mkdir -p /var/www/html/",
            "mkdir -p /var/www/html/healthcheck",
            "echo healthcheck > /var/www/html/healthcheck/index.html",
            "echo node ${count.index + 1} > /var/www/html/index.html",
            "apt-get -q -y install nginx",
        ]

        connection {
            type = "ssh"
            user = "root"
            password = random_string.password.result
            host = self.ip_address
           }
          }
        }
    ...
     {{< /file >}}

The resource uses the same count argument as the NodeBalancer Node configured earlier. Additionally, the label argument is incremented sequentially, similar to the NodeBalancer Node.

For the `authorized_keys` argument, an SSH key input variable will be defined later in this guide. This variable is passed to the `file() function`, which reads the contents of a file into a string. The `chomp() function` is then applied to strip any extra whitespace.

The `root_pass` argument receives the result of the `random_string` resource that will be defined later in this guide.

In the Utho Instance resource, the remote-exec Provisioner block is noteworthy. This block contains two components: the inline list and the connection block. The inline list comprises commands that Terraform executes on the Utho instance after it boots. In this example, these commands include updating the Utho instance, creating the necessary directory structure for NGINX, generating the health check file and a generic index.html file, and installing NGINX.

The connection block instructs Terraform on how to access the Utho instance to execute the commands specified in the inline list. Here, Terraform gains SSH access, logging in as the root user.

### Creating an Output

To complete the `nodebalancer.tf` setup, you'll add an output. Terraform will showcase this information in the terminal's output. Outputs can feature any details from your configuration that you want to make visible. Here's an example that reveals the public IP address of the NodeBalancer:

    {{< file "nodebalancer.tf" >}}
    ...

    output "nodebalancer_ip_address" {
    value = utho_nodebalancer.example-nodebalancer.ipv4
    }
    {{< /file >}}

## Define Terraform Variables

Let's set up all the necessary variables for your Terraform configuration in a file named `variables.tf`.

1.  Create a file named `variables.tf`. This file will define the variables referenced in the configuration of your NodeBalancer and Nodes. You'll provide values for these variables in the next step.

        {{< file "variables.tf" >}}
        variable "token" {
        description = "Your APIv4 Access Token"
        }

        variable "region" {
        description = "The data center where your NodeBalancer and Nodes will reside. E.g., 'us-east'."
        default = "us-west"
        }

        variable "node_count" {
        description = "The amount of backend Nodes to create."
        }

        variable "ssh_key" {
        description = "The local file location of the SSH key that will be transferred to each Utho."
        default = "~/.ssh/id_rsa.pub"
        }

        resource "random_string" "password" {
        length = 32
        special = true
        upper = true
        lower = true
        number = true
        }
        {{< /file >}}

In Terraform, each variable can have its own description and default value assigned. These variables will receive their values from a `terraform.tfvars` file, which you'll create in the following step. Keeping the variable definitions separate from their values helps to safeguard sensitive data from entering your Terraform code, especially if you're using version control systems like Git.

In addition to the variables defined earlier, there's a `random_string` resource labeled `password`. This resource, provided by the [Random provider](https://www.terraform.io/docs/providers/random/index.html), generates a random string of 32 characters, including uppercase letters, lowercase letters, and numbers. This string serves as the root password for your backend Nodes. If you prefer precise control over your passwords, you can define one here in `variables.tf` and specify its value in `terraform.tfvars` in the next step.

1.  Create the `terraform.tfvars` file and assign values to the token, region, and `node_count` variables. In this example, we'll use the us-east data center, and set `node_count` to two.

        {{< file "terraform.tfvars" >}}
        token = "your_api_token"
        region = "us-east"
        node_count = 2
        {{< /file >}}

When Terraform runs, it searches for a file named `terraform.tfvars`, or files with the extension *.auto.tfvars, to fill in the Terraform variables with those values. If your SSH key is located in a different directory than the default location (`~/.ssh/id_rsa.pub`, for example), you'll need to include that value in the `terraform.tfvars` file.

{{< note respectIndent=false >}}
If you prefer to use the default value of an input variable defined in the variables.tf file, you can leave that variable blank in the `terraform.tfvars` file.
{{< /note >}}

Feel free to modify any values in the `terraform.tfvars` file according to your preferences. To obtain a list of data center IDs, you can use the cURL command to query the API.

        curl https://api.utho.com/v4/regions

## Initializing, Planning, and Applying the Terraform State

Since this guide utilizes providers Utho that you may not have installed in your local development environment, you'll need to run the `init` command to install them.

    terraform init

Once Terraform has been successfully initialized, you'll receive a confirmation message.

To review the plan of action outlined in the `nodebalancer.tf` file, use the plan command:

    terraform plan

After executing the command, you'll see a detailed output displaying all the `create` actions that will occur. Take note that any `<computed>` values you encounter will be defined during creation. Once you're satisfied with the proposed actions, proceed to apply them.

Run the `apply` command:

    terraform apply

When prompted to approve the `apply` action, type yes and press `Enter`. Terraform will then commence creating the resources configured in the previous steps. This process may take a few minutes, during which you'll start seeing the output of the `remote-exec` commands you specified in your Utho instance resource. Once all actions are completed, you should see an output similar to the following:

    {{< output >}}
    Apply complete! Resources: 7 added, 0 changed, 0 destroyed.

    Outputs:

    NodeBalancer IP Address = 104.237.148.131
    {{< /output >}}

Go to your NodeBalancer IP address and witness your NodeBalancer in action. Congratulations! You've successfully set up a NodeBalancer and backend nodes using Terraform.

### (Optional) Remove the NodeBalancer Resources

If you're finished with the resources you've just created, you can remove them using the destroy command.

    terraform destroy

This command will ask you to confirm the action before proceeding.
