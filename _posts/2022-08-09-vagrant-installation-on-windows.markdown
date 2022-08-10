---
title: "How to Set up Virtual Box and Vagrant on Windows"
layout: post
date: 2022-08-09 21:50
image: ../assets/images/vgrant/vagrant-logo.png
headerImage: true
tag:
- vagrant
- cli
- devOps
- windows
- ansible
- vm
category: blog
author: guneycansanli
description: How to Set up Virtual Box and Vagrant on Windows?
---

# Introduction

[Vagrant][1] is a tool for building and managing virtual machine environments in a single workflow. With an easy-to-use workflow and focus on automation, Vagrant lowers development environment setup time, increases production parity, and makes the "works on my machine" excuse a relic of the past. With the Vagrant we can manage our VMs and Vagrant has commands for managing. Vagrant can use some of tools for setting up VMs like Oracle Virtual Box or Vm Ware, It this article I will use Vitual Box.

---

## Virtual Box

The **Virtual Box** is open-source software for virtualizing the x86 computing architecture. It acts as a hypervisor, creating a VM (virtual machine) where the user can run another OS (operating system).

The operating system where VirtualBox runs is called the "host" OS. The operating system running in the VM is called the "guest" OS. VirtualBox supports **Windows**, **Linux**, or **macOS** as its host OS.

When configuring a virtual machine, the user can specify how many CPU cores, and how much RAM and disk space should be devoted to the VM. When the VM is running, it can be "paused." System execution is frozen at that moment in time, and the user can resume using it later.

I that section you should download and set up the Virtual Box with that url [Virtual Box Download][2]. You can follow the basic downloading and installing steps. There is no more processing with Virtual Box. We will manage all process with Vagrant.

![VirtualBox][3]

## Download cmder?

**Cmder** is a software package created out of pure frustration over absence of usable console emulator on Windows. It is based on ConEmu with major config overhaul, comes with a Monokai color scheme, amazing clink (further enhanced by clink-completions) and a custom prompt layout. We will use some of commands and we should use more frames or more sessions with the CLI. Cmder will be our supporter on Windows.
[Download cmder][4]

![cmder][5]

## Download Vagrant for Windows?

After install **Virtual Box** and **cmder** We need to install **Vagrant** for **Windows**. It is not so diffrent with other so find here [Download Vagrant][6] for Windows version. You can follow the installation step then you will be ready to use **Vagrant**. You can use cmder for checking **Vagrant** version.

![vagrant1][7]

## What is Next ?

We will continue with Vagrant commands and creating VMs with Vagrant also inspecting to the Vagrant File whixh is the configuration file for VMs. Finally We will try to use Ansible.

---

[1]: https://www.vagrantup.com/intro
[2]: https://www.virtualbox.org/
[3]: ../assets/images/vgrant/virtualBox.PNG
[4]: https://github.com/cmderdev/cmder
[5]: ../assets/images/vgrant/cmder.PNG
[6]: https://www.vagrantup.com/
[7]: ../assets/images/vgrant/vagrant1.PNG