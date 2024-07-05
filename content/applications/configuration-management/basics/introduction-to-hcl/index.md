---
slug: "Unveiling HCL: Your Gateway to Terraform's Power"
description: "Explore the fundamental syntax and key terminology of HCL with this introductory guide."
keywords: ["terraform", "hcl", "hashicorp", "orchestration", "HashiCorp Configuration Language"]
modified: 2024-03-26
modified_by:
    name: Utho
published: 2024-03-26
title: "Unlocking the Magic: Dive into the World of HashiCorp Configuration Language (HCL)"
title_meta: "Discover the Power of HCL: Your Gateway to Terraform's Secrets Unveiled"
authors: ["Utho"]
---

The HashiCorp Configuration Language (HCL) is a configuration language developed by HashiCorp. It's primarily used alongside HashiCorp's cloud infrastructure automation tools like Terraform. HCL aims to be user-friendly and machine-readable. It's also compatible with JSON, making it easy to work with systems beyond Terraform.

This guide offers an overview of HCL syntax, commonly used terms, and its integration with Terraform.

Note: Terraform’s Utho Provider has received an update and now requires Terraform version 0.12 or newer. If you're unsure how to safely upgrade to Terraform version 0.12 or later, you can refer to Terraform’s official documentation. For a comprehensive list of new features and version incompatibility notes, you can check out Terraform v0.12’s changelog. Please note that the examples provided in this guide were written for compatibility with Terraform version 0.11 but will be updated soon.

## HCL Syntax Overview

HCL's configuration syntax is designed to be simple to read and write. It offers a clearer and more defined structure compared to other widely used configuration languages like YAML.

    {{< file "~/terraform/main.tf">}}

# Utho Provider Block: Installing the Utho Plugin
    terraform {
    required_providers {
    utho = {
    source = "utho/utho"
    version = "1.16.0"
    }
    }
    }
    provider "utho" {
    token = "${var.token}"
    }

    variable "region" {
    description = "This is the location where the Utho instance is deployed."
    }

    /* A multi
    line comment. */
    resource "utho_instance" "example_utho" {
    image = "utho/ubuntu18.04"
    label = "example-utho"
    region = "${var.region}"
    type = "g6-standard-1"
    authorized_keys = [ "my-key" ]
    root_pass = "example-password"
    }
    {{</ file >}}

Note: Avoid including sensitive data in your resource declarations. For detailed guidance on managing secrets, refer to [Secrets Management with Terraform](/docs/guides/secrets-management-with-terraform/).
{{< /note >}}

### Key Elements of HCL

