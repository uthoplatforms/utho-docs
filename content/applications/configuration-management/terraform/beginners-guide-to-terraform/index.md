---
slug: "Terraform Unleashed: Your Roadmap to Mastering the Basics"
description: 'Embark on Your Terraform Journey: Exploring Core Components, Key Features, and Configuration Essentials for Beginners'
keywords: ['terraform', 'orchestration', 'utho provider']
published: 2024-04-03
modified: 2024-04-03
modified_by:
  name: Utho
image: ABeginnersGuidetoTerraform.png
title: "A Beginner's Guide to Terraform"
authors: ["Utho"]
tags: ["saas"]
---

[Terraform](https://www.terraform.io) by HashiCorp is a handy tool for managing your Utho instances and other resources through code instead of manual processes. This approach, known as Infrastructure as Code, streamlines resource creation and management. Here's how Terraform works:

1. Write Configuration Files: Create files on your computer where you specify the infrastructure elements you want to build.

2. Apply Configuration: Let Terraform analyze your configurations and create the corresponding infrastructure accordingly.

Terraform focuses on creating, modifying, and removing servers and resources, but it typically doesn't handle software configuration. You can manage software configuration using scripts that you [upload to and execute on your new servers](#provisioners) and execute on your servers or through other tools like configuration management or container deployments.

## The Utho Provider

Terraform is like a conductor for your online stuff. It works with many types of clouds. These integrations are referred to as *providers*.

Terraform serves as a versatile orchestration tool capable of connecting with various cloud platforms through what it calls providers. Utho, a cloud platform, officially launched its Terraform provider in October 2018. This provider allows users to seamlessly integrate Utho's services with Terraform, enhancing flexibility and convenience in managing cloud resources.

{{< note >}}
To utilize the Utho provider, you'll need to access Utho's APIv4 and obtain an API access token. Detailed instructions on acquiring this token, along with installing Terraform and the Utho provider on your computer, can be found in the guide titled Use Terraform to Provision Utho Environments.
{{< /note >}}

The Utho provider facilitates the creation of various resources such as Utho instances, Images, domain records, Block Storage Volumes, and StackScripts. You can utilize Terraform's capabilities to manage these resources efficiently.

## Infrastructure as Code

Terraform simplifies managing your resources by representing them in configuration files, known as Infrastructure as Code (IAC). Here's why using Terraform and IAC is beneficial:

- **Version Control**: With resources declared in code, changes can be tracked over time using version control systems like Git.

- **Reduced Errors**: Terraform consistently produces the same results when creating resources from configuration files, minimizing human error. Additionally, repeating the application of the same configuration won't lead to duplicate resource creation, as Terraform tracks changes made over time.

- **Enhanced Collaboration**: Terraform's backends enable multiple team members to collaborate safely on the same configuration simultaneously.

### HashiCorp Configuration Language

Terraform allows configuration files to be written in either the [*HashiCorp Configuration Language*](https://github.com/hashicorp/hcl) (HCL) or JSON. HCL, developed by HashiCorp for its products, offers a balance between human readability and machine-friendliness. It's recommended to opt for HCL when deploying with Terraform.

The upcoming sections will demonstrate key Terraform concepts using examples written in HCL. For a comprehensive overview of HCL syntax, refer to [Introduction to HashiCorp Configuration Language (HCL)](/docs/guides/introduction-to-hcl/).

### Resources

Here's a basic example of a full setup using Terraform's configuration language (HCL).

    {{< file "example.tf" >}}
    terraform {
    required_providers {
    utho = {
    source = "utho/utho"
    version = "1.16.0"
    }
    }
    }

    provider "utho" {
    token = "your-utho-api-token"
    }

    resource "utho_instance" "example_instance" {
    label = "example_instance_label"
    image = "utho/ubuntu18.04"
    region = "us-central"
    type = "g6-standard-1"
    authorized_keys = ["ssh-rsa AAAA...Gw== user@example.local"]
    root_pass = "your-root-password"
    }
    {{< /file >}}

    {{< note >}}
For brevity, the SSH key in this example was shortened.
    {{< /note >}}

This example Terraform file, named with the `.tf` extension, demonstrates the creation of a single Utho instance named `example_instance_label`. It starts with a necessary `provider` block, setting up the Utho provider, which you need to include somewhere in your configuration.

Following the provider block is a resource declaration, which represents components of your Utho infrastructure like Utho instances or Block Storage Volumes.

Resources can have arguments. For the utho_instance resource, `region` and `type` are required arguments. While a root password is necessary for every Utho, the `root_pass` Terraform argument is optional; if not specified, a random password will be generated.

{{< note >}}
The `example_instance` string appearing after the utho_instance resource type declaration serves as Terraform's label for the resource. Remember, you can't have more than one Terraform resource with the same name and type.

The `label` argument sets the Utho instance's label in the Utho Manager. This name is separate from Terraform's resource name (although you can use the same value for both). The Terraform name is only stored in Terraform's state and isn't transmitted to the Utho API. Ensure that labels for Utho instances within the same Utho account are unique.
{{< /note >}}

### Data Sources
In Terraform, data sources serve as read-only values that can be fetched and utilized elsewhere in a configuration. By using the Utho Provider, users gain access to various Utho Specific Data Sources for this purpose.

To access data sources, you declare a `data` block containing necessary information. Once declared, the data source offers access to multiple attributes that can be used within the Terraform configuration. In the example below, the `utho_account` data source is invoked within the data block and later utilized in the `output` block to display the `email` attribute:

    {{< file "example.tf" >}}
    ...
    data "utho_account" "account" {}

    output "utho_account_email" {
    value = "${data.utho_account.account.email}"
    }
    {{< /file >}}

### Dependencies
In Terraform, resources can have dependencies on each other. When one resource depends on another, Terraform ensures that the dependent resource is created after the one it relies on, regardless of their order in the configuration file.

The following example extends the previous one. It defines a new domain along with an A record that points to the IP address of the Utho instance:

    {{< file "example.tf" >}}
    terraform {
    ...
    }

    provider "utho" {
    # ...
    }

    resource "utho_instance" "example_instance" {
    # ...
    }

    resource "utho_domain" "example_domain" {
    domain = "example.com"
    soa_email = "example@example.com"
    type = "master"
    }

    resource "utho_domain_record" "example_domain_record" {
    domain_id = utho_domain.example_domain.id
    name = "www"
    record_type = "A"
    target = utho_instance.example_instance.ip_address
    }
    {{< /file >}}

The `domain_id` and `target` arguments of the domain record utilize HCL's [interpolation syntax](/docs/guides/introduction-to-hcl/#interpolation) to fetch the ID of the domain resource and the IP address of the Utho instance, respectively. Terraform establishes an implicit dependency on the example_instance and `example_domain` resources for the `example_domain_record`. Consequently, the domain record won't be created until after both the Utho instance and the domain are created.

{{< note >}}
[Explicit dependencies](https://www.terraform.io/docs/configuration/resources.html#explicit-dependencies) You can also declare explicit dependencies.
{{< /note >}}

### Input Variables

In the previous example, sensitive data like your API token and root password were directly coded into your configuration. To enhance security, Terraform enables you to input values for your resource arguments using input variables. These variables are declared and referenced in your Terraform configuration using interpolation syntax, and their values are assigned in a separate file.

Input variables can also handle non-sensitive data. The following example demonstrates using variables for both sensitive (`token and root_pass`) and non-sensitive (`authorized_keys` and `region`) arguments:

    {{< file "example.tf" >}}
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

    resource "utho_instance" "example_instance" {
    label = "example_instance_label"
    image = "utho/ubuntu18.04"
    region = var.region
    type = "g6-standard-1"
    authorized_keys = [var.ssh_key]
    root_pass = var.root_pass
    }

    variable "token" {}
    variable "root_pass" {}
    variable "ssh_key" {}
    variable "region" {
    default = "us-southeast"
    }
    {{< /file >}}

    {{< file "terraform.tfvars" >}}
    token = "your-utho-api-token"
    root_pass = "your-root-password"
    ssh_key = "ssh-rsa AAAA...Gw== user@example.local"
    {{< /file >}}

    {{< note >}}

Keep all your Terraform project files in one directory. Terraform will automatically pick up input variable values from any file named `terraform.tfvars` or ending in `.auto.tfvars`.
{{< /note >}}

The `region` variable doesn't have a specific value assigned, so it'll use the default value set in its declaration. For more detailed information about input variables, check out [Introduction to HashiCorp Configuration Language](/docs/guides/introduction-to-hcl/#input-variables).

## Terraform CLI

To interact with Terraform, you'll use its command line interface. Once you've created the configuration files in your Terraform project, navigate to the project's directory and run the `init` command:

    terraform init

This command will download the Utho provider plugin and perform other necessary actions to initialize your project. You can safely run this command multiple times, but typically, you'll only need to run it again if you're adding another provider to your project.

### Plan and Apply
Once you've declared your resources in the configuration files, you create them by running Terraform's `apply` command from your project's directory. However, it's crucial to ensure that Terraform will create the resources as expected before making any actual changes to your infrastructure. To accomplish this, start by executing the `plan` command:

    terraform plan

This command generates a report outlining the actions Terraform will take to set up your Utho resources.

If you're satisfied with this report, proceed with the `apply` command:

    terraform apply

This command will prompt you to confirm that you want to proceed. Once Terraform has applied your configuration, it will display a report detailing the actions taken.

### State

When Terraform analyzes and applies your configuration, it builds an internal representation of the infrastructure it creates and uses it to track changes. This information, known as the state, is stored in JSON format by default in a local file named `terraform.tfstate`, but it can also be stored in other *backends*

{{< note type="alert" >}}
It's important to note that sensitive data such as passwords and tokens are visible in plain text within the `terraform.tfstate` file. To secure these secrets, refer to [Secrets Management with Terraform](/docs/guides/secrets-management-with-terraform/#how-to-manage-your-state-file) for guidance.
{{< /note >}}

### Other Commands
Other useful commands include `terraform show`, which provides a human-readable version of your Terraform state. You can find a comprehensive list of [Terraform commands](https://www.terraform.io/docs/commands/index.html) in the official documentation.
## Provisioners

Besides stating what resources you want, Terraform setups can also include things called provisioners. Provisioners are used to execute scripts and commands in your local development environment or on servers managed by Terraform. These actions are carried out when you apply your Terraform configuration.

For instance, the following example demonstrates uploading a setup script to a newly created Utho instance and then executing it. This approach is commonly used for bootstrapping the new instance or enrolling it in configuration management:

    {{< file "example.tf" >}}
    resource "utho_instance" "example_instance" {
    # ...

    connection {
    type     = "ssh"
    user     = "root"
    password = var.root_pass
    host     = self.ip_address
    }

    provisioner "file" {
    source      = "setup_script.sh"
    destination = "/tmp/setup_script.sh"
    }

    provisioner "remote-exec" {
    inline = [
    "chmod +x /tmp/setup_script.sh",
    "/tmp/setup_script.sh",
    ]
    }
    }
    {{< /file >}}

When you assign a provisioner, you should also include a [connection block](https://www.terraform.io/docs/language/resources/provisioners/connection.html) nested within the resource block to specify how Terraform will connect to the remote resource.

Most provisioners are declared within a resource declaration. If multiple provisioners are declared within a resource, they are executed in the order they are listed. For a comprehensive list of [provisioners](https://www.terraform.io/docs/provisioners/index.html), refer to the official Terraform documentation.

{{< note >}}
You can also utilize Utho StackScripts to configure a new Utho instance. The difference between using StackScripts and the file and remote-exec provisioners is that the latter will run and complete synchronously before Terraform proceeds with applying your plan, whereas a StackScript will run concurrently while Terraform creates the remaining resources. Consequently, Terraform might finish its application before a StackScript is fully executed.
{{< /note >}}

## Modules
Terraform enables you to structure your configurations into reusable units called modules. This is particularly helpful when you need to create multiple instances of the same cluster of servers. Refer to [Create a Terraform Module](/docs/guides/create-terraform-module/) for detailed guidance on how to create and utilize modules.

## Backends
By default, Terraform stores its state in your project's directory. However, it also offers support for storing state in non-local [*backends*](https://www.terraform.io/docs/backends/index.html). Utilizing a backend provides several advantages:

- **Enhanced Collaboration**: Backends enable seamless sharing of state among team members who have access to the backend.

- **Improved Security**: State information stored in and retrieved from backends remains solely in memory on your computer, enhancing security.

- **Remote Operations**: Managing a large infrastructure may result in lengthy terraform apply operations. Certain backends allow you to execute apply remotely, alleviating the burden from your local machine.

For detailed information on the [kinds of backends available](https://www.terraform.io/docs/backends/types/index.html), refer to Terraform's official documentation.

## Importing
You can import Utho infrastructure that was previously created outside of Terraform into your Terraform plan. Refer to [Import Existing Infrastructure to Terraform](/docs/guides/import-existing-infrastructure-to-terraform/) for detailed instructions on this topic.

## Next Steps
To begin installing Terraform and creating your initial projects, check out our Use Terraform to Provision Utho Environments guide.
