---
slug: Deploying in Code with Pulumi
description: 'Discover how to set up Pulumi, incorporate the utho module for Pulumi, and craft your inaugural Pulumi scripts'
keywords: ["pulumi", "configuration management", "infrastructure as code", "iac", "javascript", "python"]
tags: ["debian"]
modified_by:
    name: Utho
published: 2024-05-21
title: Getting Started with Pulumi
authors: ["Utho"]
---

## What is Pulumi?

Pulumi is a development tool designed for deploying cloud resources through code—a practice commonly known as infrastructure as code (IaC). It seamlessly integrates with various cloud platforms, and you can write Pulumi programs using popular programming languages.

By leveraging Pulumi's utho integration, you can manage your utho resources using familiar programming languages instead of solely relying on utho's API or CLI. While this guide showcases examples in JavaScript, Pulumi supports Go, Python, and TypeScript as well.

Additionally, Pulumi provides a CLI interface to execute the cloud infrastructure programs you create. Once you've crafted your program, you can deploy your cloud resources effortlessly with a single command:

                     pulumi up

In this tutorial, you'll discover how to:

- Install and configure Pulumi on Debian 9
- Deploy a single utho instance using Pulumi and JavaScript
- Set up a NodeBalancer with two NGINX webserver backends using Pulumi and JavaScript

##  Before You Begin

If you haven't already, generate a utho API token.

Sign up for a free Pulumi Cloud account here.

Deploy a new utho running Debian 9. Refer to our Creating a Compute Instance guide for instructions on deploying the utho. After deployment, follow the steps outlined in the Setting Up and Securing a Compute Instance guide. Ensure you create a limited Linux user with sudo privileges on your server. All commands in this guide should be executed from a sudo user.

Install Pulumi on your utho by running their installation script, available here.

                 curl -fsSL https://get.pulumi.com | sh

To begin using the Pulumi CLI, you have two options:

- Restart your shell session, or

- Add /home/username/.pulumi/bin to your $PATH variable in your current session. Replace username with the name of your limited Linux user.

                 PATH=$PATH:/home/username/.pulumi/bin

Install Node.js and npm:

                sudo apt-get install curl software-properties-common
                curl -sL https://deb.nodesource.com/setup_12.x | sudo bash -
                sudo apt-get install -y nodejs

## Generate a Pulumi Access Token

Once you have a Pulumi account, you will need to create an access token to use later.

Note: Pulumi stores the state of your infrastructure resources in a persistent backend. This allows it to track changes and updates to your infrastructure over time. By default, Pulumi securely stores this state information on the Pulumi Cloud, a web backend hosted at https://app.pulumi.com. This service is free to start and offers paid tiers for teams and enterprises.

It is possible to opt-out of using the default web backend and use a self-managed backend instead. Review Pulumi's documentation for instructions.

Log into your Pulumi account. After you've logged in, click on the avatar graphic at the top right of the Pulumi dashboard, then select Settings from the dropdown menu that appears:

Navigate to the Access Tokens section in the sidebar on the left side of the page.

Click on the New Access Token button located towards the top right of the page. Follow the prompts to create your new token. Ensure you save this token in a secure location, similar to your utho API token.

## Create a utho

### Set up your Pulumi Project

Now that you have everything you need to begin using Pulumi, you can create a new Pulumi project.

Note: A Pulumi project is a folder structure that contains your Pulumi programs. Specifically, a project is any folder that contains a Pulumi.yaml metadata file.

Pulumi requires an empty directory for each new project, so first, you'll need to create one and set it as your working directory:

        cd ~/ && mkdir pulumi && cd pulumi

Now that you're inside your new empty working directory, create a new project:

                 pulumi new

You'll see several prompts:

Enter your Pulumi access token if prompted. If you've already entered it following the installation of Pulumi, you won't be prompted again and can skip this step.

- Use your arrow keys to select the utho-javascript option.
- Enter a project name of your choice, or leave it blank to accept the default option.
- Enter a project description, or leave it blank to accept the default option.
- Enter a stack name of your choice, or leave it blank to accept the default option.

Note: Multiple instances of your Pulumi programs can be created. For example, you may want separate instances for development, staging, and production environments, or you may create multiple instances for different business clients. In Pulumi, these instances are referred to as stacks.

- Enter your utho API token.

Once the installation is successful, you will see a Your new project is ready to go! message. The pulumi new command will scaffold a collection of default configuration files in your project's directory. This default configuration provides everything you need to get started. Enter the ls command to ensure the files are present:

                  ls

                 {{< output >}}
                     index.js      package.json	 Pulumi.pulumi.yaml
                     node_modules  package-lock.json  Pulumi.yaml
                 {{< /output >}}

The contents of these files were generated based on your responses to the prompts after entering pulumi new. Specifically:

- index.js contains the JavaScript code that Pulumi will run.
- package.json defines the dependencies we can use and specifies the file path from which Pulumi will read our code.

### Inspect the Default Configuration

Let's take a look at the contents of the index.js file:

            {{< file "index.js" javascript >}}
                       "use strict";
                       const pulumi = require("@pulumi/pulumi");
                       const utho = require("@pulumi/utho");

                      // Create a utho resource (utho Instance)
                         const instance = new utho.Instance("my-instance", {
                                                         type: "g6-nanode-1",
                                                          region: "us-east",
                                                image: "utho/ubuntu18.04",
                                                                         });

                                 // Export the Instance label of the instance
                                       exports.instanceLabel = instance.label;
                                                                 {{< /file >}}

The file requires two JavaScript modules unique to Pulumi: Pulumi's SDK and Pulumi's utho integration. Pulumi's API Reference Documentation serves as a reference for the JavaScript you'll see here. It also includes a library of additional options that enable you to create configurations tailored to your specific use case.

