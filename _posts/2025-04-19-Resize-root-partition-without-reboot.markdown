---
title: "Resize Linux Root Partition on a Live System Without Rebooting"
layout: post
date: 2025-04-19 11:00
image: ../assets/images/resize-disk/main.jpg
headerImage: true
tag:
  - linux
  - partitioning
  - root
  - resize
category: blog
author: guneycansanli
description: A step-by-step, reboot-free guide to resizing the root partition on a live Linux system using `parted`, `resize2fs`, and more.
---

## Why Resize Root Without Reboot?

Running out of space on your root partition and can't afford downtime? On modern Linux systems, it's totally possible to **safely resize your root partition while the system is live** — no reboot required.

This guide is tailored for **Debian-based systems** and uses native tools like `parted`, `resize2fs`, and `partprobe` to help you make use of unused disk space without interruption.

---

## Prerequisites

Before you begin, make sure the following are in place:

- The disk has **unallocated space**.
- You have **root or sudo privileges**.
- Installed tools: `parted`, `resize2fs` or `xfs_growfs`, `partprobe`, `lsblk`, `df`, `kpartx` (optional).
- **Full system backup** or snapshot (mandatory!).

> **Warning:** Always back up your data. Resizing partitions carries risk!

---

## Step 1: Inspect the Current Partition Layout

Run the following commands to understand your current setup:

```bash
lsblk
lsblk -f (for FSTYPE or df -T)
fdisk -l <disk>
```

### Sample Outputs:

```
NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
vda    252:0    0   40G  0 disk 
├─vda1 252:1    0    1M  0 part 
└─vda2 252:2    0   40G  0 part /


root@ubuntu-jammy-server:~# df -T
Filesystem     Type  1K-blocks     Used Available Use% Mounted on
tmpfs          tmpfs    201108     1112    199996   1% /run
/dev/vda2      ext4   41102636 16290900  22908176  42% /
tmpfs          tmpfs   1005536        0   1005536   0% /dev/shm
tmpfs          tmpfs      5120        0      5120   0% /run/lock
tmpfs          tmpfs    201104        4    201100   1% /run/user/1000

```

![root-disk][1]

You can extend Your disk space now and check again to verify disk size increased

![root-disk][2]

You can see now my vda disk +5 GB increased but my root partition is still 40GB I can extend 5GB now. 

---

## Step 2: Back Up Your System

Back up everything before proceeding.

### Option 1: Snapshot (if supported)

```bash
# LVM
lvcreate --snapshot --name root_snap /dev/mapper/ubuntu--vg-root --size 5G
```

### Option 2: Rsync

```bash
rsync -aAXv / /mnt/backup/
```

---

## Step 3: Resize the Partition

### Start `parted`

```bash
sudo parted /dev/XXX
```

### Show Current Layout

```bash
(parted) print
```

### Resize Partition 1

```bash
(parted) resizepart 1 100%
```

>  `resizepart` is available in `parted 3.0+`. If you see `resize has been removed`, you’re using an outdated or incompatible version.

### Confirm Changes

```bash
(parted) print
(parted) quit
```

![root-disk][3]

---

## Step 4: Inform the Kernel

Let the kernel know the partition has changed:

```bash
sudo partprobe
```

If nothing is returned — that’s good. It means no errors.

### Optional for Device Mapper Setups

```bash
sudo kpartx -u /dev/sda
```

---

## Step 5: Resize the Filesystem

Now that the partition is larger, expand the actual filesystem.

### For `ext4`:

```bash
sudo resize2fs /dev/XXXX
```

### For `xfs`:

```bash
sudo xfs_growfs /
```

> `xfs_growfs` can only expand a mounted XFS filesystem, which makes it ideal for root partitions.

---

## Step 6: Verify Everything Worked

### Check New Disk Usage:

```bash
df -h /
lsblk
```

You should now see `/dev/XXXX` showing a size close to your total disk capacity.


![root-disk][4]

---

## Optional: Add a Swap Partition

If you have extra space and no swap, you can add one now.

### Create New Swap Partition

```bash
sudo parted /dev/XXXX mkpart primary linux-swap XXGB XXGB
```

### Format and Activate Swap

```bash
sudo mkswap /dev/XXXX
sudo swapon /dev/XXXX
```

### Make Swap Persistent

```bash
echo '/dev/XXXX none swap sw 0 0' | sudo tee -a /etc/fstab
```

### Confirm Swap Is Enabled

```bash
swapon --show
free -h
```

---

## Done — No Reboot Needed!

You just resized the root partition of a live system **without taking it offline**.

### Final Check:

```bash
uptime
```

> The uptime confirms the system remained live the whole time.

---

## Further Reading

- [`man parted`](https://man7.org/linux/man-pages/man8/parted.8.html)
- [`man resize2fs`](https://man7.org/linux/man-pages/man8/resize2fs.8.html)
- [`Debian Partitioning Guide`](https://wiki.debian.org/)

---

Thanks for reading!

—

**Guneycan Sanli**

[1]: ../assets/images/root-disk/root-disk-1.jpg
[2]: ../assets/images/root-disk/root-disk-2.jpg
[3]: ../assets/images/root-disk/root-disk-3.jpg
[4]: ../assets/images/root-disk/root-disk-4.jpg
