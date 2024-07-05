---
slug: "How-to-use-Utho-with-packer"
description: "Packer, an open-source tool maintained by HashiCorp, simplifies the process of creating machine images. Here's a guide on how to use it."
keywords: ['packer hashicorp','hashicorp packer','image','machine image','immutable infrastructure','continuous delivery','ansible','ansible playbook','hashicorp terraform','hashicorp']
tags: ["automation"]
published: 2024-05-21
modified: 2024-05-21
modified_by:
  name: Utho
title: "Using the Utho Packer Builder to Create Custom Images"
title_meta: "How to Use the Utho Packer Builder"
authors: ["Utho"]
---

## Introduction to Packer

Packer is an open-source tool maintained by HashiCorp, designed to create machine images. These images include the operating system, applications, configurations, and data files that a virtual machine instance will run once deployed. Packer works seamlessly with configuration management tools like Chef, Puppet, or Ansible to install software on your Utho and incorporate those configurations into your image.

Packer uses templates to store configuration parameters for building images, standardizing the process and ensuring consistent image creation. This helps teams maintain an immutable infrastructure within a continuous delivery pipeline.

## The Utho Packer Builder

In Packer's ecosystem, builders are responsible for creating systems and generating images from them. Each builder is designed to create images for a specific platform.

The Utho builder integrates Packer with the Utho platform. It allows Packer to deploy a temporary Utho on your account using an APIv4 token, configure the system according to the template file parameters, and create an image from that Utho. This process enables you to automatically create Utho Images for rapid deployment of new Uthos.

## Before You Begin

This guide will walk you through installing Packer, creating a template file, building an image, and deploying that image onto a new Utho. Additionally, it covers using Ansible with Packer. Before you begin, ensure the following prerequisites:

Ensure you have cURL installed on your computer.

Generate a Utho API v4 access token with read/write permissions for both Uthos and Images. If you don't have one, follow the Get an Access Token section of the Getting Started with the Utho API guide.

Optional: Set a variable named TOKEN in your shell environment by running the following command, replacing x with your API token:

       export TOKEN=x

Some of the example commands in this guide use this variable. If you do not set it, you will need to modify these commands by replacing $TOKEN with your actual API token.

## Installing Packer

The following instructions will guide you through installing the latest version of Packer on macOS, Ubuntu, or CentOS. For additional installation methods, including instructions for other operating systems or compiling from source, refer to Packer's official documentation and the Download Packer page.

### Mac

To install Packer on macOS using Homebrew, run the following commands in your terminal:

              brew tap hashicorp/tap
              brew install hashicorp/tap/packer

### Ubuntu

               curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
               sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
               sudo apt-get update && sudo apt-get install packer

### CentOS/RHEL

               sudo yum install -y yum-utils
               sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
               sudo yum -y install packer

### Verifying the Installation

To verify that Packer was successfully installed, run the command packer --version. This should output the version number of the installed Packer. For reference, this guide was last tested using version 1.7.2.

## Constructing a Template for Packer

With Packer installed, you can create a Packer template. A template is a file containing the configurations needed to build a machine image. Templates can be formatted in either JSON or HCL2 (Hashicorp Configuration Language). As of Packer v1.7.0, the HCL2 template format is preferred and will be used in the examples within this guide.

Note: The steps in this section will incur charges related to deploying a 1GB Utho. The Utho will be deployed only for the time needed to create and snapshot your image and will then be deleted. For details on hourly billing, see our Billing and Payments guide.

### Creating the Template File

Create a file named example.pkr.hcl. You can store the file anywhere, but it's recommended to create a folder called packer in your home directory to organize your template files. Edit the file and input the following content:

                           {{< file "~/packer/example.pkr.hcl">}}
                            variable "Utho_api_token" {
                            type    = string
                            default = ""
                            }

                           source "Utho" "example" {
                           image             = "Utho/debian10"
                           image_description = "This image was created using Packer."
                           image_label       = "packer-debian-10"
                           instance_label    = "temp-packer-debian-10"
                           instance_type     = "g6-1"
                           Utho_token      = "${var.Utho_api_token}"
                           region            = "us-east"
                           ssh_username      = "root"
                           }

                           build {
                           sources = ["source.Utho.example"]
                           }
                           {{</ file >}}

### Understanding Template Blocks

An HCL2 formatted template file generally consists of various blocks. Each block can define a variable, specify parameters for a builder plugin, or outline actions to take during the template build process. Here are a few essential blocks commonly used in most templates:

