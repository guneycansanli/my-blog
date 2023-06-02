---
title: "Creating Debian 11(x86) on Mac book M1(arm64) With QEMU/UTM"
layout: post
date: 2023-05-31 20:20
image: ../assets/images/qemu/main.jpg
headerImage: true
tag:
    - VM
    - linux
    - QEMU
    - Virtualization
category: blog
author: guneycansanli
description: Running amd64 Debian On Apple M1 With qemu/utm
---

# Tabel of Contents

-   Introduction
-   Prerequisites
-   Installing UTM
-   Downloading Debian 11 ISO
-   Installing QEMU
-   Creating a Virtual Machine in UTM
-   Installing Debian 11
-   Completing the Installation
-   Conclusion

## Introduction?

In this guide, we will walk you through the process of creating a **Debian 11 x86 (64-bit)** virtual machine on a **Macbook M1 ARM64** using **QEMU** and **UTM**. This allows you to run x86-based software and explore the Debian 11 environment on your **ARM-based Macbook**.


# Prerequisites:

Before we begin, ensure that you have the following:

-   Macbook M1 ARM64
-   UTM (Universal Turing Machine) installed
-   Debian 11 x86 ISO image
-   QEMU installed via Homebrew or a package manager

---

## Install UTM ?

Step 1: Installing UTM
UTM is an iOS virtualization app that leverages **QEMU (Quick Emulator)** as its core technology. QEMU is an open-source emulator and virtualization tool that allows UTM to provide hardware-level emulation for running different operating systems on iOS devices. With QEMU support, UTM can offer a wide range of compatibility and performance optimizations, making it a powerful tool for virtualization on iOS. Users can take advantage of QEMU's flexibility and features within the UTM app to create and manage virtual machines effectively.
To start, install UTM on your Macbook M1. UTM is a virtual machine app that supports various architectures, including x86 and ARM. Visit the UTM website and follow the installation instructions provided for your Macbook.

You can install UTM via [https://mac.getutm.app/][1] , UTM has a GUI that you can see your VMs and YOu can use predefined VM template. UTM employs Apple's Hypervisor virtualization framework to run ARM64 operating systems on Apple Silicon at near native speeds. On Intel Macs, x86/x64 operating system can be virtualized. In addition, lower performance emulation is available to run x86/x64 on Apple Silicon as well as ARM64 on Intel. For developers and enthusiasts, there are dozens of other emulated processors as well including: ARM32, MIPS, PPC, and RISC-V. Your Mac can now truly run anything. Under the hood of UTM is **QEMU**, a decades old, free and open source emulation software that is widely used and actively maintained. While QEMU is powerful, it can be difficult to set up and configure with its plethora of command line options and flags. UTM is designed to give users the flexibility of QEMU without the steep learning curve that comes with it.


## Downloading Debian 11 ISO

You can use different Linux distrubitions (Ubuntu, Kali Linux, RHEL, CentOS). I wanted to virtualize debian 11 so I downloaded Debian 11 ISO. You can dowload the ISO file from offical Debian [https://www.debian.org/download][2] 

## Installing QEMU

- **QEMU** is a virtualization software that enables you to run operating systems on different architectures. Install QEMU using a package manager like Homebrew. Open Terminal and execute the command "brew install qemu" to install QEMU on your Macbook.


Now that we have brew on the system we can proceed to the QEMU installation. To do that we use a simple command:

{% highlight raw %}
 brew install qemu
{% endhighlight %}

When the installation is finished, we can run the following command to test if the installation was successful:
{% highlight raw %}
 qemu-system-x86_64 --version
{% endhighlight %}


# Creating a Virtual Machine in UTM

You can use UTM UI and It will use QEMU You don't need to use QEMU commands with that way. Steps of creating VM below.


1. Create VM → Emulate
![utm1][3]

2. Select “Linux”

3. Browse for the Linux installation ISO

4. Configure system settings.
![utm2][4]


5. After you’re done, go into the settings of the VM again: go to System and check Force Multicore: ✅


# Installing Debian 11

- After boot from the ISO image, install the OS on the disk. Once that is complete, stop the machine and remove the ISO from the drives and boot up your new VM. You’re ready to play with it. (Hint: the installation will be much slower that you’d expect, so be very patient. Best is to select qemu64 as CPU type for the installation).




Thanks for reading,

Guneycan Sanli.

---

[1]: https://mac.getutm.app/
[2]: https://www.debian.org/download
[3]: ../assets/images/qemu/utm1.jpg
[4]: ../assets/images/qemu/utm2.jpg
