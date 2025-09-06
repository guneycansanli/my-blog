---
title: "ZRAM"
layout: post
date: 2025-09-05 11:00
image: ../assets/images/zram/main.jpg
headerImage: true
tag:
    - linux
    - zram
    - swap
category: blog
author: guneycansanli
description: Running Out of RAM on Linux? Use ZRAM Before Upgrading
---

# Running Out of RAM on Linux? Use ZRAM Before Upgrading

If your Linux system often freezes, swaps heavily, or even invokes the OOM (Out of Memory) killer, you may be thinking about buying more RAM. But before you spend money, try **ZRAM** — a kernel feature that creates a compressed swap device in RAM, effectively extending your memory capacity while keeping performance snappy.

---

## What This Guide Covers
- What ZRAM is and why it helps  
- How to install and configure ZRAM on Debian/Ubuntu  
- Optional tuning via sysctl for best results  

---

## What is ZRAM?

ZRAM is a Linux kernel module that creates a compressed block device in RAM. This device is then used as swap space, which helps your system avoid slow disk-based swap (especially on HDDs or SSDs).

Because the data is compressed, you can often fit **3–4x more into RAM** than physically available.

On modern CPUs, **Zstd compression** is recommended because it strikes the best balance between compression ratio and speed.

### Example:
With **16 GB RAM** and **8 GB allocated to ZRAM (zstd)**, you may effectively hold **32–40 GB of data** in memory before swapping to disk.

<table style="border-collapse: collapse; width: 70%; margin: 1em auto; font-family: Arial, sans-serif;">
  <caption style="caption-side: top; font-size: 1.2em; font-weight: bold; margin-bottom: 8px;">
    ZRAM Compression Algorithm Benchmarks
  </caption>
  <thead>
    <tr style="background-color: #2563eb; color: #fff;">
      <th style="padding: 8px; border: 1px solid #ddd;">Algorithm</th>
      <th style="padding: 8px; border: 1px solid #ddd;">Time</th>
      <th style="padding: 8px; border: 1px solid #ddd;">Data</th>
      <th style="padding: 8px; border: 1px solid #ddd;">Compressed</th>
      <th style="padding: 8px; border: 1px solid #ddd;">Ratio</th>
    </tr>
  </thead>
  <tbody>
    <tr style="background-color: #f9fafb;">
      <td style="padding: 8px; border: 1px solid #ddd;">lzo</td>
      <td style="padding: 8px; border: 1px solid #ddd;">4.6s</td>
      <td style="padding: 8px; border: 1px solid #ddd;">1.1G</td>
      <td style="padding: 8px; border: 1px solid #ddd;">387.8M</td>
      <td style="padding: 8px; border: 1px solid #ddd;">2.68</td>
    </tr>
    <tr>
      <td style="padding: 8px; border: 1px solid #ddd;"><strong>lzo-rle</strong></td>
      <td style="padding: 8px; border: 1px solid #ddd;">4.5s</td>
      <td style="padding: 8px; border: 1px solid #ddd;">1.1G</td>
      <td style="padding: 8px; border: 1px solid #ddd;">388M</td>
      <td style="padding: 8px; border: 1px solid #ddd;">2.68</td>
    </tr>
    <tr style="background-color: #f9fafb;">
      <td style="padding: 8px; border: 1px solid #ddd;"><strong>lz4</strong></td>
      <td style="padding: 8px; border: 1px solid #ddd;">4.5s</td>
      <td style="padding: 8px; border: 1px solid #ddd;">1.1G</td>
      <td style="padding: 8px; border: 1px solid #ddd;">403.4M</td>
      <td style="padding: 8px; border: 1px solid #ddd;">2.58</td>
    </tr>
    <tr>
      <td style="padding: 8px; border: 1px solid #ddd;">lz4hc</td>
      <td style="padding: 8px; border: 1px solid #ddd;">14.6s</td>
      <td style="padding: 8px; border: 1px solid #ddd;">1.1G</td>
      <td style="padding: 8px; border: 1px solid #ddd;">362.8M</td>
      <td style="padding: 8px; border: 1px solid #ddd;">2.87</td>
    </tr>
    <tr style="background-color: #f9fafb; font-weight: bold; color: #065f46;">
      <td style="padding: 8px; border: 1px solid #ddd;">zstd</td>
      <td style="padding: 8px; border: 1px solid #ddd;">7.8s</td>
      <td style="padding: 8px; border: 1px solid #ddd;">1.1G</td>
      <td style="padding: 8px; border: 1px solid #ddd;">285.3M</td>
      <td style="padding: 8px; border: 1px solid #ddd;">3.96</td>
    </tr>
  </tbody>
