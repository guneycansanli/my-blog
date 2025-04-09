---
title: "Meet bat – A Better cat Command for Modern Linux Users"
layout: post
date: 2025-04-06 12:20
image: ../assets/images/batcat/main.jpg
headerImage: true
tag:
    - linux
    - command-line
    - tools
    - terminal
category: blog
author: guneycansanli
description: A modern take on the classic Linux cat command – with syntax highlighting, Git integration, and line numbers.
---

## Introduction

The classic `cat` command has been a staple of Unix-like systems for decades, used to print file contents in a simple, no-frills way. But if you're craving something more powerful, stylish, and developer-friendly, then meet **`bat`** — a modern replacement with wings.

**`bat`** isn’t just a pretty face; it adds line numbers, syntax highlighting, paging, and even Git integration to your file-viewing experience — all while being compatible with how you'd normally use `cat`.

---

## Why Use `bat`?

- Syntax highlighting for dozens of programming languages  
- Line numbers included by default  
- Git-aware: highlights changed/added lines  
- Paging support via `less`  
- Works as a drop-in replacement for `cat` in many cases

> 🗒️ Think of it as `cat` on steroids — ideal for developers and power users alike.

---

## Installing `bat` on Linux

`bat` is available in the official repositories of most major Linux distributions.

### 🐧 Ubuntu / Debian

```bash
sudo apt install bat
```

![bat][1]

> ✅ Note: On Ubuntu/Debian, the binary is called `batcat`. You’ll need to alias it manually if you want to call it as `bat`.

### 🏹 Arch Linux

```bash
sudo pacman -S bat
```

### 🟥 Fedora / RHEL / CentOS

```bash
sudo dnf install bat
```

---

## ⚙️ Setting Up Alias (Ubuntu/Debian)

By default, on Debian-based distros, you'll invoke the command using `batcat`:

```bash
batcat filename.txt
```

Want to use `bat` instead? Set up an alias:

```bash
alias bat="batcat"
```

To make it permanent, add it to your shell config file (`~/.bashrc`, `~/.zshrc`, etc.):

```bash
echo "alias bat='batcat'" >> ~/.bashrc
source ~/.bashrc
```

---

## Usage Examples

Just like `cat`, you can view file contents:

```bash
batcat file.txt
```

![bat][2]

### Show Line Numbers Only

If you prefer minimal visuals:

```bash
batcat -n file.txt
```

![bat][3]

### 💡 Force Syntax Highlighting

`bat` auto-detects file types, but you can also manually set the language:

```bash
batcat -l python script.py
batcat -l c hello.c
```

### View Multiple Files

You can pass multiple files, just like with `cat`:

```bash
batcat file1.sh file2.sh
```

### Pipe Output or Use with Other Commands

Works seamlessly with pipes and standard input:

```bash
echo "Hello, bat!" | batcat
```

![bat][4]

---

## Custom Themes and Configuration

`bat` supports different syntax highlighting themes. To list available themes:

```bash
batcat --list-themes
```

Choose one by setting it via the config file or environment variable:

```bash
export BAT_THEME="TwoDark"
```

You can also create a `bat` config at `~/.config/bat/config`.

Example:

```ini
--theme="TwoDark"
--style="numbers,changes,header"
```

![bat][5]

---

## Summary

`batcat` is an awesome upgrade over the traditional `cat` command, especially for developers and sysadmins who spend their day in the terminal. It keeps everything you love about `cat` while adding modern capabilities that make reading code and config files a joy.

- Looks better ✅  
- Reads better ✅  
- Works with Git ✅  
- Still just as fast ✅

If you're customizing your CLI toolkit, `bat` is a no-brainer.

---

## More Tools Like `bat`

Love tools like `bat`? Check out other modern replacements:

- [`fd`](https://github.com/sharkdp/fd) – A smarter `find`
- [`ripgrep`](https://github.com/BurntSushi/ripgrep) – A lightning-fast `grep`
- [`exa`](https://github.com/ogham/exa) – A fancy `ls` replacement

---

## 📘 Official Resources

- [bat GitHub Repo](https://github.com/sharkdp/bat)
- [bat Manual](https://man.archlinux.org/man/bat.1.en)

---

Thanks for reading! If `bat` earns a spot in your daily workflow, let me know what theme you're rocking!

👍 🦇 🚀

— Guneycan Sanli

[1]: ../assets/images/batcat/bat-1.jpg
[2]: ../assets/images/batcat/bat-2.jpg
[3]: ../assets/images/batcat/bat-3.jpg
[4]: ../assets/images/batcat/bat-4.jpg
[5]: ../assets/images/batcat/bat-5.jpg