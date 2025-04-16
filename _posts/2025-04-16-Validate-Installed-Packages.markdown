---
title: "How to Validate Installed Packages on Debian/Ubuntu with MD5 Checksums"
layout: post
date: 2025-04-16 11:00
image: ../assets/images/debsum/main-2.jpg
headerImage: true
tag:
    - linux
    - apt
    - security
    - md5
category: blog
author: guneycansanli
description: Ensure the integrity of your installed packages on Debian and Ubuntu systems by verifying them using MD5 checksums and the debsums utility.
---

## Why Package Verification Matters

Have you ever run into a situation where an application fails to launch or behaves unexpectedly right after installation? It might not always be a bug — in many cases, the package might’ve been corrupted during the download or installation process.

Interrupted downloads, hardware glitches, or network instability can result in files being altered or incomplete. That’s why verifying packages after installation is a crucial step in maintaining system integrity.

This guide will walk you through how to **verify MD5 checksums** of packages using the `debsums` tool on **Debian, Ubuntu**, and their derivatives like Linux Mint.

---

## What Is `debsums`?

`debsums` is a lightweight utility used to check the integrity of installed package files against the original MD5 hash values provided by the package maintainers.

---

## Installing `debsums`

You can install `debsums` using your package manager.

### Debian/Ubuntu/Mint

```bash
sudo apt update
sudo apt install debsums
```

### Optional: View Package Info Before Installing

```bash
apt show debsums
```

![debsum][1]

---

## Basic Usage

Running `debsums` without any options will scan all packages and validate their installed files against known MD5 checksums.

```bash
sudo debsums
```

The output includes the file path and a status:

- ✅ `OK`: The file matches the original checksum.
- ❌ `FAILED`: The file differs from its expected checksum.
- ⚠️ `REPLACED`: The file was overwritten by another package.

![debsum][2]

---


## Scan All Files, Including Configs

To verify **all files**, including configuration files:

```bash
sudo debsums -a
```

Or the long version:

```bash
sudo debsums --all
```

---

## Check Only Configuration Files

Want to validate only config files? Use the `-e` or `--config` option:

```bash
sudo debsums -e
```

---

## Show Only Modified Files

Limit the output to files that have been **changed**:

```bash
sudo debsums -c
```

Or:

```bash
sudo debsums --changed
```

This is handy for spotting tampered or misconfigured files.

---

## Identify Files Missing Checksums

Not all packages include checksum data for every file. To list files that lack checksum entries:

```bash
sudo debsums -l
```

Or:

```bash
sudo debsums --list-missing
```

---

## Verify a Single Package

You can target specific packages. For example, checking the `nano` editor:

```bash
sudo debsums nano
```

This can help troubleshoot broken behavior in isolated packages.


![debsum][4]

---

## Ignore Permission Errors

Running `debsums` as a non-root user can trigger permission warnings. Suppress them with:

```bash
debsums --ignore-permissions
```

---

## Generate Checksums from .deb Files

You can also **generate MD5 hashes** directly from `.deb` files — useful if a package lacks the sums or if you’re checking against a local archive.

### Syntax

```bash
sudo debsums --generate=missing <package-name>
```

### Example: Generate for `htop`

```bash
sudo debsums --generate=missing htop
```

Options:
- `missing`: Only for packages lacking sums.
- `all`: Regenerate regardless of presence.
- `keep`: Save the new checksums to the info directory.
- `nocheck`: Skip comparison with installed files.


![debsum][3]

---

## Where Checksums Are Stored

Checksum files live in:

```bash
/var/lib/dpkg/info/
```

You can list all `.md5sums`:

```bash
cd /var/lib/dpkg/info
ls *.md5sums
```

---

## Learn More

To explore the full range of options:

```bash
man debsums
```

---

## Wrapping Up

Verifying your installed software with `debsums` ensures that no file has been accidentally or maliciously altered. It's a fast, effective way to **catch corruption**, **confirm integrity**, and **strengthen your system’s reliability**.

Whether you're a system admin or just a curious user, this small tool can save you big headaches.

---

## More System Integrity Tools

- [`aide`](https://aide.github.io/) – Advanced Intrusion Detection Environment  
- [`tripwire`](https://github.com/Tripwire/tripwire-open-source) – Host-based security monitoring  
- [`chkrootkit`](http://www.chkrootkit.org/) – Rootkit detection tool  

---

Thanks for reading!

— Guneycan Sanli

[1]: ../assets/images/debsum/debsum-1.jpg
[2]: ../assets/images/debsum/debsum-2.jpg
[3]: ../assets/images/debsum/debsum-3.jpg
[4]: ../assets/images/debsum/debsum-4.jpg
