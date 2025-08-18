---
title: "Mastering Input Redirection in Linux - <, <<, and <<<"
layout: post
date: 2025-08-17 11:00
image: ../assets/images/redirect/main.jpg
headerImage: true
tag:
    - linux
    - redirection
category: blog
author: guneycansanli
description: Mastering Input Redirection in Linux - <, <<, and <<<
---

# Introduction

In the Linux shell, redirection operators let you control how commands receive input and where they send output. Most users know about > and >> for writing to files, but input redirection (<, <<, and <<<) is equally powerful.

These three operators allow you to feed data into commands directly from files, inline text, or strings. Mastering them will help you write cleaner scripts and simplify everyday sysadmin tasks.

## 1. < – Redirect Input from a File

### Example 1. < – Redirect Input from a File

The < operator takes a file’s contents and provides it as standard input (stdin) to a command.

Example 1 – Count lines in a file:

```bash
wc -l < /var/log/dpkg.log
```

![redirect][1]

This tells wc to count lines by reading **/var/log/dpkg.log** as input, without using cat.

Equivalent to:

```bash
cat /var/log/dpkg.log | wc -l
```

---

### Example 2 – Feed SQL file into MySQL:

```bash
mysql -u root -p < database_backup.sql
```

This imports all SQL statements in the file directly into the database.

---

### Example 3 – Compare two files line by line:

```bash
diff <(sort file1.txt) <(sort file2.txt)
```

![redirect][2]

Here <() (process substitution) is combined with < to pass sorted versions of files into diff.


---

## 2. "<<" – Here Document (Multi-Line Input)

The **"<<"** operator lets you supply multi-line input inline. It continues until a specified delimiter is reached. This is very common in shell scripts.

### Example 1 – Pass multiple lines to cat:

```bash
cat <<EOF
Hi
Hello World
This is a here document.
EOF
```

![redirect][3]

### Example 2 – Automating database queries:

```bash
mysql -u root -p <<SQL
USE employees;
SELECT COUNT(*) FROM users;
SELECT * FROM logs LIMIT 3;
SQL
```

Here, everything between <<SQL and the closing SQL is passed to the mysql client.

### Example 3 – Configure files inline:

```bash
tee /etc/nginx/conf.d/test.conf <<CONFIG
server {
    listen 8080;
    server_name example.com;
    root /var/www/html;
}
CONFIG
```

This writes an Nginx config file directly from the shell without editing manually.

### Example 4 – Run multiple commands over SSH:

```bash
ssh admin@server <<'COMMANDS'
cd /var/log
ls -lh
df -h
uptime
COMMANDS
```
The block of commands runs remotely as if typed one by one.

![redirect][4]

## 3. "<<<" – Here String (Single-Line Input)

The **"<<<"** operator is like <<, but for single strings. It’s perfect for quick one-liners.

### Example 1 – Count words in a string:

```bash
wc -w <<< "Hi my name is guney"
```

![redirect][5]

### Example 2 – Grep a string:

Example:

```bash
grep "root" <<< "user:root:1234"
```

### Example 3 – Pass variables without echo:

```bash
read name <<< "Guney"
echo "Hello, $name"
```

![redirect][6]

## 4. Practical Scenarios for Sysadmins and Developers

Database administration
- **Import backups with < or send inline queries with <<.**

Configuration management
- **Generate config files dynamically with <<EOF blocks in scripts.**

Testing commands
- **Quickly test parsing tools with <<< "sample text".**

Remote automation
- **Use << with SSH for batch execution of commands.**

Scripting best practices
- **Replace echo "..." | cmd with <<< "..." for readability and efficiency.**

---

## 5. Summary

**< file → Redirects a file as input.**
**<< EOF ... EOF → Feeds multi-line text (here document).**
**<<< "string" → Feeds a single-line string (here string).**

These operators make your Bash scripts shorter, cleaner, and more powerful. Once you adopt them, you’ll stop overusing cat and echo, and your automation tasks will look much more professional.

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/redirect/redirect-1.jpg
[2]: ../assets/images/redirect/redirect-2.jpg
[3]: ../assets/images/redirect/redirect-3.jpg
[4]: ../assets/images/redirect/redirect-4.jpg
[5]: ../assets/images/redirect/redirect-5.jpg




