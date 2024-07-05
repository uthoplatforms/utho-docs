---
slug: 'Building a Multi-Cloud Infrastructure with Terraform'
description: 'This guide demonstrates how to utilize Terraform for Multi-Cloud environments, enabling a streamlined workflow to manage infrastructure using minimal configuration files'
keywords: ['Terraform','Utho','IaC','multicloud', 'automation', 'cloud manager']
tags: ['terraform','ubuntu', 'ssh', 'security']
published: 2024-06-13
modified_by:
  name: Utho
title: "Creating a Multicloud Infrastructure Using Terraform"
title_meta: "How to Create a Multicloud Infrastructure Using Terraform"
authors: ["Pawan Kumar"]
---

Terraform is an open-source tool developed by HashiCorp that utilizes the HashiCorp Configuration Language (HCL) to automate the deployment and provisioning of infrastructure resources.

With Terraform, you can manage your infrastructure lifecycle — including building, updating, and deleting resources — using just a few configuration files. This approach, known as Infrastructure as Code (IaC), revolutionizes how infrastructure is managed by treating infrastructure configurations as code.

This guide illustrates how to leverage Terraform and HCL to define and deploy a multicloud environment spanning services from Utho and another cloud provider.

## What is Infrastructure as Code (IaC)?

Infrastructure as Code refers to the practice of managing and provisioning computing infrastructure through machine-readable files, rather than through physical hardware configuration or interactive configuration tools.

Key Advantages of IaC:

- Reliability: Ensures that your infrastructure is configured exactly as specified in your code.

- Agility: Reduces manual work, eliminates configuration errors, and ensures consistency across environments.

- Speed & Efficiency: Enables rapid deployment of cloud services and infrastructure components using configuration files.

- Reusability: Configuration files can be reused across different environments and projects.

- Collaboration: Facilitates version control and collaboration among team members for managing infrastructure configurations.

- Risk Reduction: Provides a cost-effective disaster recovery strategy by enabling reproducible infrastructure deployments.

- By adopting Terraform and Infrastructure as Code principles, organizations can achieve greater operational efficiency, agility, and reliability in managing complex cloud environments.

## The Benefits of a Multicloud Terraform Environment

Cost-effective infrastructure: Construct your infrastructure by leveraging the most economical services offered by various cloud providers.

Consistency and fault tolerance: Deploy identical infrastructures across different cloud providers or local data centers. Developers can utilize consistent tools and configuration files to manage resources across multiple cloud platforms simultaneously. With Terraform, a single command can orchestrate deployments across multiple providers and manage cross-cloud dependencies seamlessly.

## Before You Begin

If you haven't already, create a Utho account and set up a Compute Instance.

Follow our Setting Up and Securing a Compute Instance guide to configure your system. This includes setting the timezone, configuring your hostname, creating a limited user account, and securing SSH access.

Obtain a personal access token for Utho’s API v4 to use with Terraform and the Terraform Utho Provider. Follow the instructions in Getting Started with the Utho API to generate a token.

{{< note >}}
This guide assumes you are using a non-root user account. Commands that require elevated privileges are prefixed with sudo. If you are unfamiliar with the sudo command, refer to the Linux Users and Groups guide for more information.
{{< /note >}}

## Downloading Terraform on your Utho Server

To install Terraform on a Utho running Ubuntu 20.04, and similar Debian-based distributions, follow these steps. You can use your Utho server as the central point for managing your Terraform infrastructure, or alternatively, install Terraform on your local computer.

Here are the steps to download and install Terraform on your Utho server:

To install Terraform on a Utho running Ubuntu 20.04, and similar Debian-based distributions, follow these steps. You can use your Utho server as the central point for managing your Terraform infrastructure, or alternatively, install Terraform on your local computer.

Here are the steps to download and install Terraform on your Utho server:

       ssh username@192.0.2.0

Update the package list and upgrade all installed packages to their latest versions.

        sudo apt-get update
        sudo apt-get upgrade

Create a new directory for Terraform and navigate into this directory.

        mkdir terraform
        cd terraform

Download Terraform by using the wget command or visit Terraform's download page. This guide references Terraform version 0.15.0, the latest available version at the time of writing.

        wget https://releases.hashicorp.com/terraform/0.15.0/terraform_0.15.0_linux_amd64.zip

Note: For earlier versions of Terraform, visit the Terraform releases page.

Download the SHA256SUMS file and the checksum signature (SHA256SUMS.sig) file for the latest version of Terraform (0.15.0 in this case).

