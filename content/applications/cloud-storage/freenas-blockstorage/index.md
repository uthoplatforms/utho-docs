---
slug: "Using FreeNAS with Block Storage"
description: "FreeNAS is storage software for network-attached storage (NAS), managed through a web interface. This guide will help you install FreeNAS and link it to a Block Storage Volume."
keywords: ["zfs","freenas","block storage","nas"]
modified: 2024-03-19
modified_by:
  name: Utho
title: "Setting Up FreeNAS on a Utho with Block Storage"
published: 2024-03-19
authors: ["Pawan Kumar"]
---

Network-attached storage (NAS) enables multiple devices to access stored data as if it were locally available. FreeNAS is NAS software based on FreeBSD, which you can configure through a browser interface.

This guide demonstrates how to install FreeNAS on a Utho and connect a Block Storage Volume. This setup allows you to access both FreeNAS and the Storage Volume from your computer, phone, or tablet from almost anywhere in the world.

{{< note type="alert" respectIndent=false >}}

Currently, FreeNAS is not officially supported by Utho. Consequently, features such as the Utho Backup Service and Lish will not be accessible to you.

{{< /note >}}

## Prepare Your Utho

1.  Create a Utho in your chosen data center with a minimum of 8GB RAM and at least 11GB of available disk space. FreeNAS suggests having 16GB of RAM, especially for media servers.

