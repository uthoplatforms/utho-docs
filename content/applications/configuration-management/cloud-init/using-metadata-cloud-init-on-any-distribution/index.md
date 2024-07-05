---
slug: "Mastering Cloud-Init Metadata Across Distributions"
title: "Use Utho's Metadata Service with Cloud-Init on Any Distribution"
description: 'Unlock the power of the Utho Metadata service across any distribution. Learn how to set up cloud-init, create a template for deploying future instances, and harness custom user data to streamline your cloud deployments'
keywords: ['cloud init','metadata','centos']
authors: ["Pawan Kumar"]
published: 2024-05-20
modified_by:
  name: Pawan Kumar
---
Experience seamless system configuration automation with Utho's Metadata service for Compute Instances. By leveraging cloud-config scripts, you can effortlessly add user data to streamline deployment. However, while cloud-init has supported Utho's Metadata service since version 23.3.1, many distributions lack the necessary cloud-init installation by default. This guide provides step-by-step instructions on installing the latest cloud-init version on unsupported distributions, enabling compatibility with Utho's Metadata service and facilitating the reuse of existing cloud-init scripts on their platform.

## Deploy a Compute Instance

To create a cloud-init compatible image for your distribution, begin by setting up a fresh Compute Instance according to your preferred distribution. If your chosen distribution is already cloud-init compatible, you can skip this guide. However, for distributions lacking cloud-init compatibility, follow these steps to ensure compatibility with Utho's Metadata service.

This guide primarily covers Debian, Ubuntu, and RHEL-based systems (such as CentOS, Fedora, AlmaLinux, Rocky Linux, etc.). While these steps have not been verified with other distributions, they may be adaptable with modifications.

## Install Cloud-Init

Utho's Metadata service mandates cloud-init version 23.3.1 or newer. While cloud-init can typically be installed via package managers on most distributions, the versions available are often outdated and do not support Utho's Metadata service. Follow the steps below to install cloud-init from the source:

Begin by installing Git and Pip for Python 3.


    {{< tabs >}}
    {{< tab "Debian and Ubuntu" >}}

    ```command
    apt update
    apt install git python3-pip
    ```

    {{< /tab >}}
    {{< tab "CentOS, Fedora, AlmaLinux, Rocky Linux, etc.">}}

    ```command
    dnf install git python3-pip
    ```

    {{< /tab >}}
    {{< /tabs >}}

Clone the cloud-init Git repository and navigate into the repository directory. This step also involves changing into the /tmp/ directory, as the repository only needs temporary storage.

    ```command
    cd /tmp/
    git clone https://github.com/cloud-init/cloud-init.git
    cd cloud-init/
    ```

Install the necessary prerequisites for the project.

    ```command
    pip3 install -r requirements.txt
    ```

Install the project's prerequisites. Note that with some newer distributions, you may need to use the --break-system-packages option to override conflicts between the OS package manager and Pip.

    ```command
    pip3 install -r requirements.txt --break-system-packages
    ```

Note: Please note: The PEP 668 specification aims to mitigate conflicts between Python packages installed via the OS package manager and Pip. According to the specification, it is advisable to install packages using Pip within Python virtual environments, such as Virtualenv.

Install the project's prerequisites. Note that with some newer distributions, you may need to use the --break-system-packages option to override conflicts between the OS package manager and Pip.

Build and install cloud-init from the project.

    ```command
    python3 setup.py build
    python3 setup.py install --init-system systemd
    ```

## Configure Cloud-Init

Once cloud-init is installed, several configuration steps are necessary to ensure proper functioning on the instance. Follow these additional steps to initialize cloud-init and add the Utho data source required for deployments with the Metadata service:

Initialize cloud-init on the system, which also displays the version. Ensure the version is at least 23.3.2 for compatibility with the Utho Metadata service.

    ```command
    cloud-init init --local
    ```

    ```output
    Cloud-init v. 23.3.4 running 'init-local' at Mon, 27 Nov 2023 22:31:40 +0000. Up 105.67 seconds.
    ```