- SHA256 checksum file:

            wget https://releases.hashicorp.com/terraform/0.15.0/terraform_0.15.0_SHA256SUMS

- The checksum signature file:

            wget https://releases.hashicorp.com/terraform/0.15.0/terraform_0.15.0_SHA256SUMS.sig

### Verify the Terraform Download

Find HashiCorp's public GPG key on their Security page under the “Secure Communications” section. The key ID is 51852D87348FFC4C.

        gpg --recv-keys 51852D87348FFC4C

The following output indicates that the gpg key has been successfully imported.

      {{< output >}}
     gpg: directory '/root/.gnupg' created
     gpg: keybox '/root/.gnupg/pubring.kbx' created
     gpg: /root/.gnupg/trustdb.gpg: trustdb created
     gpg: key 51852D87348FFC4C: public key "HashiCorp Security <security@hashicorp.com>" imported
     gpg: Total number processed: 1
     gpg: imported: 1
     {{< /output >}}

Utilize gpg to verify the signature file using the exact filenames of the sig and SHA256 files.

          gpg --verify terraform_0.15.0_SHA256SUMS.sig terraform_0.15.0_SHA256SUMS

The following output confirms that the sig file has a valid signature from HashiCorp Security.

    {{< output >}}
     gpg: Signature made Wed Apr 14 15:41:39 2021 UTC
     gpg: using RSA key 91A6E7F85D05C65630BEF18951852D87348FFC4C
     gpg: Good signature from "HashiCorp Security <security@hashicorp.com>" [unknown]
     {{< /output >}}

Verify that the RSA key displayed in the output matches the fingerprint shown on the Terraform Security page. You can find the fingerprint in the "Secure Communications" section, alongside the GPG key.

Check the checksum of the zip archive. Ensure you use the exact filename SHA256SUMS for the command.

             sha256sum -c terraform_0.15.0_SHA256SUMS 2>&1 | grep OK

The sha256sum program shows the filename of the zip file along with its status. If the status is NOT OK, it indicates that the zip file is corrupt and needs to be redownloaded.

          {{< output >}}

           terraform_0.15.0_linux_amd64.zip: OK

           {{< /output >}}

### Installing and Configuring Terraform on the Utho Server

Extract terraform_*_linux_amd64.zip into your terraform directory.

        unzip terraform_0.15.0_linux_amd64.zip

Note: If you encounter an error indicating that unzip is not installed on your system, install the unzip package using the command sudo apt install unzip and retry the extraction.

Modify your ~/.profile to add the ~/terraform directory to your PATH. Afterward, reload the profile to apply the changes.

           echo 'export PATH="$PATH:$HOME/terraform" ' >> ~/.profile
           source ~/.profile

Verify the Terraform installation by executing the terraform command without any arguments. This will display Terraform's usage information along with a list of common commands.

            terraform

          {{< output >}}
       Usage: terraform [global options] <subcommand> [args]
       The available commands for execution are listed below.
       The primary workflow commands are given first, followed by
       less common or more advanced commands.

        Main commands:
        init Prepare your working directory for other commands
        validate Check whether the configuration is valid
        plan Show changes required by the current configuration
        apply Create or update infrastructure
        destroy Destroy previously-created infrastructure

All other commands:

        ...

       {{< /output >}}

## Define your Multicloud Infrastructure Using Terraform

Terraform relies on plugins known as Providers. Each provider introduces a collection of resource types or data sources managed by Terraform.

To use these providers, Terraform configurations must declare their dependencies explicitly. Terraform supports two types of configuration files: JSON and HashiCorp Configuration Language (HCL).

This guide utilizes the HCL format, identified by files ending with the .tf extension.

### Defining the Utho Infrastructure

These steps outline how to create a multicloud configuration that includes one Utho instance and one service from Amazon Web Services (AWS). This section utilizes the Terraform Utho Provider and the Amazon Web Services (AWS) Provider.

Create a file named Utho-terraform.tf within your terraform directory.

            cd terraform
            vi Utho-terraform.tf

Begin by adding a terraform block at the beginning of the file to specify the Utho Provider. Below this, declare the Utho provider itself. Inside the provider block, include the token declaration. If you haven't already obtained an API token, refer to Utho’s Getting Started with the Utho API guide for instructions on creating one.

              {{< file "~/terraform/Utho-terraform.tf" >}}

           terraform {
           required_providers {
           Utho = {
           source = "Utho/Utho"
           version = "1.16.0"
          }
         }
        }

