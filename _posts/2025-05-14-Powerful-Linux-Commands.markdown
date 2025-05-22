---
title: "Powerful Linux Commands"
layout: post
date: 2025-05-14 11:00
image: ../assets/images/linux-commands/main.jpg
headerImage: true
tag:
    - linux
    - commands
category: blog
author: guneycansanli
description: Powerful Linux Commands
---

# Rarely Used but Powerful Linux Commands (with Practical Examples)

If you're comfortable with basic Linux commands and want to push your command-line skills further, this article introduces 10 underused commands that offer real utility. These aren't just fun to know—they're practical, powerful, and might just become part of your daily toolkit.

---

## 1. `^foo^bar` – Quickly Fix a Mistyped Command

Rather than retyping a long command with a small typo, use this history substitution trick to fix and re-run it.

```bash
echo Hello foo
^foo^bar
```

**Output:**
```
Hello foo
Hello bar
```

**Note:** This only works in Bash with command history enabled. If it doesn't work, make sure you're using Bash and not another shell like Zsh or Fish.

---

## 2. `> file.txt` – Instantly Empty a File

A lightning-fast way to clear the contents of any file without opening it.

```bash
> logfile.txt
```

---

## 3. `at` – Schedule a One-Time Task

The `at` command lets you schedule tasks to run once at a given time. However, if you see:

```bash
-bash: at: command not found
```

You need to install it:

```bash
sudo apt install at  # Debian/Ubuntu
sudo systemctl enable --now atd  # Start the 'at' daemon
```

**Usage:**

```bash
at now + 2 minutes
at> echo "Backup started" >> /tmp/backup.log
at> <Press Ctrl+D here>
```

**Note:** Pressing `Ctrl+C` while inside `at>` cancels the job. Always finish by pressing `Ctrl+D` on a new empty line to submit it

---

## 4. `du -h --max-depth=1` – Directory Disk Usage Summary

Get a summary of how much space each folder in the current directory uses.

```bash
du -h --max-depth=1
```

**Sample Output:**
```
4.0K    ./scripts
20M     ./downloads
1.2G    ./videos
```

---

## 5. `expr` – Basic Math and String Operations

Use it for quick arithmetic or string manipulation directly in the shell.

```bash
expr 9 \* 7
expr length "Linux"
```

**Output:**
```
63
5
```

---

## 6. `yes` – Repeated Output Generator

Useful for automatically feeding responses to scripts or testing performance.

```bash
yes "Proceed with operation"
```

**Output (truncated):**
```
Proceed with operation
Proceed with operation
...
```

Press `Ctrl+C` to stop it.

---

## 7. `factor` – Prime Factorization

Quickly find the prime factors of any number.

```bash
factor 84
```

**Output:**
```
84: 2 2 3 7
```

---

## 8. `ping -i 60 -a` – Custom Interval & Audible Ping

Ping every N seconds with optional audible sound on each reply.

```bash
ping -i 3 -a 8.8.8.8
```

**Output:**
```
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=119 time=4.04 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=119 time=6.34 ms
64 bytes from 8.8.8.8: icmp_seq=3 ttl=119 time=7.42 ms
```

**Tip:** The `-a` option plays a sound, but some environments (like WSL or headless servers) may not have a working speaker or bell. Use `echo -e "\a"` to test the bell.

---

## 9. `tac` – Reverse File Contents

Display file contents from bottom to top, line by line—very handy for logs.

```bash
tac /var/log/syslog
```

---

## Final Thoughts

Some of these commands may not be installed by default, but they’re well worth having. After installing missing packages like `at` , you’ll unlock additional tools to automate and streamline your Linux tasks.

---

Thanks for reading!

—

**Guneycan Sanli**