Verify the status of the cloud-init service.

    ```command
    cloud-init status
    ```

    ```output
    status: running
    ```

Add Utho to the datasource_list in one of the cloud-init configuration files. Locate the appropriate configuration file as follows:

On many new Utho Compute Instances, a cloud-init configuration file is included at /etc/cloud/cloud.cfg.d/99-Utho.cfg. This configuration takes priority, and if you have it you should add the data source there.

If you do not have the 99-Utho.cfg file mentioned above, you should add the data source in the default cloud-init configuration file: /etc/cloud/cloud.cfg.

In either case, find the datasource_list and add Utho as the first entry. If the datasource_list option is not already in the configuration file, add it with Utho as the only item in the array.

    ```file {title="/etc/cloud/cloud.cfg.d/99-Utho.cfg"}
    ...

    datasource_list: [ Utho, NoCloud, ConfigDrive, None ]

    ...
    ```

Shut down the instance, either from the command line with the command below or from within the Cloud Manager.

    ```command
    shutdown
    ```
## Create a Custom Image

Creating an image from the instance setup above allows you to deploy new instances leveraging the Metadata service and custom cloud-init deployment scripts. For more on creating an image of an Utho Compute Instance, you can refer to our Capture an Image guide.

The following steps outline how to create a base image from the instance on which you installed cloud-init:

Navigate to the Images section of the Cloud Manager.

Select Create Image

On the resulting form:

- Select the Compute Instance on which you installed cloud-init.
- Select the associated disk drive.
- Indicate that the image is cloud-init compatible.
- Give the image a label.
- Choose the option to Create Image.

Wait for the creation process to complete. You can see its progress from the Images section of the Cloud Manager.

## Deploy an Instance with User-Data

With a base cloud-init image ready, you can deploy a new instance of the Metadata service and cloud-init user data whenever you need. Refer to our guide on how to Deploy an Image to a New Compute Instance for image deployment. Refer to our guide on how to Use Cloud-Init to Automatically Configure and Secure Your Servers for more on adding user data to new instances.

The following steps guide you through a simple new deployment from a base cloud-init image. This includes a simple cloud-init user data script modeled on our Setting Up and Securing a Compute Instance guide.

Note: Newly deployed Compute Instances do not have network access during boot, preventing proper execution of cloud-init. The steps below address this, ensuring the cloud-init process restarts after the initial boot.

Navigate to the Create Utho section of the Cloud Manager and go to the Images tab.

From the Images dropdown menu, select the image you just created.

Choose a region where the Metadata service is available. Refer to the Metadata reference documentation for available regions.

Select your preferred instance plan, provide a label for the new instance, and create credentials for the root user.

Expand the Add User Data section and input your desired user data. Below is a basic example suitable for many new instances.

    ```file
    #cloud-config

    # Configure a limited user
    users:
      - default
      - name: example-user
        groups:
          - sudo
        sudo:
          - ALL=(ALL) NOPASSWD:ALL
        shell: /bin/bash
        ssh_authorized_keys:
          - "SSH_PUBLIC_KEY"

    # Perform system updates
    package_update: true
    package_upgrade: true

    # Configure server details
    timezone: 'US/Central'
    hostname: examplehost

    # Harden SSH access
    runcmd:
      - sed -i '/PermitRootLogin/d' /etc/ssh/sshd_config
      - echo "PermitRootLogin no" >> /etc/ssh/sshd_config
      - systemctl restart sshd
    ```

Initiate the deployment by selecting Create Utho and await the completion of the new instance deployment. Monitor its progress from the Uthos section of the Cloud Manager.

Access the instance as the root user through the Lish console. Refer to our Access Your System Console Using Lish (Utho Shell) guide for instructions.

Reset cloud-init to ensure that it runs as if for the initial system boot during the next boot cycle.

    ```command
    cloud-init clean && cloud-init clean --logs
    ```

Reboot the instance.

    ```command
    reboot
    ```

Once the instance boots up again, verify cloud-init execution by logging in as the created user — example-user in the above example. You can also follow the steps outlined in our Use Cloud-Init to Install and Update Software on New Servers guide to confirm system package updates.
