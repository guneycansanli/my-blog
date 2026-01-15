---
title: "Netbooting with iPXE"
layout: post
date: 2026-01-14 11:00
image: ../assets/images/ipxe/main.jpg
headerImage: true
tag:
    - pxe
    - ipxe
    - netboot
    - linux
category: blog
author: guneycansanli
description: Netbooting with iPXE
---

## Introduction

Network booting allows computers to start without relying on a local disk. Instead, the machine fetches its boot instructions over the network. One of the most flexible tools for this is **iPXE**, an open-source network boot firmware that significantly extends the traditional PXE feature set.

Unlike legacy PXE which typically uses TFTP, iPXE supports modern protocols such as:

- HTTP / HTTPS  
- iSCSI SAN  
- AoE  
- FTP  
- NFS  

It also provides a built-in shell, scripting support, and the ability to compile custom boot images, ISO files, USB images, and firmware ROMs.

---

## What We Will Build

- Compile a custom iPXE ISO  
- Boot it in QEMU  
- Embed a boot script in the ISO  
- Serve boot scripts over HTTP  
- Boot Debian 13 directly over the network  

Let’s begin!

---

## Part 1: Building and Testing iPXE

We need to have a machine/server to create and serve images and boot-scipt. There are some packages We need to install details can be found in [https://ipxe.org/download](https://ipxe.org/download)

I have used Debian12 VM below packages can be diffrent for different distros.

```bash
apt update

apt install gcc binutils make perl liblzma-dev mtools genisoimage syslinux squashfs-tools xorriso bc
```

![ipxe][1]

Clone the iPXE repo from https://github.com/ipxe/ipxe

```bash
git clone git@github.com:ipxe/ipxe.git 
```

For our testing environment we will need: 

```bash
apt install qemu python3
```

### Building first iPXE iso image

We will try to create a dummy/first image and It will be x86_64 architecture. 

Run the following two commands in the src/ folder to build your first iPXE iso image.

```bash
#Build the iPXE binaries
#This command builds the iPXE bootloader binaries for both BIOS and UEFI systems.
make bin/ipxe.lkrn bin-x86_64-efi/ipxe.efi

#Generate the ISO image
#This command packages the compiled iPXE binaries into a single bootable ISO that you can then use with QEMU or burn to a CD/USB.
./util/genfsimg -o ipxe.iso bin/ipxe.lkrn bin-x86_64-efi/ipxe.efi
```

![ipxe][2]

After that you’ll find the ipxe.iso file directly in the src folder.

![ipxe][3]

We are ready to run our image. I will use Proxmox to test ipxe image. 


### Running first iPXE iso image

As observed, iPXE sets up the network and performs a DHCP request to obtain an IP address (e.g., 192.168.1.188/24 with gateway 192.168.1.1). By pressing Ctrl-B, you can access the iPXE command prompt to experiment with commands. For instance, you could try loading the official iPXE test boot script by entering the following command. If you do not press anything It will keep reboot.

![ipxe][4]

Once You open IPXE terminal we can try to load the official iPXE test boot script by typing the command:

```bash
chain http://boot.ipxe.org/demo/boot.php
```

![ipxe][5]

![ipxe][6]

We could succesfully ran boot script over the internet.

### How to embed a boot script into your iPXE iso

Let’s assume we want to experiment with iPXE, but we won’t rely on the iPXE interactive shell anymore. Instead, we can provide a boot script dynamically via HTTP. In this setup, we need two separate boot scripts: one embedded in the iPXE ISO, and another served over HTTP.

The first script’s job is simple: it fetches further boot instructions from an HTTP server. This approach decouples the boot process, making it easier to prototype and test. You can modify the HTTP-provided script at any time without rebuilding the ISO.

Here’s an example of the ISO-embedded script boot.ipxe:

```bash
#!ipxe
dhcp

set uri http://192.168.1.178/boot-http.ipxe
echo Loading ${uri}

chain ${uri} || goto failed
exit

:failed
echo Chain failed ,, retrying in 5 seconds...
sleep 5
goto retry
```

**Note: 192.168.1.178 is the host machine’s IP address that I will use for serving the boot script.**

Now you can embed the script into your iPXE iso with the follwing commands.

```bash
#!ipxe
make bin/ipxe.lkrn bin-x86_64-efi/ipxe.efi EMBED=boot.ipxe
./util/genfsimg -o ipxe.iso bin/ipxe.lkrn bin-x86_64-efi/ipxe.efi
```

![ipxe][7]

Now we need a bootscript which is served by a HTTP server.The script name should be **boot-http.ipxe:** beacuse We have put **http://192.168.1.178/boot-http.ipxe** to our iso and It will try to fetch that script also We will serve that file. I will create another directory in same machine and put my script there. 

```bash
#!ipxe
echo hello world!
echo sleeping for 5 seconds...
sleep 5
```

![ipxe][8]

To provide the boot script over HTTP we navigate to the folder where your boot-http.ipxe is located (on the host machine). In this folder start a HTTP server, e.g. a python http server with the following command.

```bash
python3 -m http.server 80
```
![ipxe][9]

and now if you are run new image You should see this

![ipxe][10]

As we can see, iPXE starts our boot script, gets the boot-http.ipxe over HTTP and executes the commands in the boot-http file.

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/ipxe/ipxe-1.jpg
[2]: ../assets/images/ipxe/ipxe-2.jpg
[3]: ../assets/images/ipxe/ipxe-3.jpg
[4]: ../assets/images/ipxe/ipxe-4.jpg
[5]: ../assets/images/ipxe/ipxe-5.jpg
[6]: ../assets/images/ipxe/ipxe-6.jpg
[7]: ../assets/images/ipxe/ipxe-7.jpg
[8]: ../assets/images/ipxe/ipxe-8.jpg
[9]: ../assets/images/ipxe/ipxe-9.jpg
[10]: ../assets/images/ipxe/ipxe-10.jpg






