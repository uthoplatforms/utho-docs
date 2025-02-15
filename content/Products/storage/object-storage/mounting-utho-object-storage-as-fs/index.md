---
weight: 20
title: "Mounting Utho's Object Storage as a Filesystem"
title_meta: "Mount Utho's Object storage as a filesystme"
description: "This guide provides detailed instructions for installing and configuring Rclone to mount an S3-compatible object storage on your system. You’ll also learn how to verify the installation, configure the connection, and mount your object storage locally."
keywords: ["utho", "object storage"]
tags: ["utho platform", "cloud", "managed database", "object torage"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/Products/storage/object-storage/mounting-utho-object-storage-as-fs"]
icon: "node"
tab: true
homecard: true

---

# Mounting Utho's Object Storage as a Filesystem

This guide provides detailed instructions for installing and configuring Rclone to mount an S3-compatible object storage on your system. You’ll also learn how to verify the installation, configure the connection, and mount your object storage locally.

## **1. Install Rclone on Your System**

#### **macOS:** 
On macOS, Rclone can be easily installed using Homebrew. Open a terminal and run the following command:

```bash
brew install rclone
```

#### **Windows:**

Download Rclone from the official site (https://rclone.org/downloads/).
1. Extract the Rclone zip file and move the Rclone executable to a folder (e.g., C:\rclone\).Add the Rclone folder to your system's **PATH** environment variable by running the following command in your command prompt:

```CMD
 set PATH=%PATH%;C:\rclone\
```
### **Linux:**
To install Rclone on Linux, you can use **Snap** to install it quickly:

```bash
sudo snap install rclone

```

Alternatively, you can follow official instructions for manual installation.

## **2. Install Dependencies for FUSE (Windows Only)**
On Windows, Rclone requires **WinFSP** for mounting remote storage as a local filesystem. Download and install **WinFSP** from this [link](https://winfsp.dev/rel/). This is required to enable FUSE functionality, which allows Rclone to mount the object storage.


## **3. Verify Rclone Installation**

After installation, you can verify Rclone is correctly installed by running the following command in your terminal or command prompt:

```bash
rclone --version
```

This command will return the version of Rclone installed, ensuring everything is set up correctly.

## **4. Configure Rclone with S3-Compatible Object Storage**

Once Rclone is installed, you need to configure it to connect with your S3-compatible object storage. Follow these steps:

Open a terminal or command prompt and run the following command:bashCopy coderclone config

Follow these steps to set up Rclone for connecting to S3-compatible object storage:

---

### Step 1: Start Rclone Interactive Configuration
Run the Rclone configuration command:
   ```bash
   rclone config
   ```
In the interactive setup, select the following options:
   - **`n`**: Create a new remote connection.
   - **Enter a name for your remote** (e.g., `uthostorage`).
   - Choose **`s3`** from the list of storage types.

---

### Step 2: Provide S3 Configuration Details
Enter the Utho Object Storage access details when prompted:
   - **Storage provider**: Select the S3-compatible provider from the list.  
     - For AWS, select **"AWS S3"**.
   - **Access Key**: Enter your **Access Key ID** for the S3 storage.
   - **Secret Key**: Enter your **Secret Access Key**.
   - **Endpoint**: Enter the **S3 endpoint URL** of the storage provider.  
     - For AWS S3, this can be left blank.  
     - For third-party S3-compatible services, provide the appropriate endpoint (e.g., `innoida.utho.io`).

---

### Step 3: Configure Optional Settings

Additional optional settings include:
   - **Region**: Leave the region blank.
   - **Storage class**: Select the default storage class (e.g., `STANDARD`).
   - **Advanced Config**: Leave this blank unless you have specific requirements, such as encryption.

---

### Step 4: Save and Exit
After entering all required details:

Select **`y`** to save the configuration

## 5. Creating a Mountpoint

#### ***For Linux and macOS***

Before mounting your object storage, create a local directory where you will mount the storage. Use the following command to create the directory:

```
mkdir ~/objectstore
```

This will create a folder named objectstore in your home directory (you can change this path as needed).

## **6. Mount the Object Storage**

Now that you have configured Rclone, you can mount the S3-compatible object storage to your local system. This will allow you to interact with the object storage as though it were part of your file system.

#### **macOS/Linux:**
Use the following command to mount the S3 object storage:

```
rclone mount uthostorage: ~/objectstore --vfs-cache-mode writes
```
- Replace ***uthostorage*** with the name you gave your remote storage.
- The --vfs-cache-mode writes flag enables write-back caching for better performance during uploads.

**Note**: The mount will remain active as long as the terminal session is open. To unmount, you can close the terminal or use Ctrl + C to stop the process.

#### **Windows:**

On Windows, you can mount the S3 object storage to a drive letter using the following command:

```
rclone mount uthostorage:mybucket U: --vfs-cache-mode writes
```
Replace **mys3storage** with your configured remote storage name.- Replace mybucket with your actual S3 bucket name.- Replace U: with the desired drive letter.You will now have the Utho Object storage mounted as U: drive, and you can interact with it directly from File Explorer or the command prompt.

## **7. Additional Mount Options**

Rclone provides several options to customize your mount. Here are a few commonly used ones:

***--vfs-cache-mode writes***: This ensures that all write operations are cached locally before being uploaded, improving performance.

***--allow-other***: Allows other users on the system to access the mounted storage (Linux/macOS only).

***--read-only***: Mounts the storage as read-only, preventing any changes to the data.For more options, check the Rclone mount documentation.

## **8. Automating the Mount (Optional)**
For automatic mounting on startup, you can create a script or service depending on your operating system.

#### **macOS/Linux:**
You can add the mount command to your system’s startup script or use systemd (Linux) to create a service that mounts the storage on boot.

#### **Windows:**
You can create a scheduled task that runs the rclone mount command at startup to mount your object storage automatically.

## **9. Troubleshooting**
If you face any issues with mounting or accessing the storage:

**Permission errors**: Ensure that your access and secret keys are correct and that your IAM user has the necessary permissions to access the bucket.- 

**Mount errors**: Double-check the endpoint URL and make sure your bucket is in the correct region.- 

**Performance issues**: Try enabling the --vfs-cache-mode writes option for better performance during file uploads/downloads.