- HCL syntax consists of *stanzas* or *blocks*, which define various configurations for Terraform. More information about available base Terraform configurations can be found in the [Provider plugins](https://www.terraform.io/docs/configuration/providers.html) documentation.

- Stanzas or blocks are made up of `key = value` pairs. Terraform accepts values such as strings, numbers, booleans, maps, and lists.

- Single line comments begin with `#`, while multi-line comments are enclosed by `/*` and `*/`.

- You can use [Interpolation syntax](https://www.terraform.io/docs/configuration/interpolation.html) to refer to values stored outside of a configuration block, like an [input variable](#input-variables), or from a [Terraform module](#modules), or the output of a Terraform module.

- To create an interpolated variable reference, you use the `"${var.region}"` syntax. In this example, it refers to a variable named `region`, which is prefixed by `var.`. The opening `${ and closing }` marks the beginning and end of the interpolation syntax.

- To include multi-line strings, use an opening `<<EOF` followed by `EOF` on the line.

- Strings are enclosed within double quotes.

- Lists containing primitive types such as strings, numbers, and booleans are enclosed in square brackets, like [`"Andy"`, `"Leslie"`, `"Nate"`, `"Angel"`, `"Chris"`].

- Maps are represented using curly braces `{}` and colons :. For instance: { "password" : `"my_password"`, `"db_name"` : `"wordpress"` }.

For further details, refer to Terraform's [Configuration Syntax](https://www.terraform.io/docs/configuration/syntax.html) documentation.

## Terraform Providers and HCL Syntax

In Terraform, a *provider* enables interaction with an Infrastructure as a Service (IaaS) or Platform as a Service (PaaS) API, such as the Utho APIv4. The provider defines which resources are accessible for creation, reading, updating, and deletion. Typically, a set of credentials or an access token is required to connect to the service account. For instance, when using the Utho Terraform provider, you'll need your Utho API access token. You can find a comprehensive list of official Terraform providers on HashiCorp's website.

To configure Utho as the provider, you'll need to include a block specifying Utho as the provider and setting your Utho API token in one of the `.tf` files:

    {{< file "~/terraform/terraform.tf" >}}
    provider "utho" {
    token = "my-token"
    }
    {{</ file >}}

Once you've declared the provider, you can proceed to configure the available resources provided by that provider.

Note: Providers come packaged as plugins for Terraform. When you declare a new provider in your Terraform configuration files, remember to run the `terraform init` command. This command handles several initialization steps essential before applying the Terraform configuration, such as downloading plugins for any specified providers.

## Terraform Resources and HCL Syntax

A Terraform resource represents any part of the infrastructure that the provider can manage. With the Utho provider, resources can vary from a Utho instance to a block storage volume to a DNS record. You can find a complete list of supported resources in the Terraform Utho Provider documentation.

Resources are defined using a resource block in a .tf configuration file. In the following example block, a 2GB Utho instance is deployed in the US East data center using an Ubuntu 18.04 image. Additionally, values for the Utho's label, public SSH key, and root password are provided:

    {{< file "~/terraform/main.tf" >}}
    resource "utho_instance" "WordPress" {
    image = "utho/ubuntu18.04"
    label = "WPServer"
    region = "us-east"
    type = "g6-standard-1"
    authorized_keys = [ "example-key" ]
    root_pass = "example-root-pass"
    }
    {{</ file >}}

In HCL, there are special [meta-parameters](https://www.terraform.io/docs/configuration/resources.html#meta-parameters) that apply to all resources, regardless of the provider. These meta-parameters enable you to customize the lifecycle behavior of the resource, specify the quantity of resources to create, or safeguard specific resources from deletion. For further details on meta-parameters, refer to Terraform's [Resource Configuration](https://www.terraform.io/docs/configuration/resources.html) documentation.

## Terraform Modules and HCL Syntax

A *module* serves as a bundled collection of Terraform configurations designed to streamline the creation of resources in reusable setups.

The [Terraform Module Registry](https://registry.terraform.io/) serves as a repository containing community modules, offering assistance in creating resources for different providers. You also have the option to develop your own modules to enhance the organization of Terraform configurations and facilitate their reuse. Once you've created modules, you can distribute them via a remote version control repository, such as GitHub.

### Using Modules

A module block tells Terraform to create an instance of a module, which includes any resources defined within that module.

The only essential configuration for all module blocks is the source parameter, which specifies the location of the module's source code. Other required configurations may differ from module to module. If you're using a local module, you can use a relative path as the source value. For modules sourced from the Terraform Module Registry, the source path is available on the module's registry page.

Here's an example of creating an instance of a module named utho-module-example and providing a relative path as the source of the module's code:

    {{< file "~/terraform/main.tf" >}}
    module "utho-module-example" {
    source = "/modules/utho-module-example"
    }
    {{</ file >}}

Creating modules entails specifying resource needs and configuring parameters using [input variables](#input-variables), variable files, and outputs. For guidance on writing Terraform modules, check out [Create a Terraform Module](/docs/guides/create-terraform-module/)..

## Input Variables

You can define input variables to act as parameters in your Terraform configuration. Typically, input variables are declared in a file named `variables.tf`, but you can also define them in other `.tf` files as Terraform loads all such files.

- Terraform supports variables of various types: string, number, boolean, map, and list. If a variable type is not explicitly stated, Terraform assumes its *type="string"*.

- Providing a meaningful *description* for each input variable is a good practice.

- If a variable doesn't have a *default* value, or if you wish to override its default value, you need to supply a value either through an environment variable or in a variable values file.

### Variable Declaration Example

    {{< file "~/terraform/variables.tf" >}}
    variable "token" {
    description = "This is your utho APIv4 Token."
    }

    variable "region" {
    description = "This is the location where the utho instance is deployed."
    default     = "us-east"
    }
    {{</ file >}}

Two input variables are defined: *token* and *region*. The region variable has a default value set. Since no type is explicitly declared, both variables default to *type = "string"*.

### Supplying Variable Values

You can specify variable values in `.tfvars` files. These files utilize the same syntax as Terraform configuration files.

    {{< file "~/terraform/terraform.tfvars" >}}
    token = "my-token"
    region = "us-west"
    {{</ file >}}

Terraform automatically loads values from files named `terraform.tfvars` or `*.auto.tfvars`. If you use a file with a different name, you must specify it using the `-var-file` option when executing `terraform apply`. You can use the -var-file option multiple times:

     terraform apply \
     -var-file="variable-values-1.tfvars" \
     -var-file="variable-values-2.tfvars"

You can also specify values using environment variables during the `terraform apply` command. Simply prefix the variable name with `TF_VAR_:`

    TF_VAR_token=my-token-value TF_VAR_region=us-west terraform apply

Note: Environment variables can only assign values to variables with the *type "string"*.

### Referencing Variables

You can reference existing input variables in the configuration file using Terraform's interpolation syntax. To see the value of the *region* parameter, you can use interpolation.

    {{< file "~/terraform/main.tf" >}}
    resource "utho_instance" "WordPress" {
    image = "utho/ubuntu18.04"
    label = "WPServer"
    region = "${var.region}"
    type = "g6-standard-1"
    authorized_keys = [ "example-key" ]
    root_pass = "example-root-pass"
    }
    {{</ file >}}

Note: If a variable value isn't provided through any of the methods mentioned earlier, and the variable is used in a resource configuration, Terraform will prompt you for the value when you execute `terraform apply`.

For additional details on variables, refer to Terraform's documentation [Input Variables](https://www.terraform.io/intro/getting-started/variables.html) documentation.

## Interpolation

HCL supports value [interpolation](https://en.wikipedia.org/wiki/String_interpolation), which involves wrapping interpolations with `${}`. Input variable names are prefixed with `var.`.

    {{< file "~/terraform/terraform.tf" >}}
    provider "utho" {
    token = "${var.token}"
    }
    {{</ file >}}

Interpolation syntax is versatile, allowing you to refer to attributes of other resources, utilize built-in functions, and implement conditionals and templates.

In the configuration of this resource, a conditional statement is utilized to assign a value to the `tags` parameter:

    {{< file "~/terraform/terraform.tf" >}}
    resource "utho_instance" "web" {
    tags = ["${var.env == "production" ? var.prod_subnet : var.dev_subnet}"]
    }
    {{< /file >}}

If the `env` variable is set to *production*, the `prod_subnet` variable is employed. Otherwise, the `dev_subnet` variable is utilized.

### Functions

Terraform offers various built-in computational functions capable of performing tasks like file reading, list concatenation, encryption, checksum creation, and search and replace operations.

    {{< file "~/terraform/terraform.tf" >}}
    resource "utho_sshkey" "main_key" {
    label = "foo"
    ssh_key = "${chomp(file("~/.ssh/id_rsa.pub"))}"
    }
    {{</ file >}}

In the following example, `ssh_key = "${chomp(file("~/.ssh/id_rsa.pub"))}"` utilizes Terraform’s built-in function `file()` to specify the local file path to the public SSH key. Additionally, the `chomp()` function eliminates trailing new lines from the SSH key. Notice that these nested functions are enclosed within `${}` to indicate interpolation.

{{< note >}}
Running `terraform console` provides an environment for testing interpolation functions. For instance:

    terraform console

    {{< output >}}
    > list("newark", "atlanta", "dallas")
    [
    "newark",
    "atlanta",
    "dallas",
    ]
    >
    {{< /output >}}

Note: The comprehensive list of Terraform's features and functions can be found in the official documentation. [supported built-in functions](https://www.terraform.io/docs/configuration/interpolation.html#supported-built-in-functions).

### Templates

Templates are useful for storing extensive strings of data. The template provider makes this data available for consumption by other Terraform resources or outputs. The data source can either be a file or an inline template.

The data source employs Terraform's standard interpolation syntax for variables. Consequently, the template is rendered with the variable values provided in the data block.

For instance, in the following example, the template resource substitutes the value from “${utho_instance.web.ip_address}” wherever “${web_ip}” appears inside the template file `ips.json:`

    {{< file >}}
    data "template_file" "web" {
    template = "${file("${path.module}/ips.json")}"

    vars {
    web_ip = "${utho_instance.web.ip_address}"
    }
    }
    {{< /file >}}

Afterwards, you can define an  [*output variable*](https://learn.hashicorp.com/terraform/getting-started/outputs.html) to observe the rendered template when executing `terraform apply`.

    {{< file >}}
    output "ip" {
    value = "${data.template_file.web.rendered}"
    }
    {{< /file >}}

Terraform's official documentation provides a comprehensive list of [all available components](https://www.terraform.io/docs/configuration/interpolation.html) of interpolation syntax.

## Moving Forward with Terraform

Now that you have a basic understanding of HCL, you can start creating a Utho instance using Terraform.
