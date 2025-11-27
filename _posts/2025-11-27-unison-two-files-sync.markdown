---
title: "Using Unison for Two-Way File Synchronization on Linux"
layout: post
date: 2025-11-27 11:00
image: ../assets/images/unison/main.jpg
headerImage: true
tag:
    - unison
    - linux
    - rsync
    - scp
category: blog
author: guneycansanli
description: Using Unison for Two-Way File Synchronization on Linux
---

# **A Step-by-Step Guide to Installing and Using Unison for Two-Way File Synchronization on Linux**

Keeping files synchronized between multiple Linux systems is something many of us eventually need—especially if you move between a laptop and desktop, keep a home lab, or work on projects from both a personal and a remote server.  

Simple backup tools like rsync are great, but they only move data in one direction. If you modify files on both systems, you can easily overwrite something important.

This is exactly why I personally use **Unison**, a powerful two-way file synchronizer. It compares both sides, detects what changed where, and keeps everything consistent.

Below is my complete step-by-step guide on installing and using Unison effectively, including helpful examples and tips from real use.

---

## **What You Will Learn**
- How to install Unison on various Linux distributions  
- How to sync two folders locally(server)
- How to synchronize files between different machines using SSH  
- How to create Unison profiles for repeatable tasks  
- How to run continuous sync or schedule automatic syncs  
- How to avoid common pitfalls like version mismatches  

Let’s begin!

---

# **1. Installing Unison on Your Linux System**
Unison is available in most official repositories, so installation is straightforward.

### **Install on popular distributions**

```bash
sudo apt install unison  
sudo dnf install unison  
sudo apk add unison  
sudo pacman -S unison  
sudo zypper install unison  
sudo pkg install unison  
```

### GUI Version (Optional)
Available only on Debian-based distributions:

```bash
sudo apt install unison-gtk
```

### Check your installed version

```bash
unison -version
```

![unison][1]

### Important Version Warning  

Unison requires **both machines to use the same version**.  
If your local system has Unison 2.53.3 and your server has 2.48, they will refuse to sync.

This is one of the most common beginner problems—so always check versions on both ends.

---

# **2. Local Folder Synchronization (Beginner-Friendly Start)**

Before jumping into remote syncing, I always recommend practicing with two local folders.

Let’s say I have:

- My working folder:  
  ~/test/test_file  
- A local sync folder:  
  ~/sync/test_file_backup

![unison][2]

### Run your first sync
```bash
unison ~/test/test_file  ~/sync/test_file_backup
```

![unison][3]

### What happens during this process?

1. **Unison scans both directories**  
   It looks at file names, sizes, and modification timestamps.

2. **Changes are listed**  
   You’ll see which files are new, updated, or conflicting.

3. **Unison asks what to do**  
   Copy from left to right? Right to left? Skip?

4. **Synchronization happens**  
   Both folders become identical after confirming actions.

### Good to know
- Files missing on one side are automatically copied.
- If both versions of a file have changed, you’ll be asked how to resolve the conflict.

---

# **3. Synchronizing Files Between server and remote server (SSH)**

Once you're comfortable with local sync, it’s time to sync with another machine.  
Let’s assume I want to sync my local "test" folder with a folder on my server.

### Example Command
```bash
unison ~/test ssh://guney@192.168.1.151//home/guney/sync
```

![unison][4]

![unison][5]

![unison][6]

### Explanation
- ~/test → your local directory  
- ssh://user@server//path → remote folder via SSH  
- The double slash (//) after the hostname means **absolute path**  

### Tip: Use SSH keys  
I strongly recommend setting up SSH key authentication so you don’t have to enter your password every time.

---

# **4. Creating Unison Profiles (Automation Made Simple)**

Typing the full sync command each time gets tiring.  
Profiles allow you to save configuration files that run with a single word.

### Create a profile

```bash
vi ~/.unison/testsync.prf
```

### Example profile configuration

```bash
root = /home/guney/test
root = ssh://guney@192.168.11.151//home/guney/sync

auto = true
batch = true
prefer = newer
```  

![unison][7]

### What each setting does:

- **root** → folders to synchronize  
- **auto** → automatically resolves simple changes  
- **batch** → no confirmation prompts  
- **prefer = newer** → uses the most recently updated file if conflicts happen  

### Run using profile

```bash
unison testsync
```

![unison][8]

Much easier, right?

---

# **5. Live Syncing with Watch Mode (Real-Time Monitoring)**

If you're working on a project and want changes to sync automatically without manual commands, you can use:

```bash
unison testsync -repeat watch
```

**Note: It did not work in my tests, I am searching probably the version of my unison**

This runs Unison in continuous watch mode:

- Monitors both folders  
- Syncs instantly whenever something changes  
- Great for development environments  

### Note
For very large directories, watch mode may consume noticeable system resources.  
Use it only when necessary.

---

# **6. Scheduling Automatic Syncs with Cron**

If you prefer periodic sync instead of continuous watch mode, you can use cron.

### Edit your crontab

```bash
crontab -e
```

### Example: Sync every hour

```bash
0 * * * * unison testsycn -batch
```

This runs your "testsycn" profile non-interactively every hour.

---

# **7. Useful Unison Options (Cheat Sheet)**

<table>
  <thead>
    <tr>
      <th>Option</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>-auto</td>
      <td>Automatically handles simple updates</td>
    </tr>
    <tr>
      <td>-batch</td>
      <td>No user prompts (cron-friendly)</td>
    </tr>
    <tr>
      <td>-ui text</td>
      <td>Forces text interface</td>
    </tr>
    <tr>
      <td>-repeat watch</td>
      <td>Real-time sync</td>
    </tr>
    <tr>
      <td>-prefer newer</td>
      <td>Newest file wins in conflicts</td>
    </tr>
  </tbody>
</table>


### Combined example
unison work -auto -batch -prefer newer

---

# **8. Why I Prefer Unison Over Other Tools**

<table>
  <thead>
    <tr>
      <th>Tool</th>
      <th>Sync Type</th>
      <th>Two-Way</th>
      <th>GUI</th>
      <th>Best Use</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>rsync</td>
      <td>One-way</td>
      <td>❌</td>
      <td>❌</td>
      <td>Backups, mirroring</td>
    </tr>
    <tr>
      <td>Unison</td>
      <td>Two-way</td>
      <td>✅</td>
      <td>Optional</td>
      <td>Active development, multi-system workflows</td>
    </tr>
  </tbody>
</table>


Unison gives me flexibility rsync never could.  
I can edit files on either machine knowing nothing will be overwritten incorrectly.

---

# **Final Thoughts**

Unison is one of the most reliable and efficient tools for two-way synchronization on Linux.  
Once you get your first profile working, it becomes incredibly convenient—especially for people like me who work across multiple systems every day.

Whether you want automatic backups, mirrored development environments, or fast syncing between your laptop and server, Unison is a tool worth mastering.

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/unison/unison-1.jpg
[2]: ../assets/images/unison/unison-2.jpg
[3]: ../assets/images/unison/unison-3.jpg
[4]: ../assets/images/unison/unison-4.jpg
[5]: ../assets/images/unison/unison-5.jpg
[6]: ../assets/images/unison/unison-6.jpg
[7]: ../assets/images/unison/unison-7.jpg
[8]: ../assets/images/unison/unison-8.jpg