In this example, the file creates a single 1GB utho (Nanode) instance in the Newark data center running Ubuntu 18.04.

### Create and Destroy Resources

Use Pulumi's preview command to test your code and ensure it can successfully create resources under your account.

                     pulumi preview

The output will list the operations Pulumi will perform once you deploy your program:

                     Previewing update (dev):

                      Type                      Name                   Plan
                       +   pulumi:pulumi:Stack       my-pulumi-project-dev  create
                       +   └─ utho:index:Instance  my-instance            create

                        Resources:
                       + 2 to create

Use Pulumi's up command to deploy your code to your utho account:

                      pulumi up

Note: This will create a new billable resource on your account.

You will be prompted to confirm the resource creation. Use your arrow keys to select yes and press enter. You will see your resources being created. Once completed, the utho Label of your new utho will be displayed. You can verify this by checking your account manually through the Cloud Manager.

Since this utho was only created as a test, you can safely delete it by using Pulumi's destroy command:

                    pulumi destroy

Follow the prompts, and you will see the resources being removed, similar to how you saw them being created.

Note: Many Pulumi commands are logged on your Pulumi account. You can view this activity under the Activity tab of your project's stack in Pulumi Cloud.

## Create and Configure a NodeBalancer

To better demonstrate Pulumi's capabilities, we'll create a new index.js file. This file will define everything needed to create a functioning NodeBalancer pre-configured with two backend uthos running NGINX.

Replace the contents of your index.js file with the following code:

                 {{< file "index.js" javascript >}}
                  const pulumi = require("@pulumi/pulumi");
                  const utho = require("@pulumi/utho");

                 // Create two new 1GB uthos (Nanodes) using a StackScript to configure them internally.
                 // The StackScript referenced will install and enable NGINX.

                // "utho1" (the first argument passed to the utho instance constructor function) is the Pulumi-allocated Unique Resource Name (URN) for this resource
                 const utho1 = new utho.Instance("utho1", {
                 // "PulumiNode1" is the utho's label that appears in the Cloud Manager. utho labels must be unique on your utho account
                  label: "PulumiNode1",
                  region: "us-east",
                  image: "utho/debian9",
                  privateIp: true,
                  stackscriptData: {
                  hostname: "PulumiNode1",
                  },
                  stackscriptId: 526246,
                  type:"g6-nanode-1",
                  });

                  const utho2 = new utho.Instance("utho2", {
                  label: "PulumiNode2",
                  region: "us-east",
                  image: "utho/debian9",
                  privateIp: true,
                  stackscriptData: {
                  hostname: "PulumiNode2",
                  },
                  stackscriptId: 526246,
                  type:"g6-nanode-1",
                  });

                 // Create and configure your NodeBalancer

                  const nodeBalancer = new utho.NodeBalancer("nodeBalancer", {
                  clientConnThrottle: 20,
                  label: "PulumiNodeBalancer",
                  region: "us-east",
                  });

                 const nodeBalancerConfig = new utho.NodeBalancerConfig("nodeBalancerConfig", {
                 algorithm: "source",
                 check: "http",
                 checkAttempts: 3,
                 checkTimeout: 30,
                 checkInterval: 40,
                 checkPath: "/",
                 nodebalancerId: nodeBalancer.id,
                 port: 8088,
                 protocol: "http",
                 stickiness: "http_cookie",
                 });

                // Assign your uthos to the NodeBalancer

                const balancerNode1 = new utho.NodeBalancerNode("balancerNode1", {
                address: pulumi.concat(utho1.privateIpAddress, ":80"),
                configId: nodeBalancerConfig.id,
                label: "PulumiBalancerNode1",
                nodebalancerId: nodeBalancer.id,
                 weight: 50,
                 });

                const balancerNode2 = new utho.NodeBalancerNode("balancerNode2", {
                address: pulumi.concat(utho2.privateIpAddress, ":80"),
                configId: nodeBalancerConfig.id,
                label: "PulumiBalancerNode2",
                nodebalancerId: nodeBalancer.id,
                weight: 50,
                });

                //Output your NodeBalancer's Public IPV4 address and the port we configured to access it
                 exports.nodeBalancerIP = nodeBalancer.ipv4;
                 exports.nodeBalancerPort = nodeBalancerConfig.port;
                 {{< /file >}}

Note: In the new index.js file, we create and configure two uthos using an existing StackScript that installs NGINX. Pulumi's utho integration also allows you to create entirely new StackScripts directly in code, enhancing your automation capabilities.

If you're interested in how this StackScript works, you can view it here.

Now that you've prepared your JavaScript code, let's bring up our configuration:

        pulumi up

As before, select yes when prompted and wait for a few moments as your resources are created, configured, and brought online.

Once the process is completed, you'll see your NodeBalancer's IP address and the port you configured displayed in the output:

               Outputs:
               + nodeBalancerIP  : "192.0.2.1"
               + nodeBalancerPort: 8088

Enter this IP address and port into your web browser, and you will see the Hello World-style page that the StackScript configured:

        curl http://192.0.2.3:8087/

                        {{< output >}}
                       Hello from PulumiNode1
                       {{< /output >}}

Note: If you do not see this page right away, wait a few additional moments. NodeBalancers can sometimes require extra time to fully apply a new configuration.

Once you're finished with your NodeBalancer, you can remove and delete everything you added by entering pulumi destroy as before.

## Next Steps

Pulumi is a powerful tool with a vast number of possible configurations. From here you can:

- Explore Pulumi's examples for more ideas on what you can do with Pulumi.
- Try using Pulumi with different languages like Python or TypeScript.
- Import Node.js tools like Express for even more flexibility with your code.
- Use Pulumi for Serverless Computing.
