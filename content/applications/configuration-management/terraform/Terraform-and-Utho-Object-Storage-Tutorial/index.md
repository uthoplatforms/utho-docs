---
slug: Terraform-and-Utho-Object-Storage-Tutorial
description: 'This tutorial offers a concise overview of Terraform, demonstrating its usage in configuring Utho Object Storage.'
keywords: ['Utho Terraform','Terraform Utho Object Storage','Install Terraform']
published: 2024-05-23
modified: 2024-05-23
modified_by:
  name: Utho
title: "Use Terraform With Utho Object Storage"
title_meta: "How to Use Terraform With Utho Object Storage"
authors: ["Pawan Kumar"]
tags: ["saas"]
---

This guide offers an introduction to Terraform, a robust Infrastructure as Code (IaC) tool for deploying and managing infrastructure. It explains how to utilize Terraform to configure Utho Object Storage solutions.

## Understanding Terraform

Terraform is a versatile open-source tool available in both free and commercial editions. Its configuration files are declarative, describing the desired end state of the system without specifying the procedural steps. Terraform files leverage either the HashiCorp Configuration Language (HCL) or JSON format to define infrastructure. Terraform promotes modularity and reusability, allowing users to manage resources using a provider-based system. Providers act as APIs, enabling the management of resources across various vendors. Terraform supports macOS, Windows, and most Linux distributions.

Providers, created in collaboration with infrastructure vendors, enable Terraform users to create, modify, and destroy network infrastructure. Utho offers a Terraform provider for managing Utho infrastructure. Users can explore a wide range of providers in the Terraform Registry.

Utho provides a comprehensive Beginner's Guide to Terraform for understanding its core concepts. Additionally, Terraform documentation includes tutorials covering popular providers.

## Using Terraform

To use Terraform, define the desired configuration of network elements in a .tf file. This file includes providers, data sources, and resource descriptions. Terraform configurations can utilize input variables, functions, and modules for flexibility and maintainability. Users develop configurations locally and apply them using the Terraform client. Before applying changes, users execute the terraform plan command to preview intended modifications.

Once satisfied with the plan, users apply changes using the terraform apply command. Terraform maintains an internal state file to track changes efficiently. Users can add new changes without deleting existing resources, and Terraform handles resource dependencies.

Terraform supports collaboration in multi-developer environments and allows users to create custom providers. For detailed information on Terraform, refer to the Introduction to Terraform summary.

Note: Terraform can be complex, and syntax errors may be challenging to debug. Before creating infrastructure, review Utho's Introduction to the HashiCorp Configuration Language and the Utho Provider documentation in the Terraform Registry. Utho offers extensive Terraform guides for additional examples and explanations.

## Before You Start

Create a Utho account and Compute Instance if not already done. Follow Utho's Getting Started guide and Creating a Compute Instance guide.

Secure your Compute Instance by following Utho's Setting Up and Securing a Compute Instance guide. Ensure system updates and configurations are completed.

Update all Utho servers, particularly Ubuntu systems, using the provided commands.

    ```command
    sudo apt update && sudo apt upgrade
    ```

Note: This guide is tailored for non-root users. Commands requiring elevated privileges are prefixed with sudo. If you're unfamiliar with the sudo command, refer to the Users and Groups guide.

## How to Download and Install Terraform

These instructions are specifically for Ubuntu 22.04 users but can generally apply to earlier Ubuntu releases. For other Linux distributions and macOS, refer to the Terraform Downloads Portal. Below is an example of how to download and install the latest Terraform release.

Begin by installing the system dependencies required for Terraform.

    ```command
    sudo apt install software-properties-common gnupg2 curl
    ```

Import the GPG key required for verification.

    ```command
    curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
    ```

Add the HashiCorp repository to apt.

    ```command
    sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
    ```
    
Download the updates for Terraform and proceed with the installation. This command installs the latest release of Terraform, which at the moment is version 1.3.4.

    ```command
    sudo apt update && sudo apt install terraform
    ```

    ```output
    Get:1 https://apt.releases.hashicorp.com jammy/main amd64 terraform amd64 1.3.4 [19.5 MB]
    Fetched 19.5 MB in 0s (210 MB/s)
    Selecting previously unselected package terraform.
    (Reading database ... 109186 files and directories currently installed.)
    Preparing to unpack .../terraform_1.3.4_amd64.deb ...
    Unpacking terraform (1.3.4) ...
    Setting up terraform (1.3.4) ...
    ```