- [Variable Block](#variable-block)
- [Source Block](#source-block-the-Utho-builder)
- [Build Block](#build-block)

### Variable Block

A variable block defines a user-defined variable along with any additional parameters or default values. Multiple variable blocks can be included in a template to define multiple variables. The value of a variable can be referenced elsewhere in the template using the syntax ${var.variable_name}, where variable_name is the name assigned to the variable.

In this example, there is only one variable: Utho_api_token. The value of this variable is intentionally left blank (default = ""). Instead of setting the variable within the template, it will be set via the command-line when executing the packer build command: packer build -var Utho_api_token=x example.pkr.hcl, where x represents your API token.

For more information about Packer template variables, refer to the Variables page in Packer's documentation.

### Source Block (the Utho Builder)

A source block specifies the parameters for a builder plugin. These sources are then utilized within the build block. Templates can include multiple sources, enabling the creation of multiple images for various platforms. Refer to the Builders page in Packer's documentation for a comprehensive list of available builders.

The initial line of each source block contains the builder plugin to be employed, along with the name assigned to this particular source in the template. For example, the starting line for the source in this instance (source "Utho" "example" {) indicates that the Utho builder will be used, and this source will be named "example".

#### Parameters for the Utho Builder

In this example, the Utho Packer builder is utilized as a source. Each parameter within the source block is detailed on the Utho Builder page in Packer's documentation.

image: This parameter specifies the ID of the "starter" image to be used. It can refer to any of the official Utho images or any private custom images within your account. In this instance, we'll use Utho/debian10 to denote the official Utho Debian 10 image. To view all available images, you can use the following curl command:

             curl -H "Authorization: Bearer $TOKEN" https://api.Utho.com/v4/images

`image_label` (optional): This parameter defines the label for the new custom image generated by this template.
`image_description` (optional): It provides a brief description for the new custom image.
`instance_label` (optional): This parameter sets the label for the temporary Utho that Packer will deploy to create the new image.
`instance_type`: This parameter specifies the ID of the instance type for the temporary Utho. The template uses `g6-1`, representing a 1GB Utho. In most scenarios. To view all available Utho types, you can use the following curl command:

                     curl https://api.Utho.com/v4/Utho/types

- `Utho_token`: The Utho API Personal Access Token used to grant Packer access to your account. It is set to ${var.Utho_api_token} in the template, as we intend to provide this token via a command-line variable instead of embedding it directly into the template.
- `region`: The ID of the data center. This template utilizes the ID for the Newark data center: us-east. To view all available regions, you can use the following curl command:

                      curl https://api.Utho.com/v4/regions

### Build Block

The build block instructs Packer on the actions to take during the template build process. It typically references a source and can optionally specify a provisioner or post-processor.

## Building the Image

Once you've saved the template file with your desired parameters, you're prepared to build the image.

Begin by validating the template with the packer validate command provided below. If you haven't set TOKEN as a variable in your shell environment, replace $TOKEN with your Utho API token. If successful, no errors will be reported.

                   packer validate -var Utho_api_token=$TOKEN example.pkr.hcl

Note: To learn how to securely store and use your API v4 token, see the [Vault](https://www.packer.io/docs/templates/hcl_templates/functions/contextual/vault) section of Packer's documentation.

Proceed to build the image by executing the packer build command provided below. As in the previous step, if you haven't set TOKEN as a variable in your shell environment, substitute $TOKEN with your Utho API token. This process may require several minutes to finish.

                   packer build -var Utho_api_token=$TOKEN example.pkr.hcl

Upon execution, this command will display a detailed log of each step performed by Packer. Upon completion, the final line will furnish you with the ID of the newly created custom image.

## Deploying a Utho with the New Image

Once the Packer build process is complete, a new Custom Image will be added to your account. You can deploy this image in several ways:

Cloud Manager: Utilize the Cloud Manager to deploy a Custom Image by following the instructions in the Deploy an Image to a New Compute Instance guide.

Utho CLI: Utilize the Utho CLI via the command-line interface by following the steps outlined in the Using the Utho CLI guide. Refer to the Utho Instances guide for example commands. The command below deploys a new Utho in the Newark data center. Ensure to replace mypassword with your desired root password and Utho/debian10 with the ID of your new image.

                     Utho-cli Uthos create --root_pass mypassword --region us-east --image Utho/debian10

- Utho APIv4: Employ the Utho API to programmatically create a new Utho by consulting the documentation provided under API > Utho Instances > Utho Create. The following curl command example will deploy a 1GB Utho to the Newark data center. Ensure to replace any required parameters, such as substituting Utho/debian10 with the ID of your Custom Image and assigning your preferred root_pass and label.

                        curl -H "Content-Type: application/json" \
                        -H "Authorization: Bearer $TOKEN" \
                        -X POST -d '{
                         "image": "private/7550080",
                         "root_pass": "aComplexP@ssword",
                         "booted": true,
                         "label": "my-example-label",
                         "type": "g6-1",
                         "region": "us-east"
                           }' \

## Going Further with Ansible

Packer is a highly versatile and customizable tool for image creation. While the initial template outlined in this guide serves as a minimalist example, it only scratches the surface of Packer's capabilities. To delve deeper, this section will explore integrating Packer with Ansible. Ansible stands as one of several options for customizing an image within Packer.

Ansible serves as an automation tool for server provisioning, configuration, and management. Before proceeding, it's recommended to follow the Getting Started With Ansible - Basic Installation and Setup guide to install Ansible and gain familiarity with fundamental Ansible concepts.

### Creating the Ansible Playbook

An Ansible playbook delineates the tasks and scripts executed during server provisioning. You'll craft a playbook that establishes a restricted user account on the Utho prior to Packer creating the ultimate image.

Utilize the mkpasswd utility (commonly available on various Linux systems) to generate a hashed password. This hash will be utilized in configuring Ansible's user module for your restricted user account.

                         mkpasswd --method=sha-512

You'll be prompted to input a plain-text password, and the utility will provide a hash of the password.

Create the playbook file with the following content. Replace username with your desired username and substitute password with the password hash generated in the previous step.

                   {{< file "~/packer/limited_user_account.yml">}}
                   ---
                   - hosts: all
                   remote_user: root
                   vars:
                   NORMAL_USER_NAME: 'username'
                   tasks:
                      - name: "Create a secondary, non-root user"
                        user: name={{ NORMAL_USER_NAME }}
                        password='$6$eebkauNy4h$peyyL1MTN7F4JKG44R27TTmbXlloDUsjPir/ATJue2bL0u8FBk0VuUvrpsMq6rSSOCm8VSip0QHN8bDaD/M/k/'
                        shell=/bin/bash
                      - name: Add remote authorized key to allow future passwordless logins
                        authorized_key: user={{ NORMAL_USER_NAME }} key="{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
                      - name: Add normal user to sudoers
                        lineinfile: dest=/etc/sudoers
                        regexp="{{ NORMAL_USER_NAME }} ALL"
                        line="{{ NORMAL_USER_NAME }}"
                       {{</ file >}}

This playbook will additionally incorporate the public SSH key stored on your local machine. If your desired public key is located elsewhere rather than ~/.ssh/id_rsa.pub, you can modify that value accordingly. Lastly, the playbook includes the new system user in the sudoers file.

### Modifying or Creating a New Template File

Edit your existing template file or create a new template file with the following content. Specifically, you'll insert a provisioner block within the build block, specifying ansible as the provisioner type, and providing the path to the playbook file you created.

                     {{< file "~/packer/ansible-example.pkr.hcl">}}
                       variable "Utho_api_token" {
                       type    = string
                       default = ""
                       }

                         source "Utho" "ansible-example" {
                         image             = "Utho/debian10"
                         image_description = "This image was created using Packer."
                         image_label       = "packer-advanced-debian-10"
                         instance_label    = "temp-packer-debian-10"
                         instance_type     = "g6-1"
                         Utho_token      = "${var.Utho_api_token}"
                         region            = "us-east"
                         ssh_username      = "root"
                         }

                        build {
                        sources = ["source.Utho.ansible-example"]

                        provisioner "ansible" {
                        playbook_file = "./limited_user_account.yml"
                        }
                        }
                       {{</ file >}}

### Understanding the Provisioner Block

A provisioner enables you to further configure your system by performing common system administration tasks such as adding users, installing and configuring software, and more. In this example, Packer's built-in Ansible provider is utilized to execute the tasks defined in the local limited_user_account.yml playbook. Consequently, your Utho image will include anything executed by the playbook. Packer supports several other provisioners, including Chef, Salt, and shell scripts. To learn more about Packer provisioners, refer to the Provisioner page in Packer's documentation.

### Building and Deploying the Image

Proceed with the steps outlined in the previous sections for building the image and deploying the image. After deploying a new Utho using the freshly created image, you should be able to access that Utho via SSH by executing the following command. Make sure to substitute username with the username you specified in the Ansible playbook and replace 192.0.2.1 with the IPv4 address of your new Utho.
