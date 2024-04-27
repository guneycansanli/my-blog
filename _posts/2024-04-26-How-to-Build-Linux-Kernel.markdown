---
title: "How to Build Linux Kernel"
layout: post
date: 2024-04-20 14:20
image: ../assets/images/kernel/main.jpg
headerImage: true
tag:
    - linux
    - debian
    - kernel
category: blog
author: guneycansanli
description: How to Build Linux Kernel
---

# Introduction

In this guide , I will build a kernel and also I will Update the Bootloader for using my own kernel.

---

# Prerequisites

1- A system running Linux / I will use Debian buster VM
2- 12GB of available space on the hard drive

# Download the Source Code

So We will start with choosing a existing kernel and We will modify it. We can find current kernel verisons in [kernel.org](https://www.kernel.org/) and dowload the lastest kernle version. The file is compressed source code of kernel. I will use kernel 6.8.7 which is latest kernel on 04/26/2024.

1- Visit the link and chekc kernel versions.

![kernel][1]

2- We decide out kernel version so , We need to doeload it to out VM , We can use wget to dowload kernel.

```
wget https://cdn.kernel.org/pub/linux/kernel/v6.x/linux-6.8.7.tar.xz
```

![kernel][2]

3- The output shows the “saved” message when the download completes.

# Extract the Source Code

1- Since We have compressed file as .tar, We should extact the file.

2- Run the tar command to extract the source code

```
tar xvf linux-6.8.7.tar.xz
```

![kernel][3]

# Install Required Packages

1- Before building new kernel We need to install required packages You can install any package you want to add to the new kernel.

2- Let's use apt for insalling reiqured packages.

```
apt-get install git fakeroot build-essential ncurses-dev xz-utils libssl-dev bc flex libelf-dev bison -y
```

3- If You want to know What the packages do, You can check the below:

![kernel][4]

# Configure Kernel

1- The Linux kernel source code comes with the default configuration. However, You can adjust it to your needs.

2- Change your directory to kernel's folder.

```
cd linux-6.8.7
```

3- Copy the existing Linux config file using the **cp** command

```
cp -v /boot/config-$(uname -r) .config
```

![kernel][5]

4- To make changes to the configuration file, We should run make **menu config**.

```
make menuconfig
```

5- The command launches several scripts that open the configuration menu.

![kernel][6]

6- The configuration menu includes options such as firmware, file system, network, and memory settings. Use the arrows to make a selection or choose **Help** to learn more about the options. When you finish making the changes, select **Save**, and then **exit** the menu.

![kernel][7]

![kernel][8]

![kernel][9]

7- You may change your configureation and sanve it. I just click save to show you but I did not do any change on my configuration.

# Build the Kernel

1- We are ready to build our kernel after We done configuration of new kernel.

2- Start building the kernel by running **make** command

```
make
```

3- The process of building and compiling the Linux kernel may takes time.

4- The terminal lists all Linux kernel components: memory management, hardware device drivers, filesystem drivers, network drivers, and process management.

![kernel][10]

4- Install the required modules after build is completed.

```
make install
```

![kernel][11]

5- Once is done You will see **done** output on the terminal.

# Update the Bootloader

1- We successfully builded new kernel. We can test it with changing our grub bootloader.

2- The GRUB bootloader is the first program that runs when the system powers on.

3- The **make install** command performs this process automatically, but you can also do it manually.

4- Update the initramfs to the installed kernel version.

```
update-initramfs -c -k 6.8.7
```

5- Update the GRUB bootloader.

```
update-grub
```


6- The terminal prints out the process and confirmation message:

# Test

1- We can test autofs with shutdown the NFS server and reboot client and verify It can boot-up properly.

2- We can test restart autofs service after NFS server bootup and We can verify It is working and mount the folder the NFS folder in client machine.

3- I powered-off NFS server and reboot client server. Client successfully rebooted and there is not NFS mount at this point.

![autofs][6]

4- Now I will power-on NFS server and I will restart autofs in client server.

```
sudo service autofs restart
sudo service autofs status
```

5- After I restarted aufofs service I could see NFS mount to client server.

![autofs][7]

---

---

---

---

> :+1: :+1: :+1: :+1: :+1: :+1: :+1: :+1:

---

Guneycan Sanli.

---

[1]: ../assets/images/kernel/kernel1.jpg
[2]: ../assets/images/kernel/kernel2.jpg
[3]: ../assets/images/kernel/kernel3.jpg
[4]: ../assets/images/kernel/kernel4.jpg
[5]: ../assets/images/kernel/kernel5.jpg
[6]: ../assets/images/kernel/kernel6.jpg
[7]: ../assets/images/kernel/kernel7.jpg
[8]: ../assets/images/kernel/kernel8.jpg
[9]: ../assets/images/kernel/kernel9.jpg
[10]: ../assets/images/kernel/kernel10.jpg
[11]: ../assets/images/kernel/kernel11.jpg