Confirm the successful installation of Terraform by executing the terraform command without any parameters. Ensure that Terraform help information is displayed.

    ```command
    terraform
    ```

    ```output
    Usage: terraform [global options] <subcommand> [args]

Listing the available commands for execution. Common workflow commands are listed first, followed by less common or more advanced commands.

    Main commands:
    init          Prepare your working directory for other commands
    ...
    -version      An alias for the "version" subcommand.
    ```

To check the current release of Terraform, use the terraform -v command.

    ```command
    terraform -v
    ```

    ```output
    Terraform v1.3.4
    on linux_amd64
    ```

Create a directory for the new Terraform project and navigate to this directory.

    ```command
    mkdir ~/terraform
    cd ~/terraform
    ```

## Creating a Terraform File for Utho Object Storage Configuration

To deploy the required infrastructure for a Utho Object Storage solution, create a Terraform file that defines the desired state of the system. This file should include the following sections:

The terraform block, specifying the required providers. For this purpose, include only the Utho provider.

The Utho provider block.

The Utho_object_storage_cluster data source.

At least one Utho_object_storage_bucket resource, which provides a space to store files and text objects.

(Optional) A Utho_object_storage_key.

A list of Utho_object_storage_object items, representing text files or strings of text stored within a specific object storage bucket.

Follow these instructions to construct the Terraform file:

Create a file named Utho-terraform-storage.tf within the terraform directory.

    ```command
    nano Utho-terraform-storage.tf
    ```

Begin by adding a terraform section at the top of the file, specifying all required providers for the infrastructure. In this case, the only necessary provider is Utho, with the source set to Utho/Utho and the current version being 1.29.4. To determine the current version, refer to the Utho Namespace in the Terraform Registry.

    ```file {title="/terraform/Utho-terraform-storage.tf" lang="aconf"}

    terraform {
      required_providers {
        Utho = {
          source = "Utho/Utho"
          version = "1.29.4"
        }
      }
    }
    ```

Define the Utho provider, including the Utho v4 API token for the account. Refer to the Getting Started with the Utho API guide for more information about tokens.

Note: To maintain security, consider storing sensitive information such as API tokens in a separate variables.tf file and retrieve them using the var keyword. Review the Utho introduction to HCL for guidance on using variables..


    ```file {title="/terraform/Utho-terraform-storage.tf" lang="aconf" hl_lines="2" linenostart="10"}
    provider "Utho" {
      token = "THE_Utho_API_TOKEN"
    }
    ```

Create a Utho_object_storage_cluster data source. Name the new cluster object primary and specify a region for the cluster using the id attribute. For example, set the region to eu-central-1. This cluster object provides access to information such as domain, status, and region. Refer to the Terraform registry documentation for the Utho Object Storage Cluster data source for more details.

Note: Please note that storage clusters are not supported in all regions. For a comprehensive list of data centers where storage clusters can be configured, refer to the Utho Object Storage Product Information.

    ```file {title="/terraform/Utho-terraform-storage.tf" lang="aconf" linenostart="14"}
    data "Utho_object_storage_cluster" "primary" {
        id = "eu-central-1"
    }
    ```

Optional: Optionally, create a Utho_object_storage_key to manage access to storage objects. Provide a name and a label for the key for identification purposes.

    ```file {title="/terraform/Utho-terraform-storage.tf" lang="aconf" linenostart="18"}
    resource "Utho_object_storage_key" "storagekey" {
        label = "image-access"
    }
    ```

Create a Utho_object_storage_bucket resource. The cluster attribute for the bucket must contain the id of the cluster data source object. Retrieve the cluster identifier using the data.Utho_object_storage_cluster.primary.id attribute. Assign a unique label to the storage bucket.

Set the access_key and secret_key attributes to the access_key and secret_key fields of the storage key, if applicable.