2.  Turn off the [Lassie Shutdown Watchdog](/docs/products/compute/compute-instances/guides/monitor-and-maintain/#configure-shutdown-watchdog/) to avoid automatic restarts of your Utho without your intervention. You can disable Lassie in the **Settings** tab of the Utho Manager under **Shutdown Watchdog**.

3.  [Create two disks](/docs/products/compute/compute-instances/guides/disks-and-storage/#creating-a-disk):

    1.  **Label:** Installer
        * **Type:** unformatted / raw
        * **Size:** 1024

    2.  **Label:** FreeNAS
        * **Type:** unformatted / raw
        * **Size:** Can be set to use remaining disk. At least 10240MB

4.  [Create two configuration profiles](/docs/products/compute/compute-instances/guides/configuration-profiles/#creating-a-configuration-profile) Using the provided settings, within each profile, deactivate all options under **Filesystem/Boot Helpers**.

    1.  **Label:** Installer
        * **Kernel:** Direct Disk
        * **/dev/sda:** FreeNAS
        * **/dev/sdb:** Installer
        * **root / boot device:** Standard /dev/sdb
        * **Filesystem/Boot Helpers:** Select No for each Helper

    2. **Label:** FreeNAS
        * **Kernel:** Direct Disk
        * **/dev/sda:** FreeNAS
        * **root / boot device:** Standard /dev/sda
        * **Filesystem/Boot Helpers:** Select No for each Helper

## Generating an Installer Disk

1.  Boot into **Rescue Mode** with the installer disk mounted to `/dev/sda` and access your Utho via Lish from the dashboard of your Utho in the Utho Cloud Manager.

2.  While in Rescue Mode, execute the following command to designate the [latest FreeNAS release](http://www.freenas.org/download-freenas-release/)  (11.1 at the time of writing) as a variable:

        iso=https://download.freenas.org/11/latest/x64/FreeNAS-11.1-U4.iso

3.  Execute the update-ca-certificates program to enable secure downloads:

        update-ca-certificates

4.  Download the FreeNAS ISO image and extract it to the Installer disk:

        curl $iso | dd of=/dev/sda

5.  After the command completes, restart your system into the **Installer profile**:

6.  Navigate to the Utho Cloud Manager and open the dashboard for your Utho.

7.  Click on the **Launch Console** link to access the [Glish](/docs/products/compute/compute-instances/guides/glish/) console and initiate the installation process.

## Install FreeNAS

1.  Press the **Enter** key to proceed with the installation/upgrade.
2.  Press the **Spacebar** to choose the FreeNAS disk (identified as `da0` in this screenshot), then hit **Enter**:
3.  During the installation process, a warning appears indicating that all data on the disk will be erased. Press **Enter** to proceed.
4.  Enter a root password, then move to the next field by pressing the **Tab** key to confirm the password, and finally press **Enter** to proceed.
5.  Press **Enter** to Boot via BIOS.
6.  As there's no media to remove, simply press Enter to acknowledge the successful installation message.
7.  Press **4** to choose "Shutdown System," then press **Enter** to end the session. Close the Glish window afterward.
8.  In the Manager, initiate the shutdown process for your Utho.

## Booting and Configuring FreeNAS

1.  Choose the FreeNAS configuration profile and click on **Boot**.

In FreeNAS, both SSH and Lish are disabled. Utilize Glish to monitor the initial boot, which may take a few minutes. Once the boot process is complete, close Glish and proceed to the next step to configure FreeNAS via the web interface.

2.  Open a web browser and enter the IP address of your Utho. Log in with the username `root` and the password you set in Step 4 of the previous section. If any popup menu appears upon logging in, close it.

3.  Click on the Network icon and fill in the **network** information using the Default Gateways and DNS Resolvers provided in the Networking tab of the Utho Cloud Manager. Utilize the DNS Resolvers details to complete the Nameserver fields. Remember to click **Save** before proceeding.

4.  Navigate to the **Interfaces** section within the Network tab, then select **Add Interface**. Provide a name for the interface, enable DHCP, and finally, click **OK**.

## Adding a Block Storage Volume to FreeNAS

1. [Add or attach a Block Storage Volume](/docs/products/storage/block-storage/guides/manage-volumes/) to your Utho by following the steps outlined in the Add or attach a Block Storage Volume guide. Once the Block Storage Volume is attached, the Utho Manager will display command-line instructions for mounting it from your Utho, but you can ignore these.

2.  Reboot your Utho using the Utho Manager. After a few minutes, reopen Glish from the dashboard. You can monitor the progress of the reboot in Glish.

### Formatting the Block Storage Volume as ZFS

{{< note type="alert" respectIndent=false >}}

Formatting will delete all data on the volume.

{{< /note >}}

1.  Log back into the web interface for FreeNAS.

2.  Click on the **Storage** icon located at the top, then select **View Disks** to verify that FreeNAS detected the Block Storage Volume.

3.  Go back to the **Storage** tab and click on **Volume Manager**. Enter a name for the Volume, and under Available Disks, click the + next to the Block Storage Volume. Choose **Stripe** under Volume layout, and finally, click **Add Volume** to format and attach the Volume.

### Adjust Permissions, Share the Volume, and Finish Configuration

1.  Click the Wizard icon:

2.  Choose your language, keyboard map, and timezone, then click **Next**:

3.  If prompted for the Directory Service, simply press Next to skip this step.

4.  Provide a name and purpose for the Share. Click on **Ownership** to adjust Permissions, ensuring the client can access and manage access. For more details on available options, refer to the [official FreeNAS documentation](http://doc.freenas.org/11/storage.html#change-permissions). Once configured, click **Add**, and then proceed by clicking **Next**.

5.  Set up email notification settings or press **Next** to skip this step and proceed.

6.  Click **Confirm** to allow the Wizard to finalize the remaining configuration.

## Enabling SSH Root Login (Optional)

By default, FreeNAS has SSH disabled, which is a more secure setup but can hinder troubleshooting from the command line.

1.  Click on the **Services** icon, followed by the wrench icon on the SSH line:

2.  Tick the box next to **Login as Root with password** and click **OK**. If you want to maintain this setting through future reboots, also check the box next to **Start on Boot**. Finally, click **Start Now**.

Utilize the same username and password as you did for accessing the web interface.

## Next Steps

With FreeNAS now running and [connect to it from your local machine](http://doc.freenas.org/11/sharing.html), set up a [Plex server](http://www.freenas.org/blog/plex-on-freenas/), or utilize various other platforms using [plugins](http://doc.freenas.org/11/plugins.html#available-plugins).
