---
title: "How to Install Proxmox 8.2 VE to Bare Metal"
layout: post
date: 2024-05-21 14:20
image: ../assets/images/proxmox/proxmox-main.jpg
headerImage: true
tag:
    - boot
    - proxmox
    - linux
category: blog
author: guneycansanli
description: How to Install Proxmox 8.2 VE to Bare Metal
---

# Introduction

This tutorial shows how to boot/install Proxmox 8.2 VE to Bare Metal.  

---

# What is Proxmox?

Proxmox VE is an open-source server platform for enterprise virtualization. You can set up and oversee virtual environments using either a web interface or a command line, offering straightforward and quick access.

# Prerequisites

1- PC, Laptop, Mini PC or Server

2- 64bit CPU.

3- Enough RAM/Memory (At least 8 GB RAM would be better for creating VMs)

3- A USB stick with at least 2GB storage.

# Step 1: Download Proxmox ISO Image

1- We need to dowload ISO to create bootable USB stick.

2- The best way dowload Proxmox image is official   [Proxmox ISO Downloads page](https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso)

3- You can find out latest Proxmox ISO top of the list. At the time of writing, the latest version was 8.2-1

4- Click Download and save the latest ISO.

![proxmox][30]


# Step 2: Prepare Installation USB (Boot)

1- In this step We need to make a bootable USB stick. Depends on which OS You are using but process similar for all OS. 

2- I am working on MacOS Ventura so I will precedure via MacOS, If you are using Windows I would recommend Rufus (You can dowload it and burn ISO to Your USB Stick).

3- I will use terminal so I do not need to download any tools.

4- Attach the target USB drive to the Mac if you haven’t done so yet, then launch Terminal
Type the following command to print a list of attached volumes on the Mac:

```
diskutil list
```

5- You can see you USB and determine from name or size of USB.

6- We should umount disk(USB's All volumes) using same command with an option which is **umountDisk**

```
diskutil umountDisk /dev/<disk-identifier>
```
![proxmox][1]

7- Now We are ready to burn/write proxmox ISO to the USB. Make sure You run this command via **sudo**

```
sudo dd if=/path/image.iso of=/dev/r(IDENTIFIER) bs=1m
```

8- After USB is ready We can eject the USB via:
```
diskutil eject /dev/<<disk-identifier>>
```
![proxmox][2]


# Step 3: Launch the Proxmox Installer

1- We are ready for boot Proxmox ISO so move to the server (machine) where you want to install Proxmox and plug in the USB device.

2- While target device is booting press ESC either F2, F10, F11, or F12 to reach BIOS screen.

3- We need to change boot order or choose boot from USB option.

4- Change the screen to Boot Option via keyboard and change boot order to boot from USB , You should able to see plugged USB device under boot section.

![proxmox][3]

5- Save and exit.

6- The Proxmox VE menu should be appeared. Select Install Proxmox VE to start the standard installation(Graphical).

![proxmox][4]

7- Read and accept the EULA to continue.

![proxmox][5]

8- Choose the target hard disk where you want to install Proxmox. Click Options to specify additional parameters, such as the filesystem. By default, it is set to **ext4**. I will keep as defafult and click next.

![proxmox][6]

9- Next, country, time zone , and keyboard layout setting will be shown. You can enter your informations and click next.

![proxmox][7]

10- We need to create a root password We will use root password SSH and Web GUI, retype the password to confirm, and type in an email address for system administrator notifications.

![proxmox][8]

11- Last step is network configuration. In this section can be different related your home network but most of people uses 198.168.1.1 gateway as default. Select the management interface, a hostname for the server, an available IP address, the default gateway, and a DNS server. During the installation process, use either an IPv4 or IPv6 address. To use both, modify the configuration after installing. I am booting the Proxmox next to my desk via attich moniyot and keyboard but after I done I will plug Ethernet Cable from My router to my Mini PC and I will have **198.168.1.11** ip. 

12- The installer summarizes the selected options. After confirming everything is in order, press Install.

![proxmox][9]

13- After installation is done You can remove USB from the PC and let it to boot.


# Step 4: Access Proxmox

1- If You have monitor connected to your Proxmox Server You can see the root login and IP of the server. 

2- After installation I removed monitor and keyboard adn I will placed my server next to my router also plugged Ethernet cables.

![proxmox][10]

3- You can reach to Proxmox server While you are in same LAN network.

4- You can SSH or to Web Server via Web Browser.

5- Since Proxmox easy to use and configure over Web I will use IP of Proxmox and port **8006** which is Proxmox port.

6- After navigating to the required IP address, you may see a warning message that the page is unsafe because Proxmox VE uses self-signed SSL certificates. Click the IP link to proceed to the Proxmox web management interface.

![proxmox][11]

7-  To access the interface, log in as root and provide the password you set when installing Proxmox.

![proxmox][12]

8- A dialogue box pops up saying there is no valid subscription for the server. Proxmox offers an optional add-on service to which you can subscribe. To ignore the message, click OK.

![proxmox][13]

9- You can discover Proxmox GUI there are tons of informations and also actions. You can check your server's summary. Under **Datacenter**. 

![proxmox][14]

10- You can also check Storage for your VMs. Proxmox uses a different LVM for your VMS so root or management partitions are different from your VMs partitions (Proxmox uses LVM)

![proxmox][15]

11- We covered all steps for building a new Proxmox server. I will try to learn and explore Proxmox. 

Thanks for reading...

---

---

---

---

> :+1: :+1: :+1: :+1: :+1: :+1: :+1: :+1:

---

Guneycan Sanli.

---

[30]: ../assets/images/proxmox/proxmox-iso.jpg
[1]: ../assets/images/proxmox/proxmox-1.jpg
[2]: ../assets/images/proxmox/proxmox-2.jpg
[3]: ../assets/images/proxmox/proxmox-boot-1.jpg
[4]: ../assets/images/proxmox/proxmox-boot-2.jpg
[5]: ../assets/images/proxmox/proxmox-boot-3.jpg
[6]: ../assets/images/proxmox/proxmox-boot-4.jpg
[7]: ../assets/images/proxmox/proxmox-boot-5.jpg
[8]: ../assets/images/proxmox/proxmox-boot-6.jpg
[9]: ../assets/images/proxmox/proxmox-boot-7.jpg
[10]: ../assets/images/proxmox/proxmox-boot-9.jpg
[11]: ../assets/images/proxmox/proxmox-3.jpg
[12]: ../assets/images/proxmox/proxmox-4.jpg
[13]: ../assets/images/proxmox/proxmox-5.jpg
[14]: ../assets/images/proxmox/proxmox-6.jpg
[15]: ../assets/images/proxmox/proxmox-7.jpg