# Utho Provider definition

             provider  "Utho" {
             token = var.token
             }
             {{< /file >}}

Define the Resources for Utho. Specifically, define a new Utho instance as the resource to be deployed. Proceed by setting values for all mandatory resource configurations.

                {{< file "~/terraform/Utho-terraform.tf" >}}

                    resource  "Utho_instance"  "terraform" {
                    image = "Utho/ubuntu20.04"
                    label = "Terraform-Example"
                    group = "Terraform"
                    region = "us-east"
                    type = "g6-standard-1"
                    authorized_keys = [ var.authorized_keys ]
                    root_pass = var.root_pass
                  }
                 {{< /file >}}

1. Below is the complete Utho-terraform.tf file, encompassing both the provider and resource sections:

Replace "your_api_token_here" with your Utho API token. Refer to the Utho API documentation for comprehensive lists of valid values for each configuration field, such as region.

                     {{< file "~/terraform/Utho-terraform.tf" >}}

                 terraform {
             required_providers {
                   Utho = {
                 source = "Utho/Utho"
                 version = "1.16.0"
                   }
                  }
                 }

# Utho Provider definition

              provider  "Utho" {
              token = var.token
              }

               resource  "Utho_instance"  "terraform" {
               image = "Utho/ubuntu20.04"
               label = "terraform-example"
               group = "terraform"
               region = "us-east"
               type = "g6-standard-1"
               authorized_keys = [ var.authorized_keys ]
               root_pass = var.root_pass
              }

            {{< /file >}}

Create another file named variables.tf within the same terraform directory. Declare all the variables used in Utho-terraform.tf as illustrated below.

            vi variables.tf

            {{< file "~/terraform/variables.tf">}}
             variable "token" {}
             variable "authorized_keys" {}
             variable "root_pass" {}
            {{< /file >}}

Create another file named variables.tf within the same terraform directory. Declare all the variables used in Utho-terraform.tf as illustrated below.

              vi terraform.tfvars

              {{< file "~/terraform/terraform.tfvars">}}

              token = "YOUR_Utho_API_TOKEN"
              authorized_keys = "YOUR_PUBLIC_SSH_KEY"
              root_pass ="YOUR_ROOT_PASSWORD"

             {{< /file >}}

Note: It could be beneficial to define variables for fields where each resource shares the same value. For instance, if all Uthos use the same image, define an image variable and assign var.image to the image parameter for each resource. This approach simplifies updating the image details across all instances.

### Defining the AWS Infrastructure

Prerequisites for Provisioning AWS Resources Using Terraform

- An AWS account
- An AWS secret key

The following example illustrates how to configure a database table in the DynamoDB service using Terraform. For detailed guidance on defining AWS resources with Terraform, refer to the AWS Provider documentation.

Follow these steps to define AWS resources:

Create a new file named aws-terraform.tf within your terraform directory.

Specify the AWS provider in this file. Include the AWS region for the resources and utilize variable references for aws_access_key and aws_secret_key.

              {{< file "~/terraform/aws-terraform.tf" >}}

## Initialize the AWS Provider

               provider "aws" {
               access_key = var.aws_access_key
               secret_key = var.aws_secret_key
               region = "eu-west-2"
                              }
               {{< /file >}}

  Include a declaration for the AWS resource. The complete file, which also includes the provider configuration, is displayed below. This aws_dynamodb_table resource is used to configure a database table within the DynamoDB service.

                {{< file "~/terraform/aws-terraform.tf" >}}

# Initialize the AWS Provider

             provider "aws" {
             access_key = var.aws_access_key
             secret_key = var.aws_secret_key
             region = "eu-west-2"
                             }
             resource "aws_dynamodb_table" "inventory-dynamodb-table" {
             name           = "RecordInventory"
             billing_mode   = "PROVISIONED"
             read_capacity  = 20
             write_capacity = 20
             hash_key       = "ArtistName"
             range_key      = "AlbumTitle"

             attribute {
             name = "ArtistName"
             type = "S"
                             }

             attribute {
             name = "AlbumTitle"
             type = "S"
             }
           }
              {{< /file >}}

Update the variables.tf file by adding the new variables utilized in aws-terraform.tf to the end of the file, as demonstrated below.

Note: If numerous variables are employed across the configuration files, it's advisable to maintain a separate variables file for each cloud provider.

                     {{< file "~/terraform/variables.tf" >}}
                      ...
                     variable "aws_access_key" {}
                     variable "aws_secret_key" {}
                     {{< /file >}}

