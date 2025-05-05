---
title: "How to Create a Bootable Linux USB on macOS"
layout: post
date: 2025-05-04 11:00
image: ../assets/images/linux-usb-mac/main.jpg
headerImage: true
tag:
    - linux
    - image
    - usb
category: blog
author: guneycansanli
description: How to Create a Bootable Linux USB on macOS via CLI
---

# 🐧 How to Create a Bootable Linux USB on macOS via CLI

This guide walks you through the process of creating a bootable USB drive for installing a Linux distribution, using only Terminal on macOS.

---

## ✅ Requirements

- A USB flash drive with at least 4GB capacity (8GB+ recommended)
- A downloaded `.iso` file for your desired Linux distribution (e.g., Ubuntu, Fedora, Debian)
- macOS system with administrative access
- Basic comfort with Terminal commands

---

## 🛠 Step-by-Step Instructions

### 1. Prepare the ISO File

Download your preferred Linux `.iso` from the official website. For example:

```bash
~/Downloads/debian-12.10.0-amd64-netinst.iso
```

![usb][1]

---

### 2. Convert the ISO to IMG Format

Use `hdiutil` to convert the `.iso` to a `.img.dmg` file:

```bash
hdiutil convert -format UDRW -o ~/Desktop/debian-12.10.0-amd64-netinst.img ~/Downloads/debian-12.10.0-amd64-netinst.iso
```

![usb][2]

**🔍 Explanation:**

- `hdiutil`: macOS utility to manipulate disk images.
- `convert`: Converts one disk image format to another.
- `-format UDRW`: Specifies the **Universal Disk Image Format, Read/Write**. This is required because `.img` files need to be written directly to USB and must support raw device access.
    - `UDRW` stands for **Universal Disk Image Format - Read/Write**.
    - Unlike `.ISO` files, `.IMG` images in UDRW format are byte-for-byte writable using `dd`.
- `-o ~/Desktop/debian-12.10.0-amd64-netinst.img`: Output path and base name (macOS will append `.dmg`).

> ✅ Output file will be: `debian-12.10.0-amd64-netinst.img.dmg` on your Desktop.

Note: macOS adds the `.dmg` extension automatically — the resulting file will be `linux.img.dmg`.

---

### 3. Identify the USB Drive

Insert your USB drive and list all disks:

```bash
diskutil list
```

Look for a device that matches your USB drive’s size (e.g., 16GB, 32GB). For example:

```
/dev/disk4 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *15.5 GB    disk4
   1:       Microsoft Basic Data                         6.2 GB     disk4s1
   2:                        EFI ESP                     5.2 MB     disk4s2
   3:       Microsoft Basic Data                         307.2 KB   disk4s3
   4:           Linux Filesystem                         9.3 GB     disk4s4
```

> ⚠️ **Important:** Double-check this step to avoid overwriting your system disk.

![usb][3]

---

### 4. Unmount the USB Drive

Unmount the disk (replace `disk4` with your actual identifier):

```bash
diskutil unmountDisk /dev/diskX # (Replace X with your USB disk for me "5")
```

You should see:

```
Unmount of all volumes on disk4 was successful
```

![usb][4]

---

### 5. Write the IMG File to USB

Now copy the `.img.dmg` to the USB using the `dd` command:

```bash
sudo dd if=~/Desktop/debian-12.10.0-amd64-netinst.img.dmg of=/dev/rdisk4 bs=1m
```

- `if=`: input file
- `of=`: output file (use `/dev/rdiskX` for raw access, which is faster)
- `bs=1m`: block size for writing

> 🔐 Enter your password when prompted. This process may take several minutes.

Optional: You can check progress by pressing `CTRL+T` during the write.

Example output:

```bash
633+0 records in
633+0 records out
663748608 bytes transferred in 161.418830 secs (4111965 bytes/sec)
```

![usb][5]

---

### 6. Eject the USB Drive

Once `dd` finishes, eject the USB drive safely:

```bash
diskutil eject /dev/disk4
```

![usb][6]

Now your USB drive is ready to boot.

---

## 🚀 Boot from USB

1. Insert the USB drive into the target computer.
2. Reboot and hold the `Option` (⌥) key at startup.
3. Select the USB device (usually shows up as "EFI Boot") from the boot menu.
4. Begin the Linux installation process.

---

## 🧪 Troubleshooting Tips

- **The USB doesn’t show up in boot menu:** Try recreating the USB with a different ISO, or ensure it's a hybrid image.
- **Permission denied errors:** Make sure you're using `sudo` with `dd`.
- **Overwriting wrong disk:** Always verify the disk ID with `diskutil list` before using `dd`.

---

## 🔗 References

- [Ubuntu Official Downloads](https://ubuntu.com/download)
- [Debian Live Images](https://www.debian.org/CD/live/)
- [Fedora Workstation](https://getfedora.org/)
- [Apple hdiutil Manual](https://ss64.com/osx/hdiutil.html)

---

👍 You now have a bootable Linux USB drive created using macOS without any third-party apps or Boot Camp!

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/linux-usb-mac/usb-1.jpg
[2]: ../assets/images/linux-usb-mac/usb-2.jpg
[3]: ../assets/images/linux-usb-mac/usb-3.jpg
[4]: ../assets/images/linux-usb-mac/usb-4.jpg
[5]: ../assets/images/linux-usb-mac/usb-5.jpg
[6]: ../assets/images/linux-usb-mac/usb-6.jpg

