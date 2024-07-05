---
slug: "Automate Web Server Creation on utho with Laravel Forge"
description: 'This guide demonstrates how to leverage Laravel Forge to automate the deployment of your PHP projects on a utho server'
keywords: ["content management", "web-server automation", "laravel", "php", "wordpress", "drupal", "cms", "joomla", "Laravel Forge"]
tags: ["automation", "php", "drupal", "wordpress", "cms"]
modified: 2024-05-20
modified_by:
    name: Utho
published: 2024-05-20
title: 'Use Laravel Forge to Automate Web-Server Creation on a utho'
authors: ["Pawan Kumar"]
---

## What is Laravel Forge

Laravel Forge is a powerful tool for deploying and configuring web applications, developed by the creators of the Laravel framework. Although tailored for Laravel, it can automate the deployment of any web application running on a PHP server.

Setting up a fully functional web server typically requires installing and configuring components like NGINX, MySQL, and PHP. Laravel Forge simplifies this process by automating all necessary installation and configuration steps, enabling you to launch your website quickly.

Once your server is set up, deploying updates is as easy as pushing to your GitHub repository. You can manage your website's configuration through a user-friendly web interface. Additionally, Forge enhances security with advanced features like free SSL certificates (via Let's Encrypt) and automatic firewall configuration.

## Before You Begin

Sign up for a Laravel Forge account if you don't have one.

Create a utho API key for Laravel Forge to interface with your account. Forge uses utho's new APIv4, and you can create APIv4 tokens in the utho Cloud Manager. Refer to the Getting Started with the utho API guide for instructions on creating your key.

Purchase a domain name from a domain name registrar if you don't already have one.

Note: While you can set up a site without a domain name and access it via your utho's IP address, you will need a domain name to use SSL.


## Set Up your Forge Account

### Link to a Source Control Service

To quickly deploy from GitHub, GitLab, or Bitbucket, you need to link these services to your Forge account. Follow these steps:

From the top navigation menu of the Laravel Forge website, click on your username and select My Account.

Navigate to the Source Control section.

Choose your source control provider. Your browser will redirect to the provider's website, where you'll need to authorize the connection.

Confirm the authorization request. You will be redirected back to the Laravel Forge website.

### Adding your utho API Key to Forge

In the My Account page, go to the Server Providers section and select utho Cloud.

Enter a profile name (e.g., your utho username) and your APIv4 key, then click Add Credential.

## Create a Server

Click on the Forge icon in the top left navigation menu, then select the utho provider button.

Fill out the form that appears with the necessary details.

    | **Option**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | **Description** |
    | ------ | ----------- |
    | Credentials | Any of the utho accounts that you have linked to your Laravel Forge account. |
    | Name | A name for your server. Laravel Forge auto-generates a random name, but you can edit it. |
    | Region | The data center where you want your server hosted. Choose a location close to where you expect the majority of users to be. |
    | Server Size | The hardware resource plan for your server. Plans with more CPU and memory can serve more connections to your sites, and a larger storage capacity can hold bigger databases. |
    | PHP Version | The PHP version to install. |
    | Post-Provision Recipe | [Actions](https://medium.com/@taylorotwell/post-provision-recipes-on-forge-634ccb189847) that should be taken after the server is provisioned. |
    | Database | The database package to install. |
    | Database Name | Your application's database name. By default it'll be named `forge`. |

Once you've finished selecting options, click Create Server. A pop-up dialog will show you the sudo and database passwords that have been automatically generated. Copy these values and store them securely.

Forge will now create and configure a utho based on your settings. The new server will appear in the Active Servers section, and a list of recent events representing the server's configuration will appear below it.

When the server status shows as Active, navigate to the public IP address of your new utho in a browser to see the PHP settings active for the server.

You will also receive an email with details about your new server once the setup process is complete.

## Set Up your Site

Your server is now created, but no sites are set up on it, apart from the default site displaying your PHP settings.

Note: If you do not want to use a domain, you can configure the default site on your server. Select the default site from the Active Sites panel on your server's dashboard in Forge, then skip to the Add a repository instructions.

### Add a New Site

Set up your DNS records for your domain. Create a Domain Zone and an A record assigned to your utho's IP address. If you use utho's name servers, refer to the DNS Manager guide. For other DNS providers, check their documentation.

               {{< content "update-dns-at-common-name-server-authorities" >}}

From the Servers menu in the top navigation bar, select your new server. If you don't see this menu yet, refresh your browser window.

Fill out the New Site form and click Add Site.

    | **Option**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | **Description** |
    | ------ | ----------- |
    | Root Domain | Your website's domain. |
    | Project Type | Your project type. If you are building regular PHP, you can choose `General PHP/Laravel`. Other options include Static, HTML, Symfony, and Symfony (Dev). |
    | Web Directory | The directory from which public files will be served. You will need to make sure your website's files are in this directory in your source code repository. |

Your new site will appear below the form in the Active Sites panel.

### Add a Repository

From the Active Sites panel, click on your site. The Apps section will appear.

Select the Git Repository option and fill out the form that appears. The format for the Repository field should be source_control_username/repository_name. Laravel Forge will pull your code from the branch you enter.

Note: If you do not use Composer, leave the Install Composer Dependencies option disabled to avoid deployment errors.

Click on the Install Repository button. Forge will copy your source code to the server. When this process completes, visit your domain name in your browser to see your site's contents.

If your site deployment encounters any errors, a Server Alerts panel will appear. Click on the information button in this panel to review the errors in detail.

Verify that your site displays your repository's latest changes by navigating to your site's URL. If you did not register a domain, navigate to your site's IP address.

### Quick Deploy

Forge can automatically deploy updates to your server whenever you push new code to your application's repository.

From the Apps section of your site's dashboard in Forge, click on Enable Quick Deploy.

Make changes to your project's code and push them to your repository service. Your live site will automatically update to reflect these changes.

### Adding SSL to your Domain Name

SSL (Secure Sockets Layer) is the standard security technology for establishing an encrypted link between a web server and a browser. To add SSL:

Navigate to the SSL section of your site's dashboard in Forge.

If you already have an SSL certificate, click on Install Existing Certificate. Otherwise, select the Let's Encrypt option.

Note: Let's Encrypt is a free and widely-trusted SSL certificate authority.

If you choose to use Let's Encrypt, verify that the domains you want to secure are listed correctly and click Obtain Certificate.