Modify the terraform.tfvars file and insert the real values of your AWS keys.

           {{< file "~/terraform/terraform.tfvars" aconf >}}
            ...
              aws_access_key = "YOUR_AWS_ACCESS_TOKEN"
              aws_secret_key = "YOUR_AWS_SSH_KEY"
             {{< /file >}}

## Initialize, Review Plan, and Execute Terraform

To deploy Terraform, you need to follow these three steps:

Start by initializing the Terraform directory. This step prepares Terraform to read variables and load essential providers.

Next, review the execution plan to understand the changes Terraform will apply to your infrastructure.

Finally, apply the Terraform plan to execute the desired changes.

This guide provides a detailed description of each of these steps.

          terraform init

The following output indicates that Terraform has been successfully initialized:

    {{< output >}}

      * Finding Utho/Utho versions matching "1.16.0"...
      * Finding latest version of hashicorp/aws...
      * Installing Utho/Utho v1.16.0...
      * Installed Utho/Utho v1.16.0 (signed by a HashiCorp partner, key ID F4E6BBD0EA4FE463)
      * Installing hashicorp/aws v3.34.0...
      * Installed hashicorp/aws v3.34.0 (signed by HashiCorp)
       ...

       Terraform has been successfully initialized!

       You may now begin working with Terraform. Try running "terraform plan" to see
       any changes that are required for your infrastructure. All Terraform commands
       should now work.
       ...
      {{< /output >}}

Note: If Terraform encounters an error, rerun the init command with debug mode enabled using TF_LOG=debug terraform init.

Execute the terraform plan command. This command lists the resources that Terraform intends to create, modify, or remove.
        terraform plan

       {{< output >}}
        An execution plan has been generated and is shown below.
        Resource actions are indicated with the following symbols:

        * create

Terraform will execute the following actions:

# aws_dynamodb_table.inventory-dynamodb-table will be created

       * resource "aws_dynamodb_table" "inventory-dynamodb-table" {
       * arn              = (known after apply)
       * billing_mode     = "PROVISIONED"
       * hash_key         = "ArtistName"
       * id               = (known after apply)
       * name             = "RecordInventory"
       * range_key        = "AlbumTitle"
       * read_capacity    = 20
       * stream_arn       = (known after apply)
       * stream_label     = (known after apply)
       * stream_view_type = (known after apply)
       * write_capacity   = 20

       * attribute {
       * name = "AlbumTitle"
       * type = "S"
                }
       * attribute {
       * name = "ArtistName"
       * type = "S"
                }

       * point_in_time_recovery {
       * enabled = (known after apply)
        }

       * server_side_encryption {
       * enabled     = (known after apply)
       * kms_key_arn = (known after apply)
        }
       }

# Utho_instance.terraform-example will be created

         * resource "Utho_instance" "terraform-example" {
         * backups            = (known after apply)
         * backups_enabled    = (known after apply)
         * boot_config_label  = (known after apply)
         * group              = "Terraform"
         * id                 = (known after apply)
         * image              = "Utho/ubuntu20.04"
         * ip_address         = (known after apply)
         * ipv4               = (known after apply)
         * ipv6               = (known after apply)
         * label              = "Terraform-Web-Example"
         * private_ip_address = (known after apply)
         * region             = "eu-west"
         * root_pass          = (sensitive value)
         * specs              = (known after apply)
         * status             = (known after apply)
         * swap_size          = (known after apply)
         * type               = "g6-standard-1"
         * watchdog_enabled   = true

         * alerts {
         * cpu            = (known after apply)
         * io             = (known after apply)
         * network_in     = (known after apply)
         * network_out    = (known after apply)
         * transfer_quota = (known after apply)
            }
            }

            Plan: 2 to add, 0 to change, 0 to destroy.
            {{< /output >}}

Running terraform plan will not take any action or implement changes on your Utho account.

Note: If the command produces an error or an unexpected outcome, rerun terraform plan in debug mode.

             TF_LOG=debug terraform plan

If there are no errors, proceed to deploy new resources using the apply command.

              terraform apply

Terraform will present the action plan once more and prompt for confirmation. Enter yes to proceed with the deployment.

