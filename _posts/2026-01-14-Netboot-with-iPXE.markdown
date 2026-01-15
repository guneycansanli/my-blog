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
- Embed a boot script in the ISO  
- Serve boot scripts over HTTP  
- Boot Debian 13 directly over the network  

Let’s begin!

---

## Part 1: Building and Testing iPXE

We need to have a machine/server to create and serve images and boot-scipt. There are some packages that We need to install. Details can be found in [https://ipxe.org/download](https://ipxe.org/download)

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

## Part 2: Booting Debian 13 Over HTTP

Since We have tested and understood iPXE We can now do a real life example. I will try to boot Debian 13 but Youc an try any other distros steps should be pretty much similar. We need to install dowload debian netboot.

Debian Netboot is a lightweight network installer that contains boot loaders, the Debian installer kernel and initrd, PXE/UEFI boot menus, and optional automation files (preseed), allowing computers to boot over the network and download and install the full Debian operating system from Internet mirrors without any local installation media.

Here is the flow:

```bash
Custom iPXE ISO
        ↓
Embedded boot.ipxe
        ↓
HTTP-served boot-http.ipxe  
        ↓
Debian kernel + initrd over HTTP
        ↓
Full Debian installer
```

### Prepare Debian Netboot Files

Download Debian Netboot Files:

```bash
#https://deb.debian.org/debian/dists/stable/main/installer-amd64/current/images/netboot/
wget https://deb.debian.org/debian/dists/stable/main/installer-amd64/current/images/netboot/netboot.tar.gz
```

Extract:

```bash
tar -xzf netboot.tar.gz
```

Result:

```bash
root@pup-agent:~# ls -lrth web/
total 20K
-rw-r--r-- 1 root root   63 Jan  5 13:38 version.info
lrwxrwxrwx 1 root root   47 Jan  5 13:38 splash.png -> debian-installer/amd64/boot-screens//splash.png
lrwxrwxrwx 1 root root   35 Jan  5 13:38 pxelinux.cfg -> debian-installer/amd64/pxelinux.cfg
lrwxrwxrwx 1 root root   33 Jan  5 13:38 pxelinux.0 -> debian-installer/amd64/pxelinux.0
lrwxrwxrwx 1 root root   47 Jan  5 13:38 ldlinux.c32 -> debian-installer/amd64/boot-screens/ldlinux.c32
drwxr-xr-x 3 root root 4.0K Jan  5 13:38 debian-installer
```

We are ready to create our Debian Boot Script. We can create a file **boot-http.ipxe** and place in same directory. The script will be used by ISO's script and it will call debian boot script.

```bash
vi boot-http.ipxe  
```

```bash
#!ipxe
echo Starting unattended Debian 12 install...

set base http://192.168.1.178/debian-installer/amd64
set mirror http://deb.debian.org/debian

kernel ${base}/linux \
    initrd=initrd.gz \
    ip=dhcp \
    auto=true \
    priority=critical \
    interface=auto \
    mirror/http/hostname=deb.debian.org \
    mirror/http/directory=/debian \
    mirror/http/proxy= \
    preseed/url=http://192.168.1.178/preseed.cfg

initrd ${base}/initrd.gz
boot
```

![ipxe][11]

Here’s what that iPXE script is doing, line-by-line:

**echo Starting unattended Debian 12 install...** --> Just prints a message on the iPXE screen so you know this is the automatic installer path.

**set base http://192.168.1.178/debian-installer/amd64** --> efines the base URL where the Debian installer files live (kernel + initrd).
This should point to the extracted Debian netboot directory on your HTTP server.

**set mirror http://deb.debian.org/debian** --> Defines which Debian package mirror the installer will use to download all packages during install.

**kernel ${base}/linux** --> Tells iPXE to load the Debian installer kernel from your HTTP server.

**Boot parameters passed to the kernel** 

```bash
#Parameter	What it does
initrd=initrd.gz #	Tells the kernel which initrd file to use
ip=dhcp #	Automatically configures networking via DHCP
auto=true #	Enables automatic (non-interactive) install mode
priority=critical #	Hides almost all installer questions
interface=auto #	Auto-selects the network interface
mirror/http/hostname=deb.debian.org #	Debian mirror hostname
mirror/http/directory=/debian #	Mirror directory
mirror/http/proxy=	# No proxy
preseed/url=http://192.168.1.178/preseed.cfg	 # Location of your preseed file (answers all install questions)
```

**initrd ${base}/initrd.gz** --> Loads the installer’s initial RAM disk (contains the installer environment).

**boot** --> Starts the Debian installer using the kernel + initrd + parameters you defined.


As We can see I have used **preseed.cfg** Which will do automatic installation for Debian so We need to create that file.


```bash
vi preseed.cfg
```

```bash
### Localization
d-i debian-installer/locale string en_US.UTF-8
d-i keyboard-configuration/xkb-keymap select us
d-i time/zone string UTC

### Network
d-i netcfg/choose_interface select auto
d-i netcfg/get_hostname string debian
d-i netcfg/get_domain string local

### Mirror
d-i mirror/country string manual
d-i mirror/http/hostname string deb.debian.org
d-i mirror/http/directory string /debian
d-i mirror/http/proxy string

### Users
d-i passwd/root-login boolean false
d-i passwd/user-fullname string guney
d-i passwd/username string guney
d-i passwd/user-password-crypted password $6$qRJQfbULu.OWRgX8$XOcmtFP9nC/eki9LdFY6C6wyOcaP0JR6ZgGQiXmUxX9CloEf6Nyfrzs9eRYudbBTBOieZU1gLLOCkHtFkEFkx0

### Disk (FULL AUTO WIPE)
d-i partman-auto/method string lvm
d-i partman-auto/choose_recipe select atomic

# This ensures any existing LVM or RAID partitions are cleared without asking
d-i partman-lvm/device_remove_lvm boolean true
d-i partman-md/device_remove_md boolean true
d-i partman-lvm/confirm boolean true
d-i partman-lvm/confirm_nooverwrite boolean true

# These lines answer the "Write changes to disk?" prompts
d-i partman-partitioning/confirm_write_new_label boolean true
d-i partman/choose_partition select finish
d-i partman/confirm boolean true
d-i partman/confirm_nooverwrite boolean true

### Packages
tasksel tasksel/first multiselect standard, ssh-server
d-i pkgsel/include string curl wget vim

### Bootloader
d-i grub-installer/only_debian boolean true
d-i grub-installer/with_other_os boolean true
d-i grub-installer/bootdev string /dev/sda

### Finish
d-i finish-install/reboot_in_progress note
```

All files necessary are created and We are ready to start boot process, Our iPXE iso will look for **boot-http.ipxe** so We will need to server this file. 

```bash
#Please use diffrent port if You have anoter service uses same port (It will require update iPXE ISO)
python3 -m http.server 80
```

![ipxe][12]

Durinng boot You will see Debian will install automaticly and will reboot itself: 

![ipxe][13]

And Debian is up and running.

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
[11]: ../assets/images/ipxe/ipxe-11.jpg
[12]: ../assets/images/ipxe/ipxe-12.jpg
[13]: ../assets/images/ipxe/ipxe-13.jpg







