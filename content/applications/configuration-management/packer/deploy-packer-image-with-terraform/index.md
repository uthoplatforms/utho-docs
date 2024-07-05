---
slug: "Streamline Your Deployment: Automate Packer Images with Terraform"
description: "Packer streamlines machine image development, while Terraform automates infrastructure provisioning. When these powerful tools join forces, they create a comprehensive solution for automating deployments, including CI/CD pipelines. In this tutorial, discover how to integrate Packer and Terraform effectively to automate your infrastructure deployment process."
keywords: ['packer terraform provider','terraform packer resource','utho packer']
published: 2024-03-26
modified: 2024-03-26
modified_by:
  name: Pawan Kumar
title: "Unlock Efficiency: Seamlessly Deploy Packer Images Using Terraform"
title_meta: "Master the Art: Deploying Packer Images with Terraform"
authors: ["Pawan Kumar"]
tags: ["saas"]
---

Both Packer and Terraform, developed by HashiCorp, excel in automating infrastructure tasks. While they share some similarities, each tool offers unique and complementary features. This synergy makes them a powerful combination, with Packer used to create images that Terraform deploys as a complete infrastructure.

If you're new to Terraform, check out our [Beginner's Guide to Terraform](/docs/guides/beginners-guide-to-terraform/).

In this tutorial, you'll learn how to integrate Packer and Terraform to deploy Utho instances. We'll use the Utho Terraform provider to deploy multiple instances based on a Utho image built with Packer.

## Before You Begin

1. If you haven't already, create a Utho account and set up a Compute Instance.

1. Follow our guide to update your system. You might also want to adjust the timezone, set up your hostname, create a restricted user account, and strengthen SSH access.

Note: This guide is designed for a non-root user. Commands that need elevated privileges are prefixed with `sudo`. If you're unsure about the `sudo` command, refer to the [Users and Groups](/docs/guides/linux-users-and-groups/) guide for more information.

## Installing the Required Components

To begin, install both Packer and Terraform on your system. Below are links to installation guides for both tools, along with steps that apply to most Linux operating systems.

### Installing Packer