After confirmation, Terraform will proceed to provision the resources according to its plan. Once complete, Terraform will summarize the results of configuring your multicloud infrastructure.

             {{< output >}}
             Plan: 2 to add, 0 to change, 0 to destroy.

             Do you want to perform these actions?
             Terraform will perform the actions described above.
             Only 'yes' will be accepted to approve.

             Enter a value: yes

             Utho_instance.terraform-web: Creating...
             alerts.#:           "" => "<computed>"
             authorized_keys.#:  "" => "1"
             authorized_keys.0:  "" => "ssh-rsa ..."
             backups.#:          "" => "<computed>"
             backups_enabled:    "" => "<computed>"
             boot_config_label:  "" => "<computed>"
             group:              "" => "Terraform"
             image:              "" => "Utho/ubuntu18.04"
             ip_address:         "" => "<computed>"
             ipv4.#:             "" => "<computed>"
             ipv6:               "" => "<computed>"
             label:              "" => "web"
             private_ip_address: "" => "<computed>"
             region:             "" => "us-east"
             root_pass:          "<sensitive>" => "<sensitive>"
             specs.#:            "" => "<computed>"
             status:             "" => "<computed>"
             swap_size:          "" => "<computed>"
             type:               "" => "g6-standard-1"
             watchdog_enabled:   "" => "true"
             Utho_instance.terraform-web: Still creating... (10s elapsed)
             Utho_instance.terraform-web: Still creating... (20s elapsed)
             Utho_instance.terraform-web: Still creating... (30s elapsed)
             Utho_instance.terraform-web: Still creating... (40s elapsed)
             Utho_instance.terraform-web: Still creating... (50s elapsed)
             Utho_instance.terraform-web: Creation complete after 52s (ID: 10975739)

              Apply complete! Resources: 1 added, 0 changed, 0 destroyed.

             {{< /output >}}

Note: Visit the Utho Cloud Manager to confirm that the terraform-example Utho has been successfully added to your account. Check the AWS Dashboard to verify the details of the newly created database.

Note: Terraform stores information about the state of your resources in the terraform.tfstate file located in the same directory.

## Update the Terraform Configuration

Terraform can modify individual resources without impacting other parts of your infrastructure. It manages configuration changes by comparing the current state of your infrastructure with the updated configuration files.

The following example demonstrates how to add a new Utho and modify an AWS database table simultaneously:

Edit the Utho-terraform.tf file and append the following snippet to define a new resource: a 1GB Utho running Ubuntu 20.04.

              {{< file "~/terraform/Utho-terraform.tf" aconf >}}
              ...
              resource "Utho_instance" "terraform2-example" {
              image = "Utho/ubuntu20.04"
              label = "terraform-web-example-2"
              group = "terraform"
              region = "eu-west"
              type = "g6-1"
              authorized_keys = [var.authorized_keys]
              root_pass = var.root_pass
              }
              {{< /file >}}

Modify the aws-terraform.tf file by editing one of the fields. For instance, update AlbumTitle to RecordTitle and adjust the range_key field name accordingly to match the new name.

           {{< file "~/terraform/aws-terraform.tf" aconf >}}
       ...
            range_key      = "RecordTitle"
        ...
            attribute {
            name = "RecordTitle"
            type = "S"
             }
         ...
              {{< /file >}}

Run the terraform plan command again.

        terraform plan

Terraform will generate a new plan that includes two additions and one deletion of your resources.

    {{< output >}}
        Utho_instance.terraform-example: Refreshing state... [id=25603885]
        aws_dynamodb_table.inventory-dynamodb-table: Refreshing state... [id=RecordInventory]

        An execution plan has been generated and is shown below.
        Resource actions are indicated with the following symbols:

        * create
        -/+ destroy and then create replacement

        Terraform will perform the following actions:

# aws_dynamodb_table.inventory-dynamodb-table must be replaced

        -/+ resource "aws_dynamodb_table" "inventory-dynamodb-table" {
        ~ arn              = "arn:aws:dynamodb:eu-west-2:836653344247:table/RecordInventory" -> (known after apply)
        ~ id               = "RecordInventory" -> (known after apply)
        name             = "RecordInventory"
        ~ range_key        = "AlbumTitle" -> "RecordTitle" # forces replacement
        + stream_arn       = (known after apply)
        - stream_enabled   = false -> null
        + stream_label     = (known after apply)
        + stream_view_type = (known after apply)
        - tags             = {} -> null
        # (4 unchanged attributes hidden)

          - attribute {
          - name = "AlbumTitle" -> null
          - type = "S" -> null
           }
           + attribute {
           + name = "RecordTitle"
           + type = "S"
            }

          ~ point_in_time_recovery {
          ~ enabled = false -> (known after apply)
             }

           + server_side_encryption {
           + enabled     = (known after apply)
           + kms_key_arn = (known after apply)
              }

            - ttl {
            - enabled = false -> null
            }
            # (1 unchanged block hidden)
            }

