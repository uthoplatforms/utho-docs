---
slug: "Unlocking Dropbox: Easy Setup and Sync on Utho"
description: "Setting Up and Configuring Dropbox on Utho"
keywords: ["Dropbox", "debian", "centos", "fedora", "ubuntu", "headless", "storage", "cloud storage"]
tags: ["ubuntu", "debian", "centos", "fedora"]
modified: 2024-03-15
modified_by:
    name: Utho
published: 2024-03-15
title: 'Setting Up and Configuring Dropbox'
authors: [Utho]
---

Installing and Configuring Dropbox

Dropbox is a versatile tool for storing your documents, files, videos, and photos. Anything you store on Dropbox is accessible through the Dropbox website and any devices where you've installed the Dropbox application.

Before setting up Dropbox on your Utho, it's advisable to go through the Setting Up and Securing a Compute Instance guide. Additionally, you'll need a [Dropbox account](https://www.dropbox.com/). Dropbox is compatible with Debian, Ubuntu, and any Red Hat Enterprise Linux-based operating systems.

## Installing Dependencies

To install Dropbox on the latest modern Distros, you'll need to install a few dependencies first. Use the appropriate command below based on your Distro:

**Debian and Ubuntu**

    sudo apt install libc6 libglapi-mesa libxdamage1 libxfixes3 libxcb-glx0 libxcb-dri2-0 libxcb-dri3-0 libxcb-present0 libxcb-sync1 libxshmfence1 libxxf86vm1

**CentOS**

    yum install tar wget libglapi libXext libXdamage libxshmfence libXxf86vm

## Setting Up and Configuring Dropbox

1.  Download and install the Dropbox package:

        cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -

2.  Launch the Dropbox daemon:

        ~/.dropbox-dist/dropboxd &

3.  You'll get a message indicating that the computer isn't linked to your Dropbox account:

        This computer isn't linked to any Dropbox account...
        Please visit https://www.dropbox.com/cli_xxxxxxx to link this device.

  Copy the unique URL provided. Ensure not to copy the one displayed above.

4.  Paste the address provided above into your web browser and sign in to your Dropbox account. You should see the following message displayed in your browser:

        Your computer was successfully linked to your account

In your Utho terminal window, you'll see the following message displayed:

           This computer is now linked to Dropbox. Welcome User

## Testing the Link

Any files created within your `Dropbox` directory on your Utho will automatically sync with Dropbox.

1.  Go to your Dropbox folder:

        cd ~/Dropbox

2.  Create a new file and add text to it using the "echo" command.
        echo "testing...." > dropbox-test.txt

3.  Open your Dropbox account in your web browser to view the changes. You'll now find `dropbox-test.txt` in your files. Congratulations! Your Utho is now set up to run Dropbox.
