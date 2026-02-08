---
title: "Remote Development with VSCode over SSH"
layout: post
date: 2026-02-08 11:00
image: ../assets/images/remote-vscode/main.jpg
headerImage: true
tag:
    - vscode
    - ide
    - ssh
category: blog
author: guneycansanli
description: Remote Development with VSCode over SSH
---

## Introduction

Developing on remote machines often introduces friction—mismatched environments, missing tools, or the hassle of constantly syncing files. Visual Studio Code addresses this problem elegantly with the **Remote — SSH** extension, allowing you to work directly on a remote system as if the code lived on your local machine. No manual file transfers, no environment duplication.

This guide walks through the concept, setup, and advanced usage of Remote — SSH so you can build, debug, and manage remote projects efficiently.


## Why Choose Remote — SSH?

The Remote — SSH extension allows you to:

- Open and edit folders located on remote machines via SSH  
- Use the full VS Code feature set (IntelliSense, debugging, Git, etc.) on remote files  
- Run commands and extensions directly on the remote host  

This approach is ideal when you need to:

- Work inside production or staging servers  
- Use powerful remote hardware for resource-intensive tasks  
- Share consistent environments across teams without local replication  


## How Remote — SSH Works

The extension is based on a lightweight client–server model:

- **Local side:** VS Code runs on your computer and handles the UI  
- **Remote side:** A VS Code Server is installed on the remote machine and executes extensions, language servers, and commands  

This setup delivers a smooth, near-local development experience regardless of where the server is hosted.

## Requirements

### Local System

- Visual Studio Code installed  
- An SSH client (OpenSSH is standard on most platforms) 

### Remote System

- An active SSH server  
- Supported platforms include:  
  - x86_64: Debian 8+, Ubuntu 16.04+, CentOS/RHEL 7+  
  - ARMv7l: Raspberry Pi OS 9+ (32-bit)  
  - ARMv8l: Ubuntu 18.04+ (64-bit)  
  - Windows: OpenSSH Server on Windows 10 (1803+)  
  - macOS: Remote Login enabled  

---

## Installation and Initial Setup

### Step 1: Install VS Code and Extensions

- Install Visual Studio Code on your local machine  
- Add the **Remote — SSH** extension from the VS Code Marketplace  
- Optionally install the **Remote Development** extension pack for additional tools.

![vscode][1]

![vscode][2]

---

### Step 2: Prepare the Remote Machine

Ensure the SSH service is running.

On Linux or macOS:

    sudo systemctl start sshd

On Windows:

- Enable **OpenSSH Server** from  
  Settings → Apps → Optional Features  

Verify connectivity from your terminal:

    ssh user@hostname

---

### Step 3: Connect from VS Code

1. Open the Command Palette (Ctrl+Shift+P or F1)  
2. Choose **Remote-SSH: Connect to Host…**  
3. Enter `user@hostname`  
4. Select the remote platform if prompted  

Once connected, VS Code switches context to the remote machine, and you can open folders and files normally.

---

## Advanced Setup and Customization

### SSH Key Authentication

Using SSH keys removes the need to type passwords and improves security.

Generate a key pair:

    ssh-keygen -t rsa -b 4096

Copy the public key to the remote host:

    ssh-copy-id user@hostname

---

### Using an SSH Config File

For servers you access often, define them in `~/.ssh/config`:

    Host myremote
        HostName remote-machine.com
        User myuser
        IdentityFile ~/.ssh/id_rsa

You can now connect using `myremote` both in the terminal and inside VS Code.

![vscode][3]

![vscode][4]

![vscode][5]

---

### Port Forwarding

If services on the remote machine run on private ports (such as web apps or databases), you can expose them locally.

- Open the Command Palette  
- Select **Forward a Port**  
- Specify the remote port and map it to a local one  

Access the service via `localhost:<local-port>` in your browser or tools.

---

## Managing Extensions on Remote Hosts

VS Code distinguishes between local and remote extensions:

- UI-focused extensions (themes, icons) stay local  
- Language tools, linters, and debuggers run remotely  

To install an extension on a remote host:

- Open the Extensions panel  
- Click **Install in SSH: [hostname]**  

You can also define default remote extensions in `settings.json`:

    "remote.SSH.defaultExtensions": [
        "eamodio.gitlens",
        "ms-python.python"
    ]

![vscode][6]

---

## Debugging Remotely

Remote debugging behaves the same as local debugging. Configure your debugger in `.vscode/launch.json`, and VS Code will execute and debug the application directly on the remote machine without extra setup.

---

## Disconnecting from the Remote Host

To end the session:

- Select **File → Close Remote Connection**, or  
- Simply close VS Code  

---

## Troubleshooting Common Problems

### SSH Issues

- Incorrect permissions can break SSH keys:

      chmod 600 ~/.ssh/id_rsa  
      chmod 700 ~/.ssh

- Hanging connections often indicate firewall or SSH configuration problems  

---

### Extension Compatibility

Some extensions that rely on native x86 binaries may not work on ARM-based systems. Always check the extension documentation if you encounter issues.

---

## Final Thoughts

The **Remote — SSH** extension transforms how developers work with remote environments. By bringing editing, debugging, and tooling directly to the server, it eliminates friction and boosts productivity. Whether you’re managing cloud servers, embedded devices, or shared development machines, Remote — SSH delivers a powerful and flexible workflow that feels truly local.

---

Thanks for reading!

**Guneycan Sanli**


[1]: ../assets/images/remote-vscode/vscode-1.jpg
[2]: ../assets/images/remote-vscode/vscode-2.jpg
[3]: ../assets/images/remote-vscode/vscode-3.jpg
[4]: ../assets/images/remote-vscode/vscode-4.jpg
[5]: ../assets/images/remote-vscode/vscode-5.jpg
[6]: ../assets/images/remote-vscode/vscode-6.jpg