Note: The Utho Object Storage Bucket resource contains many other configurable attributes. It is possible to set life cycle rules, versioning, and access control rules, and to associate the storage bucket with TLS/SSL certificates. For more information, see the [Utho Object Storage Bucket documentation](https://registry.terraform.io/providers/Utho/Utho/latest/docs/resources/object_storage_bucket) in the Terraform registry.

    ```file {title="/terraform/Utho-terraform-storage.tf" lang="aconf" linenostart="22"}
    resource "Utho_object_storage_bucket" "mybucket-j1145" {
      cluster = data.Utho_object_storage_cluster.primary.id
      label = "mybucket-j1145"
      access_key = Utho_object_storage_key.storagekey.access_key
      secret_key = Utho_object_storage_key.storagekey.secret_key
    }
    ```

To populate the storage bucket, create a Utho_object_storage_object resource. Specify the cluster and bucket where the object should be stored, along with a key to uniquely identify it within the cluster. If using a storage key, include the secret_key and access_key.

For adding a text file to storage, define the file path as the source attribute. Here's an example:

This example adds the file terraform_test.txt to the bucket mybucket-j1145 in the primary cluster. For more details on adding storage objects, refer to the Utho Storage Object resource documentation.

    ```file {title="/terraform/Utho-terraform-storage.tf" lang="aconf" linenostart="29"}
    resource "Utho_object_storage_object" "object1" {
        bucket  = Utho_object_storage_bucket.mybucket-j1145.label
        cluster = data.Utho_object_storage_cluster.primary.id
        key     = "textfile-object"

        secret_key = Utho_object_storage_key.storagekey.secret_key
        access_key = Utho_object_storage_key.storagekey.access_key

        source = pathexpand("~/terraform_test.txt")
    }
    ```

Optional: Additionally, the storage bucket can store text strings. To do so, declare a new Utho_object_storage_object and include the bucket, cluster, and storage key information. Choose a unique key for the text object and set the content attribute to the desired text string. Optionally, set content_type and content_language to reflect the nature of the text.

    ```file {title="/terraform/Utho-terraform-storage.tf" lang="aconf" linenostart="40"}
    resource "Utho_object_storage_object" "object2" {
        bucket  = Utho_object_storage_bucket.mybucket-j1145.label
        cluster = data.Utho_object_storage_cluster.primary.id
        key     = "freetext-object"

        secret_key = Utho_object_storage_key.storagekey.secret_key
        access_key = Utho_object_storage_key.storagekey.access_key

        content          = "This is the content of the Object..."
        content_type     = "text/plain"
        content_language = "en"
    }
    ```

Once all sections are added, the .tf file should resemble the provided example.

    ```file {title="/terraform/Utho-terraform-storage.tf" lang="aconf" hl_lines="11"}
    terraform {
      required_providers {
        Utho = {
          source = "Utho/Utho"
          version = "1.29.4"
        }
      }
    }

    provider "Utho" {
      token = "THE_Utho_API_TOKEN"
    }

    data "Utho_object_storage_cluster" "primary" {
        id = "eu-central-1"
    }

    resource "Utho_object_storage_key" "storagekey" {
        label = "image-access"
    }

    resource "Utho_object_storage_bucket" "mybucket-j1145" {
      cluster = data.Utho_object_storage_cluster.primary.id
      label = "mybucket-j1145"
      access_key = Utho_object_storage_key.storagekey.access_key
      secret_key = Utho_object_storage_key.storagekey.secret_key
    }

    resource "Utho_object_storage_object" "object1" {
        bucket  = Utho_object_storage_bucket.mybucket-j1145.label
        cluster = data.Utho_object_storage_cluster.primary.id
        key     = "textfile-object"

        secret_key = Utho_object_storage_key.storagekey.secret_key
        access_key = Utho_object_storage_key.storagekey.access_key

        source = pathexpand("~/terraform_test.txt")
    }

    resource "Utho_object_storage_object" "object2" {
        bucket  = Utho_object_storage_bucket.mybucket-j1145.label
        cluster = data.Utho_object_storage_cluster.primary.id
        key     = "freetext-object"

        secret_key = Utho_object_storage_key.storagekey.secret_key
        access_key = Utho_object_storage_key.storagekey.access_key

        content          = "This is the content of the Object..."
        content_type     = "text/plain"
        content_language = "en"
    }
    ```

Upon completion, exit nano by pressing <kbd></kbd> + <kbd>X</kbd>, then <kbd>Y</kbd> to save, and finally <kbd>Enter</kbd> to confirm.

## Using Terraform to Configure Utho Object Storage

Terraform commands operate on the Utho-terraform-storage.tf file to analyze its contents and deploy the required infrastructure. To create the Utho object storage infrastructure defined in the file, follow these steps:

Initialize Terraform by running terraform init. Terraform confirms successful initialization.

    ```command
    terraform init
    ```

    ```output
    Initializing the backend...

    Initializing provider plugins...
    - Finding Utho/Utho versions matching "1.29.4"...
    - Installing Utho/Utho v1.29.4...
    - Installed Utho/Utho v1.29.4 (signed by a HashiCorp partner, key ID F4E6BBD0EA4FE463)
    ...
    Terraform has been successfully initialized!
    ...
    ```

    Use the terraform plan command to get an overview of the planned infrastructure changes. This plan outlines the components Terraform will add, modify, or delete. Carefully review the output to ensure accuracy and verify that there are no unexpected changes. If needed, make adjustments to the .tf file and repeat the process.

    ```command
    terraform plan
    ```

    ```output
    data.Utho_object_storage_cluster.primary: Reading...
    data.Utho_object_storage_cluster.primary: Read complete after 0s [id=eu-central-1]

Terraform has used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbol:
      + create

Terraform will perform the following actions:

      # Utho_object_storage_bucket.mybucket-j1145 will be created
      + resource "Utho_object_storage_bucket" "mybucket-j1145" {
          + access_key   = (known after apply)
          + acl          = "private"
          + cluster      = "eu-central-1"
          + cors_enabled = true
          + hostname     = (known after apply)
          + id           = (known after apply)
          + label        = "mybucket-j1145"
          + secret_key   = (sensitive)
          + versioning   = (known after apply)
        }

      # Utho_object_storage_key.storagekey will be created
      + resource "Utho_object_storage_key" "storagekey" {
          + access_key = (known after apply)
          + id         = (known after apply)
          + label      = "image-access"
          + limited    = (known after apply)
          + secret_key = (sensitive value)
        }

      # Utho_object_storage_object.object1 will be created
      + resource "Utho_object_storage_object" "object1" {
          + access_key    = (known after apply)
          + acl           = "private"
          + bucket        = "mybucket-j1145"
          + cluster       = "eu-central-1"
          + content_type  = (known after apply)
          + etag          = (known after apply)
          + force_destroy = false
          + id            = (known after apply)
          + key           = "textfile-object"
          + secret_key    = (sensitive)
          + source        = "/home/username/terraform_test.txt"
          + version_id    = (known after apply)
        }

      # Utho_object_storage_object.object2 will be created
      + resource "Utho_object_storage_object" "object2" {
          + access_key       = (known after apply)
          + acl              = "private"
          + bucket           = "mybucket-j1145"
          + cluster          = "eu-central-1"
          + content          = "This is the content of the Object..."
          + content_language = "en"
          + content_type     = "text/plain"
          + etag             = (known after apply)
          + force_destroy    = false
          + id               = (known after apply)
          + key              = "freetext-object"
          + secret_key       = (sensitive)
          + version_id       = (known after apply)
        }

    Plan: 4 to add, 0 to change, 0 to destroy.
    ```

Once all desired changes have been made to the .tf file, deploy the changes by running terraform apply. If any errors occur, edit the .tf file, rerun terraform plan, and then terraform apply. Confirm the intended changes when prompted.

    ```command
    terraform apply
    ```

    ```output
    Plan: 4 to add, 0 to change, 0 to destroy.

      Do you want to perform these actions?
      Terraform will perform the actions described above.
      Only 'yes' will be accepted to approve.

      Enter a value:
    ```

Enter yes to proceed with the deployment. Terraform provides a summary of all changes and confirms completion of the operation. If any errors arise, address them in the .tf file and rerun the commands.

    ```command
    yes
    ```

    ```output
    Utho_object_storage_key.storagekey: Creating...
    Utho_object_storage_key.storagekey: Creation complete after 3s [id=367232]
    Utho_object_storage_bucket.mybucket-j145: Creating...
    Utho_object_storage_bucket.mybucket-j1145: Creation complete after 6s [id=eu-central-1:mybucket-j1145]
    Utho_object_storage_object.object1: Creating...
    Utho_object_storage_object.object2: Creating...
    Utho_object_storage_object.object1: Creation complete after 0s [id=mybucket-j1145/textfile-object]
    Utho_object_storage_object.object2: Creation complete after 0s [id=mybucket-j1145/freetext-object]

    Apply complete! Resources: 4 added, 0 changed, 0 destroyed.
    ```

  Check the Object Storage summary page on the Utho Dashboard to verify that all objects have been correctly created and configured. Click on the Object Storage Bucket name to view a list of objects within the bucket and download files and text objects as needed.

## Deleting and Editing the Utho Storage Objects

To remove the storage object configuration, use the terraform destroy command. This action deletes any objects listed in the Terraform files within the directory. For instance, running terraform destroy on Utho-terraform-storage.tf removes all storage clusters, buckets, keys, and storage objects. To delete only specific configurations, adjust the .tf file to include only the objects to delete, removing any objects that should be retained. Before executing the deletion, run terraform plan -destroy to preview the objects slated for removal.

```command
terraform plan -destroy
terraform destroy
```

To modify the contents of an object storage object, update the .tf file with the new configuration. After making changes, run terraform plan to review the proposed modifications, followed by terraform apply to implement them. Be cautious as this action may involve deleting and recreating objects instead of direct modification.

```command
terraform plan
terraform apply
```

## Conclusion

Terraform is a robust Infrastructure as Code (IaC) tool that automates infrastructure deployment. By describing the desired network state in HCL or JSON formats, Terraform facilitates infrastructure management. Use terraform plan to preview changes and terraform apply to enact them.
