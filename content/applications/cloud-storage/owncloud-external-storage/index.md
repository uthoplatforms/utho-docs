---
slug: ownCloud External Storage Integration Guide
description: "This guide demonstrates configuring ownCloud to utilize external storage, preventing your ownCloud instance from experiencing space constraints"
keywords: ['ownCloud external storage', 'ownCloud s3']
tags: ['marketplace']
published: 2024-03-20
modified_by:
  name: Utho
title: "Setting up ownCloud with External Storage"
title_meta: "Configuring ownCloud for External Storage: A Step-by-Step Guide"
authors: ["Pawan Kumar"]
---
ownCloud is an open-source solution for hosting and sharing files. It enables you to synchronize files to a Utho-hosted instance, allowing collaboration with users worldwide. With ownCloud, you can create your own personal cloud, offering various installable apps and features for security, communication, and convenience.

Among ownCloud's features is the capability to link an instance to Utho Object Storage. This option allows you to expand the storage capacity of ownCloud, preventing your instance from running out of space. This feature proves particularly beneficial as your ownCloud instance grows with more collaborators and increasing data volume.

## Before You Begin

1. Make sure you have a running instance of ownCloud deployed on your Utho.

    {{< note >}}

To automatically install ownCloud on a Compute Instance, you can deploy ownCloud Server through the Utho Marketplace.

    {{< /note >}}

1. Acquire an [enterprise license for ownCloud](https://doc.owncloud.com/server/admin_manual/enterprise/installation/install.html) to activate the required external storage app.

1. Create a set of Object Storage access keys.

{{< note >}}

To connect ownCloud to an Object Storage service, you need to install two external storage applications:

- Begin by installing the ownCloud Marketplace external storage app.
- Once installed, you can configure a connection to your Utho Object Storage bucket.

{{< /note >}}

## Configuring ownCloud

### Installing the External Storage App

To link to a Utho Object Storage bucket, you need to install the *External Storage: S3 app*.

1. Sign in to ownCloud as an admin user.

1. Select the **Files** button located in the upper left corner, then click on **Market**.

1. Navigate to Storage in the left sidebar and click on it.

1. Click on **Object Storage Support** and then select **INSTALL**.

1. Return to the Storage apps listing and select **External Storage: S3**. Then, click on **INSTALL** when prompted.

## Setting Up Utho Object Storage as External Storage

With external storage enabled for ownCloud, you now need to create an external storage mount between ownCloud and your Utho bucket. For this you need the following information:

- **Bucket**: This is a unique name assigned to your external bucket. Ensure it's not already in use in the selected data center region.

- **Hostname**: This is the hostname for the Object Storage region you've chosen, typically in the format us-east-1.uthoobjects.com or similar.

- **Region**: The region you've designated for your bucket, such as us-east-1 or similar.

- **Access Key**: The access key value you generated in the Utho Cloud Manager.

- **Secret Key**: The secret key value you generated in the Utho Cloud Manager.

1. Click on your username dropdown and choose **Settings**. In the Settings window, select **Storage** from the left sidebar.

1. Check the box for **Enable external storage**.

1. From the **Add storage** dropdown, choose **Amazon S3 Compatible**.

In the resulting window, ensure to configure the external storage mount with the information provided above. Additionally, set the following options:

- **Enable SSL** Enabled.

- Port Set to `443`.

- **Enable Path Style** Disabled.

Once all fields are filled out, ownCloud automatically attempts to establish the connection. When configured correctly, a green circle appears to the left of the folder name.

Your settings are automatically saved.

## Accessing your External Storage Mount

To access your external storage mount, click on the **Files** icon located on the ownCloud toolbar. You'll notice a new folder with the same name you specified for the Bucket option in the External Storage configuration.

Open that folder, and you're all set to start storing files in your external storage. Simply upload files to the folder, and they'll be automatically saved to your Utho Object Storage instance. Additionally, the files you upload are accessible through the Utho Cloud Manager, under the Object Storage section.
