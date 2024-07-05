---
slug: "Unlocking the Magic: Your Guide to Installing YunoHost"
title: "How to Install and Use YunoHost"
description: "Empower Your Server Journey with YunoHost! Seamlessly manage apps, users, and services through an intuitive web interface. Ready to dive in? Click here to embark on your YunoHost adventure"
keywords: ['yunohost install','yunohost apps','yunohost alternative']
authors: ["Pawan Kumar"]
published: 2024-05-06
modified_by:
  name: Utho

---
YunoHost offers a user-friendly platform for simplified self-hosting and server management. With YunoHost, you can effortlessly set up servers, install applications, manage users, and more, all via a convenient web interface.

This guide walks you through the process of installing YunoHost on a Debian base server and provides detailed steps to kickstart your journey with it.

## Before You Begin
Ensure you have a Utho account and a Compute Instance running Debian 11 or above. If you haven't already, check out our Getting Started with Utho and Creating a Compute Instance guides.

Be sure to also add an [A and AAA record](/docs/products/networking/dns-manager/guides/a-record/) pointing to the remote IP address of your Compute Instance.

Optionally, consider creating a domain name and configuring it using Utho's DNS Manager. For instructions on adding a domain to utho and using utho name servers with your domain registrar, refer to our DNS Manager - Get Started guide.Don't forget to include an [A and AAA record](/docs/products/networking/dns-manager/guides/a-record/) pointing to the remote IP address of your Compute Instance.

Note The steps outlined in this guide require `root` privileges. Make sure to execute the following steps as root. For further details on privileges, refer to our [Users and Groups](/docs/guides/linux-users-and-groups/) guide.

