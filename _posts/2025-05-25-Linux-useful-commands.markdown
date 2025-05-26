---
title: "10 Useful Linux Commands You’ve Probably Overlooked"
layout: post
date: 2025-05-25 11:00
image: ../assets/images/linux-commands/main.jpg
headerImage: true
tag:
    - linux
    - commands
    - terminal
category: blog
author: guneycansanli
description: 10 Useful Linux Commands You’ve Probably Overlooked
---

# 10 Useful Linux Commands You’ve Probably Overlooked

The Linux command line is full of small but mighty tools that often go unnoticed. While most users stick with common utilities like `ls`, `grep`, or `top`, there are lesser-known commands that can help you debug, automate, monitor, and streamline your workflow more effectively.

Here’s a curated list of powerful yet underutilized Linux commands—with examples to help you get started.

---

## 1. `strace` – Watch What a Program is Doing Behind the Scenes

`strace` allows you to observe all the system calls a program makes. Great for debugging, performance tuning, or understanding program behavior.

```bash
# For Debian-based distributions like Ubuntu
sudo apt-get install strace

# For RPM-based distributions like CentOS
sudo yum install strace
```

```bash
strace ls
strace -e openat curl https://google.com
```

![cmd][1]

**Common Uses**:
- Trace why a program isn’t working.
- See which config files or libraries are accessed.
- Monitor file permissions or missing dependencies.

---

## 2. `disown` – Keep Jobs Running After Closing the Terminal

Run a command in the background and prevent it from being terminated when you log out(SSH).

```bash
some_command & disown
```

Detach all background jobs:

```bash
disown -a && exit
```

**Also try**:
```bash
jobs       # List running jobs
disown %1  # Disown job number 1
```

Use `nohup` as an alternative to avoid disconnection:

```bash
nohup long_script.sh &
```

---

## 3. Terminal Clock Using `tput`, `sleep`, and `date`

Display a live clock in the top-right corner of your terminal:

```bash
while sleep 1; do tput sc; tput cup 0 $(($(tput cols)-29)); date; tput rc; done &
```

Adjust `29` to fit your terminal size. This command won’t interfere with your normal shell usage.

![cmd][2]

---

## 4. ASCII Clock with `watch` + `figlet`

Make your terminal fun and functional by turning it into a digital clock.

```bash
watch -t -n 1 "date +%T | figlet"
```

**Install figlet if needed**:
```bash
sudo apt install figlet        # Debian/Ubuntu
sudo dnf install figlet        # RHEL/Fedora
```

![cmd][3]

---

## 5. `host` & `dig` – DNS Lookup Utilities

Use these to troubleshoot DNS issues or gather domain info.

### `host`
```bash
host google.com
host 8.8.8.8
```

### `dig`
```bash
dig google.com
dig +short github.com
```

![cmd][4]

**Why use them**:
- Discover IPs and DNS records.
- Diagnose slow domain resolution.
- Perform reverse lookups.

---

## 6. `dstat` – Real-Time System Monitoring

An excellent all-in-one replacement for `vmstat`, `iostat`, and `netstat`.

```bash
sudo apt install dstat
dstat -cdngy
```

**Use Cases**:
- Monitor CPU, disk, and network activity.
- Check system behavior under load.
- Find performance bottlenecks quickly.


![cmd][5]

---

## 7. `bind -p` – Show Bash Key Bindings

Want to know what happens when you press `Ctrl` + something in Bash?

```bash
command-7
```

Useful for:
- Customizing key behavior.
- Discovering hidden keyboard shortcuts.
- Creating more efficient terminal workflows.

Example:
```bash
"\C-l": clear-screen
"\C-a": beginning-of-line
```

![cmd][6]

---

## 8. `touch /forcefsck` – Trigger Filesystem Check at Reboot

Create this file to ensure a full file system check runs during the next boot.

```bash
sudo touch /forcefsck
```

 **Why do it?**
- Detect and fix disk errors.
- Schedule preventive maintenance.
- Useful after improper shutdowns.

---

## 9. `ncdu` – Interactive Disk Usage Analyzer

A better, visual version of `du`.

```bash
sudo apt install ncdu
ncdu /
```

Features:
- Interactive file browser for disk usage.
- Easily find large files.
- Delete files directly from the interface.

![cmd][7]

---

## 10. `shred` – Secure File Deletion

Don’t just `rm` sensitive files—`shred` them:

```bash
shred -u confidential.txt
```

**Why?**
- Prevents file recovery by overwriting contents.
- Ideal for security-conscious environments.

More options:
```bash
shred -n 5 -z -u file.txt
```
- `-n 5`: Overwrite 5 times.
- `-z`: Final pass with zeros.
- `-u`: Delete after shredding.

---

## Tips:

- Try `bat` instead of `cat` for syntax-highlighted file viewing.
- Use `fzf` for fuzzy search through history or files.
- Combine `watch`, `grep`, and `curl` for auto-updating logs.

---

## Wrapping Up

These Linux commands may not be famous, but they are **powerful, time-saving, and worth learning**. Add them to your toolbox, and you’ll find yourself working more efficiently and solving problems faster.

Have a favorite hidden gem of your own? Let us know!



---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/linux-commands/command-1.jpg
[2]: ../assets/images/linux-commands/command-2.jpg
[3]: ../assets/images/linux-commands/command-3.jpg
[4]: ../assets/images/linux-commands/command-5.jpg
[5]: ../assets/images/linux-commands/command-6.jpg
[6]: ../assets/images/linux-commands/command-7.jpg
[7]: ../assets/images/linux-commands/command-9.jpg