# Utho_instance.terraform2-example will be created

             * resource "Utho_instance" "terraform2-example" {
             * backups            = (known after apply)
             * backups_enabled    = (known after apply)
             * boot_config_label  = (known after apply)
             * group              = "Terraform"
             * id                 = (known after apply)
             * image              = "Utho/ubuntu18.04"
             * ip_address         = (known after apply)
             * ipv4               = (known after apply)
             * ipv6               = (known after apply)
             * label              = "Terraform-Web-Example-2"
             * private_ip_address = (known after apply)
             * region             = "eu-west"
             * root_pass          = (sensitive value)
             * specs              = (known after apply)
             * status             = (known after apply)
             * swap_size          = (known after apply)
             * type               = "g6-nanode-1"
             * watchdog_enabled   = true

             * alerts {
             * cpu            = (known after apply)
             * io             = (known after apply)
             * network_in     = (known after apply)
             * network_out    = (known after apply)
             * transfer_quota = (known after apply)
             }
             }

             Plan: 2 to add, 0 to change, 1 to destroy.
             {{< /output >}}

Once the plan is confirmed, execute the apply command to implement the changes. Enter yes to confirm and proceed with the deployment.

        terraform apply

         {{< output >}}

           Utho_instance.terraform2-example: Creating...
           aws_dynamodb_table.inventory-dynamodb-table: Destroying... [id=RecordInventory]
           aws_dynamodb_table.inventory-dynamodb-table: Destruction complete after 2s
           aws_dynamodb_table.inventory-dynamodb-table: Creating...
           aws_dynamodb_table.inventory-dynamodb-table: Creation complete after 7s [id=RecordInventory]
           Utho_instance.terraform2-example: Still creating... [10s elapsed]
           Utho_instance.terraform2-example: Still creating... [20s elapsed]
           Utho_instance.terraform2-example: Still creating... [30s elapsed]
           Utho_instance.terraform2-example: Still creating... [40s elapsed]
           Utho_instance.terraform2-example: Creation complete after 48s [id=25624349]

            Apply complete! Resources: 2 added, 0 changed, 1 destroyed.
            {{< /output >}}

Visit the Utho Cloud Manager and the AWS dashboard to ensure that the updates have been successfully implemented.

## Destroy the Terraform Configuration

Terraform provides a destroy command to completely remove resources from your infrastructure when they are no longer needed.

Execute the plan command with the -destroy flag to review the list of resources that will be deleted.

          terraform plan -destroy

          {{< output >}}

          An execution plan has been generated and is shown below.
          Resource actions are indicated with the following symbols:

          * destroy

Terraform will execute the following actions:

## aws_dynamodb_table.inventory-dynamodb-table will be destroyed

...

## Utho_instance.terraform-example will be destroyed

...

## Utho_instance.terraform2-example will be destroyed

...

       Plan: 0 to add, 0 to change, 3 to destroy.
       {{< /output >}}

Execute the terraform destroy command to delete your resources. Confirm by entering yes when prompted by Terraform.

        terraform destroy

Note: This action is irreversible and permanently deletes your resources.

        {{< output >}}

        Plan: 0 to add, 0 to change, 3 to destroy.

        Do you really want to destroy all resources?
        Terraform will destroy all your managed infrastructure, as shown above.
        There is no undo. Only 'yes' will be accepted to confirm.

        Enter a value: yes

        Utho_instance.terraform-example: Destroying... [id=25603885]
        Utho_instance.terraform2-example: Destroying... [id=25624349]
        aws_dynamodb_table.inventory-dynamodb-table: Destroying... [id=RecordInventory]
        aws_dynamodb_table.inventory-dynamodb-table: Destruction complete after 3s
        Utho_instance.terraform2-example: Destruction complete after 4s
        Utho_instance.terraform-example: Destruction complete after 4s

         Destroy complete! Resources: 3 destroyed.

          {{< /output >}}

Check the Utho Cloud Manager to ensure that the devices and services have been successfully removed.

## Learn More About Terraform

Terraform is revolutionizing the DevOps ecosystem by redefining how infrastructure is managed. It provides advanced techniques to simplify common Infrastructure as Code (IaC) tasks. For example, Terraform modules bundle frequently-used configuration tasks together.

Explore Utho's Guide to Creating a Terraform Module for instructions on module creation.