## What Is YunoHost?
[YunoHost](https://yunohost.org/#/) functions as an operating system built on Debian, aimed at simplifying the complexities of self-hosting and Linux server administration.

One of its standout features is its marketplace of open-source applications. YunoHost offers a convenient way to install and manage these applications through a centralized web interface. Additionally, YunoHost incorporates single-sign-on (SSO), facilitating seamless navigation between applications.

The applications within the YunoHost "marketplace" span from system and development tools to social media and publishing platforms.

Beyond application installation, YunoHost encompasses various other server administration features. It enables management of user accounts and the server's SSL certification. Moreover, it includes a comprehensive email service and furnishes tools for monitoring and interacting with running services and firewalls via its web interface.

### YunoHost vs Cloudron
YunoHost operates similarly to Cloudron, another tool offering an application marketplace and simplifying system administration. So why opt for YunoHost over Cloudron?

Both uphold an open-source ethos, but YunoHost follows a purely open-source model. Cloudron provides both a free and a premium tier, which imposes limitations on certain features (e.g., the number of applications) and restricts others (e.g., email services) to the premium tier. In contrast, YunoHost operates without any feature limits or paid services, adhering strictly to an open-source model.

However, Cloudron boasts a more polished and simplified presentation. YunoHost's setup process can be more intricate, and its interface might not be as intuitive for some users initially.

## How to Install YunoHost

YunoHost installation on a compute instance requires a stock Debian 11 (or higher) setup, without the need for additional software installations or configuration changes. The post-installation script for YunoHost takes care of all necessary server configurations and security measures.

Connect to the Debian instance as the `root` user, either via SSH or the console within the utho Cloud Manager.

If the system's firewall is enabled, ensure it permits connections on the HTTPS port (`443`). Use the following commands with UFW, the standard tool for managing firewalls on Debian:

    ```command
    ufw allow https
    ufw reload
    ```

Execute the following commands to install YunoHost. The first command ensures prerequisite packages are installed, while the second initiates the YunoHost installation script:

    ```command
    apt install curl ca-certificates
    curl https://install.yunohost.org | bash
    ```

During installation, when prompted to overwrite configuration files and allow YunoHost to reconfigure SSH, choose the default options (**Yes and No**, respectively).

    ```output
    [...]

    [ OK ] YunoHost installation completed !
    ===========================================================================
    You should now proceed with Yunohost post-installation. This is where you will
    be asked for :
      - the main domain of your server ;
      - the administration password.

    You can perform this step :
      - from the command line, by running 'yunohost tools postinstall' as root
      - or from your web browser, by accessing :
        - https://192.0.2.0/ (global IP, if you're on a VPS)

    If this is your first time with YunoHost, it is strongly recommended to take
    time to read the administrator documentation and in particular the sections
    'Finalizing your setup' and 'Getting to know YunoHost'. It is available at
    the following URL : https://yunohost.org/admindoc
    ===========================================================================
    ```

Follow the post-installation setup instructions provided by the installation script. This can be done either through the command line or a browser.

Note: The YunoHost post-installation setup requires a domain name. You can bypass purchasing and configuring a real domain by entering a dummy domain like `no.domain`. However, note that this may affect the behavior of some applications.

To complete post-installation via a browser, navigate to the URL indicated in the installation script output. This should be an HTTPS address with your system's remote IP address. For example, if your system's remote IP address is `192.0.2.0`, navigate to `https://192.0.2.0/`.

Follow the instructions on the screen to set up a domain name and create an administrator password for your YunoHost instance.

To finalize the post-installation setup from the command line, run the following command on the system while logged in as the `root` user:

        ```command
        yunohost tools postinstall
        ```

When prompted, enter a domain name and create an administrator username and password. The post-installation script then runs through its configuration steps.

        ```output
        Main domain: example.com
        New administration password: ********
        Confirm new administration password: ********

        [...]

        Success! YunoHost is now configured
        Warning: The post-install completed! To finalize your setup, please consider:
            - adding a first user through the 'Users' section of the webadmin (or 'yunohost user create <username>' in command-line);
            - diagnose potential issues through the 'Diagnosis' section of the webadmin (or 'yunohost diagnosis run' in command-line);
            - reading the 'Finalizing your setup' and 'Getting to know YunoHost' parts in the admin documentation: https://yunohost.org/admindoc.
        ```

Note: The post-installation setup modifies the SSH configuration of the utho. After setup, use the admin user and the password created during the setup process to connect via SSH.

For example, if your Utho's remote IP address is 192.0.1.0:

```command
ssh admin@192.0.2.0
```

If configured during setup, the domain name can also be used for SSH connection, assuming the DNS is configured accordingly. For instance, using the `example.com` domain from the example above:

``` command
ssh admin@example.com
```

## How to Get Started with YunoHost

Once YunoHost is installed, log in. YunoHost offers two interfaces: one for administrators and another for users. Each interface provides functionalities for different tasks.

To initialize the server fully, start by installing an SSL certificate and creating a user. Follow the steps outlined in the administrator interface section below.

### Administrator Interface

The administrator interface can be accessed by navigating to the `/yunohost/admin` path of your server's addresses in a web browser. Using the example above, access the administrator interface by visiting either:

-   `https://192.0.2.0/yunohost/admin`
-   `https:/example.com/yunohost/admin`

Log in using the administrator username and password created during the post-installation setup.

Once logged into the administrator interface, you'll find tools for managing users, domains, applications, and server processes.

The subsequent sections guide you through various essential tasks, including deploying applications to the server. Installing an SSL certificate and creating a user, covered initially, are highly recommended before proceeding with other tasks.

#### Installing an SSL Certificate

The YunoHost instance initially uses a self-signed certificate. However, most modern web browsers display a security warning when users visit websites with self-signed certificates. To address this, start by obtaining a free certificate signed by Let's Encrypt.

Navigate to the **Domains** option on the main page of the YunoHost administrator interface.

Choose the entry corresponding to the domain name added during the post-installation setup. In the provided example, this was `example.com`.

Access the **Certificate tab**.

Opt for **Install Let's Encrypt certificate**, then confirm by selecting **OK** to initiate the installation process.

Note: You might encounter several warnings that can prevent the **Install Let's Encrypt certificate** button from functioning properly. If this occurs, toggle the **Ignore diagnosis checks** option from **No** to **Yes**. This action will re-enable the **Install Let's Encrypt certificate** button.

Upon completion of the process, you will receive a confirmation indicating the successful installation.

#### Create a User
The post-installation setup generates administrator credentials for your YunoHost instance. However, YunoHost requires at least one regular user account for various operations, including application installations.

Each user interacts with installed applications and automatically receives an email address. Additionally, YunoHost serves as a Single Sign-On (SSO) portal, allowing users to log in once and access multiple applications seamlessly.

Navigate to the **Users** option on the YunoHost administrator interface main page.

Click **+ New User** located at the upper right corner of the user page.

Enter a **Username**, **Full Name**, and **Password** for the new user. YunoHost automatically generates an email address for the user based on the username. For instance, creating a user with the username **exampleuser** on a domain named example.com would result in the automatic generation of the email address **exampleuser@example.com** for the new user.

Click **Save** to finalize the user creation process.

Note: While YunoHost includes a comprehensive email stack, outbound emails may be restricted for newer utho accounts to prevent spam.

#### Install an Application

One of YunoHost's standout features is its simplicity in installing server applications directly from its web interface. Explore its marketplace of open-source tools, select an application, and with just a few clicks, have it up and running on your server.

Access the **Applications** option on the YunoHost administrator interface main page.

Click **+ Install** located at the upper right corner of the applications page.

Browse through the list of applications to find one for installation. Applications marked with gold stars next to their names are well-integrated with YunoHost and are recommended for beginners.

As an example, let's select the [Mastodon](https://joinmastodon.org/) application, a microblogging platform part of the Fediverse. You can find Mastodon in YunoHost under the **Social media** category.

Customize the parameters in the **Install settings** section according to your requirements. For this example, simply adjust the language to English and designate the standard user created earlier as an administrator for the new application.

Click on **Install** to initiate the installation process.

Once the installation is finished, the application will be listed on the **Applications** page of the YunoHost administrator interface.

The application is now ready for use. YunoHost users automatically gain access to the new application through the YunoHost user portal. The following section, focusing on the user interface, demonstrates how to utilize the portal to access the newly installed application.

### User Portal

With a YunoHost user account created, access the user portal. This portal serves as an SSO hub for users, enabling them to sign in and navigate to various installed applications.

There are two primary methods for accessing the user portal:

- Click on the User interface button located at the upper right corner of the administrator interface.
- Visit the /yunohost/sso path of your server's address. Using the examples provided earlier, this would be either https://192.0.2.0/yunohost/sso or https://example.com/yunohost/sso.

Most users prefer accessing the interface through the second option, using the server address.

The portal will prompt for user credentials to log in. Utilize the credentials created through the administrator interface (e.g., `exampleuser`).

Once logged in, you'll see a gallery of installed applications. Selecting one of these will take you to the application's interface. For applications supporting this feature, YunoHost utilizes SSO to automatically log the user in.

Following the example of the application installed earlier, an icon for Mastodon should now be visible. Clicking on it will open the Mastodon instance and automatically log in as the current YunoHost user (e.g., `exampleuser`).

## Conclusion

YunoHost offers a comprehensive interface for simplifying self-hosting and server administration. The features covered in this guide provide a solid foundation for starting with YunoHost, and many scenarios require no further setup.

Additionally, YunoHost offers an extensive suite of documentation and resources to assist in building your setup. The YunoHost documentation, linked below, covers administration, application listings, and provides links to community resources.
