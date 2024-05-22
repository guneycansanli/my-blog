---
title: "Creating Image with Recuezilla"
layout: post
date: 2024-05-21 14:20
image: ../assets/images/rescuezilla/rescuezilla-main.jpg
headerImage: true
tag:
    - rescuezilla
    - image
    - linux
category: blog
author: guneycansanli
description: Creating Image with Recuezilla
---

# Introduction

In this guide , I will create my Debian VM's image with rescuezilla.

---

# Prerequisites

1- Bootable Rescuezilla USB.

2- Server/VM.

3- Another Server/VM for saving/storing image over SSH.

# Plug Recuezilla image to VM

1- I am using MacOS and UTM/QEMU so If You are using another virtulization engine. You should plug-in Rescuezilla to target VM which is We want to create image.

2- There is a section for attach USB to VM in UTM When you start VM you can transfer memory from your host machine.

![rescue][1]

3- While VM booting up press ESC and change your boot options for using USB boot.

4- While Rescuezilla booting up It will ask you Language and start options.

5- Start Rescuezilla and You will able to see Rescuezilla desktop environment.

![rescue][2]

6- Once Rescuezilla booted up It will start the Rescuezilla program.

# Backup VM's Image

1- Rescuezilla has basic UI and there are couple of diffrent options.

2- Today We will create/snapshot an image so We should choose **Backup**

3- When You click **Backup**, Recuezilla will scan all disks/drivers on the VM (USB plugged device).

![rescue][3]

4- As you can see I have QEMU disk , It is my VM's device that I want to create image.

5- After We choose target device. Rescuezilla will show us all partitions on the disk. We should choose partitions to save.

![rescue][4]

6- In that part I will not change anything and I will click next.

7- We are at **STEP 3** Now We need to select destination drive which will store the image.

8- I want to store/save my image in another VM , so I will choose **Shared over a network** option, You may want to store the image in an USB but fot this option You need to attach another USB to the VM.

9- If you want to sahre over network there are diffrent options for it too.

10- I have already a VM and SSH service is running. I will use SSH option.

![rescue][5]

11- If You will use SSH make sure You have not accessing issue. I haev also created a folder for storing the image.

![rescue][6]

12- Here is my destination VM's configs

![rescue][7]

13- Now We are ready for crate image , just click next and wait until Rescuezilla mount process.

![rescue][8]

14- We are at **STEP 4** , You can choose destination folder for saving image since I have already define it I will click next.

15- At **STEP 5** We can rename or image name Rescuezilla uses **DATE-img-rescuezilla** default. I will rename it as debian10 since My VM is debian10.

![rescue][9]

16- **STEP 6** Now We should choose our image's type (compressed or not). I will use gzip so It will be fast. It depends on your reqirement You may choose different options.

![rescue][10]

17- **STEP 7** Rescuezilla will confirm our configurations and also there is a check-box for filesystem check You can ignore fsck for your partitions Rescuezilla will check FS as default.

![rescue][11]

18- **STEP 8** and now Rescuezilla has started to create image. We can choose completed task action Do nothing, Shutdown or Reboot.

![rescue][12]

19- If you want to make sure the image is creating, You can check target Server/VM/Destination.

![rescue][13]

Thanks for reading...

---

---

---

---

> :+1: :+1: :+1: :+1: :+1: :+1: :+1: :+1:

---

Guneycan Sanli.

---

[1]: ../assets/images/rescuezilla/rescuezilla1.jpg
[2]: ../assets/images/rescuezilla/rescuezilla2.jpg
[3]: ../assets/images/rescuezilla/rescuezilla3.jpg
[4]: ../assets/images/rescuezilla/rescuezilla4.jpg
[5]: ../assets/images/rescuezilla/rescuezilla5.jpg
[6]: ../assets/images/rescuezilla/rescuezilla6.jpg
[7]: ../assets/images/rescuezilla/rescuezilla7.jpg
[8]: ../assets/images/rescuezilla/rescuezilla8.jpg
[9]: ../assets/images/rescuezilla/rescuezilla9.jpg
[10]: ../assets/images/rescuezilla/rescuezilla10.jpg
[11]: ../assets/images/rescuezilla/rescuezilla11.jpg
[12]: ../assets/images/rescuezilla/rescuezilla12.jpg
[13]: ../assets/images/rescuezilla/rescuezilla13.jpg