</table>

*Source: [linuxreviews.org/Zram](https://linuxreviews.org/Zram)*

---

## Step 1: Check Your Current RAM Usage

Before adding ZRAM, check how much RAM you’re using:

```bash
free -h
```

![zram][1]

This shows your total, used, and available memory. You’ll later compare this after enabling ZRAM.

---

## Step 2: Install ZRAM on Debian/Ubuntu

On Debian 12+, Ubuntu 22.04+, and derivatives (Linux Mint, etc.), install the ZRAM tools:

```bash
sudo apt update
sudo apt install zram-tools
```

This package automatically manages ZRAM setup on boot.

Verify the module is loaded:

```bash
lsmod | grep zram
```

![zram][2]

## Step 3: Configure ZRAM Swap

Edit the configuration file:

```bash
sudo vim /etc/default/zramswap
```

Recommended settings for modern CPUs:

```bash
ALGO=zstd
PERCENTAGE=50
PRIORITY=100
```

![zram][3]

- **ALGO=zstd** → best compression ratio and speed
- **PERCENTAGE=50** → use 20% of total RAM for ZRAM (safe default; if <8 GB RAM, increase to 50%)
- **PRIORITY=100** → ensures ZRAM swap is used before slower disk swap

Save and restart the service:

```bash
sudo systemctl restart zramswap
```

Check if ZRAM is active:

```bash
zramctl
swapon --show
```

![zram][4]

You should see /dev/zram0 listed.


## Step 4: Tune Linux Memory Behavior (Optional but Recommended)

Linux defaults may still cause unnecessary disk swapping. Adjust kernel parameters with a sysctl config:

```bash
sudo vim /etc/sysctl.d/99-zram-tweaks.conf
```

Add:

```bash
vm.swappiness=80
vm.vfs_cache_pressure=50
vm.dirty_background_ratio=5
vm.dirty_ratio=10
```

Apply changes:

```bash
sudo sysctl --system
```

![zram][5]

Explanation of Key Settings:

- vm.swappiness=80 → balance RAM vs ZRAM usage (10–100 depending on workload)
- vm.vfs_cache_pressure=50 → keeps file/dentry cache longer for performance
- vm.dirty_background_ratio=5 → smooth background disk writes
- vm.dirty_ratio=10 → forces flush at 10% dirty RAM, preventing big I/O spikes

---

## Step 5: Monitor ZRAM Usage

To monitor usage:

```bash
free -h
zramctl
```

![zram][6]

You should see increased available memory and ZRAM usage instead of immediate disk swapping.

## Real-World Results

With ZRAM enabled and tuned:

✅ No more sudden browser crashes due to OOM killer
✅ Smoother multitasking with 100+ browser tabs and many apps open
✅ Less SSD wear since disk swap is used much less


# Conclusion

If you’re hitting memory limits on Linux, try ZRAM before buying more RAM:

- Lightweight and kernel-native
- Extends usable memory
- Improves responsiveness
- Reduces SSD/HDD swap usage

ZRAM is especially useful on:

- Desktops/laptops with 4–16 GB RAM
- Systems running heavy browsers, IDEs, or VMs

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/zram/zram-1.jpg
[2]: ../assets/images/zram/zram-2.jpg
[3]: ../assets/images/zram/zram-3.jpg
[4]: ../assets/images/zram/zram-4.jpg
[5]: ../assets/images/zram/zram-5.jpg
[6]: ../assets/images/zram/zram-6.jpg




