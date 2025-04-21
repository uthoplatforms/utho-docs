---
weight: 120
title: "Update and Restore snapshot"
title_meta: "Update and Restore snapshot"
description: "Manage your cloud instance using simply clicks on utho platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho platform","cloud"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/compute/cloud/how-tos/Update and Restore snapshot"]
icon: "snapshot"
tab: true
---
Utho offers a snapshot feature to capture and manage point-in-time backups of your cloud server, providing options to take new snapshots, view current snapshots, and perform snapshot-related actions.

### Take New Snapshot

To create a new snapshot of your cloud server:![1718873381503](image/index/1718873381503.png)

1. **Power Off Recommendation** : It is recommended to power off your cloud server before taking a snapshot to ensure data consistency.
2. **Snapshot Cost** : Snapshots are charged based on the disk size used, at a rate of Rs. 4 per GB per month.

### Taking a Live Snapshot

1. **Click on "Take Live Snapshot"** : Click the "Take Live Snapshot" option to initiate the live snapshot process.
2. **Enter Snapshot Name** : ![1718873563147](image/index/1718873563147.png)In the drawer that appears, provide a name for your snapshot in the input field labeled "Snapshot Name".
3. **Create Snapshot** : Click the "Create" button to create the snapshot.

### Snapshot Actions

Upon clicking "Create", the snapshot will be generated and added to the snapshot table, displaying the following details:![1718873670172](image/index/1718873670172.png)

* **Snapshot Name** : The name provided for the snapshot.
* **Snapshot Size** : The size of the snapshot.
* **Created At** : The timestamp indicating when the snapshot was created.
* **Actions** : Options to restore or delete the snapshot as needed.
  * **Restore** : Restore the cloud server to a previous state using a selected snapshot.
  * **Delete** : Remove unnecessary snapshots to manage storage efficiently.

Workflow

1. **Take New Snapshot** : Click on "Take New Snapshot" to capture a point-in-time backup of your cloud server's current state.
2. **Manage Snapshots** : Once snapshots are created, manage them including restoring the server from a snapshot or deleting outdated snapshots.

Utho's snapshot management feature ensures data protection and recovery options are readily available to maintain the integrity and availability of your cloud server.

---