Packer's installation steps differ based on your operating system. If your system isn't covered here, refer to the [official installation guide](https://learn.hashicorp.com/tutorials/packer/get-started-install-cli) for instructions.


```command {title="Debian / Ubuntu"}
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -\
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install packer
```

```command {title="AlmaLinux / CentOS Stream / Rocky Linux"}
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum -y install packer
```

```command {title="Fedora"}
sudo dnf install -y dnf-plugins-core
sudo dnf config-manager --add-repo https://rpm.releases.hashicorp.com/fedora/hashicorp.repo
sudo dnf -y install packer
```

Afterward, verify your installation and display the installed version with the following command:

```command
packer --version
```

```output
1.8.4
```

### Installing Terraform on Your System

Installing Terraform on different operating systems varies. For systems not covered here, consult HashiCorp's [official documentation](https://learn.hashicorp.com/tutorials/terraform/install-cli) for installing the Terraform CLI. You can also check the installation section in our guide Use Terraform to Provision Utho Environments.


```command {title="Debian / Ubuntu"}
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform
```

```command {title="AlmaLinux / CentOS Stream / Rocky Linux"}
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum -y install terraform
```

```command {title="Fedora"}
sudo dnf install -y dnf-plugins-core
sudo dnf config-manager --add-repo https://rpm.releases.hashicorp.com/fedora/hashicorp.repo
sudo dnf -y install terraform
```

Afterward, verify your installation with:

```command
terraform -version
```

```output
Terraform v1.3.3
on linux_amd64
```

## Creating a Packer Image

Packer automates the creation of machine images, which streamlines the process of provisioning infrastructure. These images provide a consistent foundation for deploying instances.

Using images is more efficient because instead of running a series of installations and commands for each provisioned instance, you can deploy pre-made images.

In this tutorial, we'll use a Utho image created with Packer. Utho provides a builder for Packer, allowing you to create images tailored for Utho instances.

To get started, follow our guide on Using the Utho Packer Builder to Create Custom Images. By the end, you'll have a Packer-built image in your Utho account.

The subsequent steps in this tutorial will work regardless of the type of image you created following the linked guide. However, the examples in this tutorial use a Packer image labeled packer-utho-image-1, based on Ubuntu 20.04, with NGINX installed.

## Configuring Terraform for Deployment

Terraform specializes in automating the provisioning process, enabling you to deploy your infrastructure entirely through code.

To explore deploying Utho instances with Terraform, check out our tutorial on Using Terraform to Provision Utho Environments.

This tutorial follows a similar sequence of steps but focuses specifically on working with custom Utho images.

Before proceeding, create a directory for your Terraform scripts and set it as your working directory. This tutorial uses the `utho-terraform` directory located in the current user's home directory:

```command
mkdir ~/utho-terraform
cd ~/utho-terraform
```

The remaining steps of the tutorial assume that you are operating from this directory.

### Configuring the Utho Provider

Terraform's providers serve as interfaces to APIs, enabling Terraform to interact with different resources on host platforms.

To utilize the provider, you only need to include a few simple blocks in a Terraform script.

Start by creating a new Terraform file named `packer-utho.tf`, which serves as the foundation for this tutorial's Terraform project.

```command
nano packer-utho.tf
```

Give it the contents shown here:

```file {title="packer-utho.tf"}
terraform {
  required_providers {
    utho = {
      source = "utho/utho"
      version = "1.29.3"
    }
  }
}
provider "utho" {
  token = var.token
}
```

The `terraform` block initiates the project and specifies its necessary providers, such as Utho. Then, the `provider` block initializes the Utho provider. The token argument enables the provider to authenticate its connection to the Utho API.

To exit nano, press <kbd>Ctrl</kbd> + <kbd>X</kbd>, then <kbd>Y</kbd> to save, and finally <kbd>Enter</kbd> to confirm.

### Defining Terraform Variables

In the example above, the `token` value for the Utho provider utilizes the    `var.token` variable. While not mandatory, variables significantly enhance the adaptability and organization of Terraform scripts.

This tutorial manages variables using two files.

1. Start by creating a file named `variables.tf`.

    ```command
    nano variables.tf
    ```

Now, populate it with the following contents. This file outlines all the variables for the Terraform project. Some variables come with default values, which Terraform utilizes automatically if not explicitly assigned. Others require assignment, as demonstrated in the subsequent file.

    ```file {title="variables.tf"}
    variable "token" {
      description = "The Utho API Personal Access Token."
    }
    variable "password" {
      description = "The root password for the Utho instances."
    }
    variable "ssh_key" {
      description = "The location of an SSH key file for use on the Utho instances."
      default = "~/.ssh/id_rsa.pub"
    }
    variable "node_count" {
      description = "The number of instances to create."
      default = 1
    }
    variable "region" {
      description = "The name of the region in which to deploy instances."
      default = "us-east"
    }
    variable "image_id" {
      description = "The ID for the Utho image to be used in provisioning the instances"
      default = "utho/ubuntu20.04"
    }
    ```

After editing, press <kbd>Ctrl</kbd> + <kbd>X</kbd> to exit nano, then press <kbd>Y</kbd> to save, and finally press <kbd>Enter</kbd> to confirm.

1. Now, create a file named `terraform.tfvars`.

    ```command
    nano terraform.tfvars
    ```

This file, ending with `.tfvars`, serves as a location for assigning variable values. Populate the file with the following contents, replacing the values in arrow brackets (`<...>`) with your actual values:

    ```file {title="terraform.tfvars"}
    token = "<UthoApiToken>"
    password = "<RootPassword>"
    node_count = 2
    image_id = "private/<UthoImageId>"
    ```

The `<UthoApiToken>` should be an API token associated with your Utho account. You can refer to our guide to generate a personal access token. Ensure that the token has "Read/Write" permissions.

In the example, you'll notice a value of `private/<UthoImageId>` for the image_id. This value should correspond to the image ID for the Utho image you created using Packer. All custom Utho images start with `private/` followed by the image's ID. For instance, in these examples, `private/17691867` is assumed to be the ID for the Utho image built with Packer.

You can obtain your image ID in two primary ways:

- The Utho image ID is located at the end of the output when you create the image using Packer. For example, in the guide linked above for creating a Utho image with Packer, you'll find the output:

        ```output
        ==> Builds finished. The artifacts of successful builds are:
        --> utho.example-utho-image: Utho image: packer-utho-image-1 (private/17691867)
        ```

- The Utho API provides an endpoint for listing available images. This list includes your custom images when you call it with your API token.

You can utilize a cURL command to list all images available to you, both public and private. Replace `$UTHO_API_TOKEN` with your Utho API token:

        ```command
        curl -H "Authorization: Bearer $LINODE_API_TOKEN" \https://api.utho.com/v4/images
        ```

The output can be overwhelming in the command line, so you might prefer using another tool to format the JSON response. This has been done with the result shown here:

        ```output
        {
            "pages": 1,
            "data": [{
                "id": "private/17691867",
                "label": "packer-utho-image-1",
                "description": "Example Packer Utho Image",
                // [...]
        ```

Once finished, press <kbd>Ctrl</kbd> + <kbd>X</kbd> to exit nano, <kbd>Y</kbd> to save, and <kbd>Enter</kbd> to confirm.

### Creating the Utho Resource

The next step in the Terraform script is to specify the actual resource to be provisioned. In this scenario, the script needs to provision Utho instances, which can be achieved using the `utho_instance` resource.

Open the `packer-utho.tf` file created earlier and append the details provided below to the end:

```file {title="packer-utho.tf" linenostart="14"}
resource "utho_instance" "packer_utho_instance" {
  count = var.node_count
  image = var.image_id
  label = "packer-image-utho-${count.index + 1}"
  group = "packer-image-instances"
  region = var.region
  type = "g6-standard-1"
  authorized_keys = [ chomp(file(var.ssh_key)) ]
  root_pass = var.password
  connection {
    type = "ssh"
    user = "root"
    password = var.password
    host = self.ip_address
  }
  provisioner "remote-exec" {
    inline = [
      # Update the system.
      "apt-get update -qq",
      # Disable password authentication; users can only connect with an SSH key.
      "sed -i '/PasswordAuthentication/d' /etc/ssh/sshd_config",
      "echo \"PasswordAuthentication no\" >> /etc/ssh/sshd_config",
      # Check to make sure NGINX is running.
      "systemctl status nginx --no-pager"
    ]
  }
}
```

Now, the Terraform project is set to provision two Utho instances based on your Packer-built image. Most of the configuration details for the `resource` block are handled by variables. Thus, you shouldn't need to tweak much within the `resource` block to adjust settings like the number of instances to provision.

The `remote-exec` provisioner, particularly the `inline` list within it, is where most of the customization occurs. This section defines shell commands to execute on the newly provisioned instance. Although the commands here are relatively simple, this provisioner offers precise control over operations on the instance.

## Deploying a Packer Image Using Terraform

From here, a few Terraform commands are all that's required to provision and manage Utho instances using the Packer-built image.

Initially, Terraform needs to execute some initialization tasks related to the script. This step installs any necessary prerequisites, such as the utho provider in this case, and configures Terraform's lock file.

```command
terraform init
```

Executing Terraform's `plan` command is a good habit. This command checks your script for any immediate errors and presents an overview of the resources that will be deployed. It's like a preliminary dry run.

```command
terraform plan
```

After reviewing the plan, proceed to provision your instances using the `apply` command. This process might take several minutes to complete, depending on your systems and the number of instances being deployed.

```command
terraform apply
```

```output
utho_instance.packer_utho_instance[0] (remote-exec): Connected!
utho_instance.packer_utho_instance[0] (remote-exec): ● nginx.service - A high performance web server and a reverse proxy server
utho_instance.packer_utho_instance[0] (remote-exec):      Loaded: loaded (/lib/systemd/system/nginx.service; enabled; vendor preset: enabled)
utho_instance.packer_utho_instance[0] (remote-exec):      Active: active (running) since Thu 2022-10-27 15:56:42 UTC; 9s ago
[...]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.
```

In the future, if you wish to remove the instances created with Terraform, you can utilize the `destroy` command from within your Terraform script directory.

```command
terraform destroy
```

Similar to the `apply` command, you'll receive a preview of the instances and will be prompted to confirm before the instances are destroyed.

## Conclusion

This tutorial demonstrated how to utilize Terraform for deploying Utho instances created with a Packer image. This approach offers an efficient solution for provisioning and overseeing Utho instances. Terraform simplifies infrastructure provisioning, particularly when incorporating pre-built images from Packer.

Although the example in this tutorial is straightforward, the setup can be easily customized and extended to deploy more intricate and comprehensive infrastructures.
