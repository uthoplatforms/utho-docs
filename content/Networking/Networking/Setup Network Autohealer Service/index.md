---
title: "Server Network Auto‑Heal Service"
date: "2025-12-13"
---

# Server Network Auto‑Heal Service (ifup Watchdog)

This document explains how to set up a **self‑healing network service** on a Linux Server where the network interface occasionally goes down and requires a manual `ifup` to recover.

The solution uses a **systemd service** that continuously monitors connectivity and automatically runs `ifdown` / `ifup` when the network is detected as down.

---

## Problem Statement

- Network interface goes DOWN intermittently
- Outbound connectivity drops
- Manual recovery is required using:
  ```bash
  ifup eth0
  ```

This impacts application availability and requires human intervention.

---

## Solution Overview

We create a lightweight **systemd watchdog service** that:

- Runs continuously in the background
- Pings a reliable external IP (e.g. `1.1.1.1`)
- If ping fails:
  - Brings the interface down
  - Brings the interface back up
- Automatically restarts if it ever crashes

This provides **automatic recovery without rebooting the Server**.

---

## Prerequisites

- Linux Server (CentOS / RHEL / Rocky / Alma / similar)
- `ifup` / `ifdown` available (`network-scripts` package)
- Root access
- Interface name known (example: `eth0`)

> ⚠️ Recommended: Disable `NetworkManager` on servers to avoid conflicts.

---

## One‑Command Setup

Run the following **single command** as root:

```bash
bash -c 'cat >/etc/systemd/system/netheal.service <<"EOF"
[Unit]
Description=Auto-heal network (ifup on failure)
After=network.target

[Service]
Type=simple
ExecStart=/bin/bash -lc '\''while true; do ping -c1 -W2 1.1.1.1 >/dev/null 2>&1 || ( /sbin/ifdown eth0 >/dev/null 2>&1; /sbin/ifup eth0 >/dev/null 2>&1 ); sleep 30; done'\''
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF
systemctl daemon-reload
systemctl enable --now netheal.service'
```

---

## Customization

### Change Interface Name

If your interface is not `eth0` (for example `ens3` or `ens160`), update this part:

```bash
/sbin/ifdown eth0
/sbin/ifup eth0
```

### Change Health Check Target

You can replace `1.1.1.1` with any reliable endpoint:

- Gateway IP
- Public DNS (8.8.8.8)
- Internal monitoring IP

---

## Service Management

### Check Status

```bash
systemctl status netheal.service -l
```

### View Logs

```bash
journalctl -u netheal.service -f
```

### Stop Service

```bash
systemctl stop netheal.service
```

### Disable Service

```bash
systemctl disable netheal.service
```

---

## Why This Works

- Avoids Server reboot
- Works even when DHCP / carrier state breaks
- Independent of applications
- systemd guarantees restart
- Minimal CPU and memory usage

---

## Notes & Best Practices

- Prefer **static IPs** for production servers
- Disable `NetworkManager` on backend / app Servers
- Use `NM_CONTROLLED=no` in ifcfg files

---

## Summary

This auto‑heal service ensures that **temporary network drops do not require manual intervention**, improving Server stability and uptime.

It is safe, simple, and production‑friendly.

---
