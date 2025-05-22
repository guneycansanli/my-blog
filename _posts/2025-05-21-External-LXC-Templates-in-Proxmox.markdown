---
title: "Using External LXC Templates in Proxmox"
layout: post
date: 2025-05-21 11:00
image: ../assets/images/prx-lxc-ex/main.jpg
headerImage: true
tag:
    - proxmox
    - lxc
category: blog
author: guneycansanli
description: Using External LXC Templates in Proxmox
---

# Using External LXC Templates in Proxmox

Learn how to create Linux Containers (LXC) in **Proxmox VE** using alternative image repositories outside the default Proxmox options.

---

## Standard Method: Proxmox Templates

By default, Proxmox allows you to create containers using official templates. The process goes like this:

### Step 1: Refresh Template List

```bash
pveam update
```

![ex][1]

This command syncs your template list with the Proxmox servers.

### Step 2: Pick Your Storage

Navigate to your preferred storage volume (e.g. `local`, `local-lvm`, etc.), then go to:

```
CT Templates > Templates
```

![ex][2]

### Step 3: Download a Template

Browse the available images (Debian, Ubuntu, TurnKey, etc.) and download the one you need. This template becomes available when creating a new container.


![ex][3]

---

## Why Use External Template Sources?

Although Proxmox provides a rich selection, it doesn’t cover everything. Some distributions like **Linux Mint**, **Arch Linux**, or **Fedora** might not be listed.

You can grab additional LXC templates from other sources:

- [images.linuxcontainers.org](https://images.linuxcontainers.org/images/)
- [images.lxd.canonical.com](https://images.lxd.canonical.com/images/)

These mirrors host many more distributions and variations.

---

## Example: Using a Linux Mint Image

Let’s walk through using a Linux Mint container image hosted externally.

### Step 1: Navigate the Repository

Visit the repo:

```
https://images.linuxcontainers.org/images/
```

Then choose:

```
Mint > [VERSION] > amd64 > default > [DATE]
```

Look for the file named:

```
rootfs.tar.xz
```

> "default" images are for general-purpose containers. "cloud" images include cloud-init tools.

![ex][4]

---

## Download the Template to Proxmox

1. **Copy the link** to the `rootfs.tar.xz` file. **https://images.linuxcontainers.org/images/mint/wilma/amd64/default/20250521_08%3A51/rootfs.tar.xz**
2. In the Proxmox web UI, go to:

   ```
   CT Templates > Download from URL
   ```

3. Paste the copied link into the URL field.
4. Hit **Query URL** – it will fetch the metadata.
5. Add a description so you remember what the image is for.
6. Click **Download**.

Once finished, you'll see **TASK OK**.

![ex][5]

![ex][6]

---

## Create a New Container

1. Go to **Create CT** in the Proxmox dashboard.
2. Under **Template**, select the external image you just downloaded.
3. Configure your container as usual (hostname, disk, CPU, etc.)
4. Launch the container.
5. Login as `root` with the password or SSH key provided during setup.


![ex][7]

---

## Managed vs. Unmanaged Containers

Proxmox includes optimizations for official containers – such as OS detection and startup tweaks. These don't always apply to external templates.

To avoid conflicts:

- Edit your container config at:
  
  ```
  /etc/pve/lxc/<CT-ID>.conf
  ```

- Add or change:

  ```
  ostype: unmanaged
  ```

This tells Proxmox not to try and auto-adjust the OS internals.

---

## Summary

Using external templates in Proxmox unlocks a broader ecosystem of Linux distributions. Whether you're looking for more flexibility, specific versions, or niche distros – this method gives you that power.

---

**References:**

- [Proxmox Documentation](https://pve.proxmox.com/wiki/Linux_Container)
- [Linux Containers Image Server](https://images.linuxcontainers.org/images/)


---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/prx-lxc-ex/ex-1.jpg
[2]: ../assets/images/prx-lxc-ex/ex-2.jpg
[3]: ../assets/images/prx-lxc-ex/ex-3.jpg
[4]: ../assets/images/prx-lxc-ex/ex-4.jpg
[5]: ../assets/images/prx-lxc-ex/ex-5.jpg
[6]: ../assets/images/prx-lxc-ex/ex-6.jpg
[7]: ../assets/images/prx-lxc-ex/ex-7.jpg




