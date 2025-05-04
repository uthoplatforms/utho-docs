---
weight: 40
title: "FAQs"
title_meta: "FAQs"
description: "FAQs on Snapshots in Utho Cloud Platform"
keywords: ["cloud", "instances",  "ec2", "server", "graph"]
tags: ["utho cloud", "snapshots", "data backup", "volume snapshots", "cloud recovery"]
date: "2024-03-07T17:25:05+01:00"
lastmod: "2024-03-07T17:25:05+01:00"
draft: false
toc: true
aliases: ["/products/images/Snapshots/FAQs"]
icon: "globe"
tab: true
---

# **Snapshots : Frequently Asked Questions (FAQ)**

## 1. What is a snapshot in Utho Cloud? 🤔
A snapshot in Utho Cloud is a point-in-time backup of a server instance's state, including the operating system, configurations, and data. It allows you to preserve your server's exact state and restore it later if needed.

## 2. Why should I use snapshots in Utho Cloud? 💡
Snapshots are useful for backup and recovery, testing and development, scaling, and disaster recovery. They help you revert to a previous state if things go wrong and ensure business continuity.

## 3. How can I view my snapshots in Utho Cloud? 👀
You can view all your snapshots by logging into the Utho Cloud platform and navigating to the **Snapshot Listing Page**. From there, you can see details like the snapshot ID, name, size, and creation time.

## 4. How do I restore a snapshot in Utho Cloud? 🔄
To restore a snapshot, go to the [**Snapshot Listing Page**](https://console.utho.com/snapshots), find the snapshot you want to restore, and click the **Restore** button. Confirm your action in the popup, and the server will be restored to its state at the time the snapshot was taken.

## 5. Can I delete a snapshot in Utho Cloud? ❌
Yes, you can delete a snapshot by navigating to the [**Snapshot Listing Page**](https://console.utho.com/snapshots), locating the snapshot you want to remove, and clicking the **Delete** button. Once deleted, the snapshot cannot be recovered.

## 6. Is restoring a snapshot safe? 🛡️
Restoring a snapshot will overwrite the current state of your server with the state from the snapshot. Be sure to backup any important data before restoring, as recent changes will be lost.

## 7. How do I know the size of a snapshot? 📏
The size of a snapshot is displayed in the **Size** column on the Snapshot Listing Page. This tells you how much storage space the snapshot is using.

## 8. Can I create multiple snapshots for the same server? 🔄
Yes, you can create multiple snapshots for the same server, each representing a different point in time. This allows you to have multiple restore points for various purposes.

## 9. What happens if I delete a snapshot? 🗑️
When you delete a snapshot, it is permanently removed from the system. This does not affect the cloud server itself, only the snapshot data will be deleted.

## 10. Can I restore a snapshot to a different server? 🔄➡️
Currently, you can only restore a snapshot to the same server from which it was taken. The process is designed to maintain the server's state as it was during the snapshot.

## 11. How often should I create snapshots? ⏰
The frequency of creating snapshots depends on how often you make changes to your server. It is recommended to take snapshots before significant updates or changes to ensure you have a safe restore point.

## 12. How do I manage snapshot storage? 💾
You can manage snapshot storage by regularly reviewing and deleting unnecessary snapshots from the [**Snapshot Listing Page**](https://console.utho.com/snapshots). Deleting old snapshots can help free up space and reduce storage costs.

## 13. How long does it take to restore a snapshot? ⏳
The time it takes to restore a snapshot depends on the size of the snapshot and the server’s performance. Typically, the restoration process is quick, but it may take longer for large snapshots.

## 14. Can I automate snapshot creation in Utho Cloud? ⚙️
Currently, Utho Cloud does not offer automatic snapshot creation. However, you can manually create snapshots whenever necessary, especially before making significant changes to your server.

## 15. Can I view the history of a snapshot? 📜
The **Created At** column in the Snapshot Listing Page provides the creation date and time of each snapshot, allowing you to track the history of your snapshots.

## 16. What should I do if my snapshot fails to restore? 🚨
If a snapshot fails to restore, check the status and logs for any error messages. If the issue persists, contact Utho Cloud support for assistance with restoring the snapshot.

## 17. Can I restore a snapshot to the same server multiple times? 🔄🔁
Yes, you can restore the same snapshot to the same server as many times as needed, as long as the snapshot is still available.

## 18. Do I need to shut down my server to take a snapshot? 💻🛑
No, you don’t need to shut down your server to take a snapshot in Utho Cloud. Snapshots can be taken while the server is running, minimizing downtime.