---
title: "Creating Virtual Machine Templates in Proxmox"
layout: post
date: 2024-06-19 14:20
image: ../assets/images/prxmx-vm-template/main.jpg
headerImage: true
tag:
    - vm
    - proxmox
    - linux
category: blog
author: guneycansanli
description: Creating Virtual Machine Templates in Proxmox
---

# Introduction

This tutorial shows how to create virtual machine template in Proxmox

---

# Prerequisites

1- Created VM in Proxmox (Ubuntu or Debian)

2- SSH connection to VM


# Preparing VM Template 

1- Connecting to a Virtual Machine (VM): We're using SSH (a secure way to access a computer over a network) to connect to a virtual machine.

2- Creating a VM Template: We're preparing a template to create multiple VMs from this single template.

3- We need to do some changes on our base(template) image

![proxmox-img][1]

Example Problem - SSH Host Keys:

SSH Host Keys: These are unique identifiers created when you first install SSH on a machine. They help ensure that the machine you connect to is really the machine you expect.

Identical Host Keys: If every VM created from the template has the same SSH host keys, your SSH client (the tool you use to connect to the VM) might get confused. It might think there’s a security problem because it expects each machine to have unique keys.

4- So When We installl SSH on the server , It generates ssh host keys If these ssh host keys are the same on every VM then SSH client is going to be very confused every time You connect your servers. 

![proxmox-img][2]

5- In that case We should use cloud-init package which is most likely already installed. We can use **apt search <>** comamnd to check.

6- You can see cloud-init package is currently installed. IF you do not have it already you can install via **sudo apt install clud-init**

![proxmox-img][3]

# What is cloud-init 

1- cloud-init is usefull package and using for many different things. In this guide I will not focus cloud-init but TL;DR the short answer is quite a bit actually cloud init features a number of modules that you can use to essentially master an image or in our case a template in a nutshell it allows you to automate a series of tasks that you normally would do every time you set up a new linux server and when it comes to resetting the ssh host keys that's a given that's one of the many things that cloud init does for us. 

2- So We have cloud-init installed and reayd to use but before using it We need to remove host keys.

3- Change your directory to **/etc/ssh/** and remove ssh_host_* 
```
cd /etc/ssh/
sudo rm ssh_hosts_*
```
![proxmox-img][4]

4- You can see all the host keys have been deleted and with the host keys being absent that's going to help trigger cloud init to regenerate those for us.

5- Ssh host key should be reset when We mastered our template and also there is additional file that We need to do empty as well.

6- We need to make empty to **machine-id**. Machine ids are should be uniqe for serves/vms and  not every linux distribution is going to have this particular file right here but ubuntu has it and we need to make sure that this is emptied out.

7- We should not delete this file becuase It is not going to work, We will use **truncate** with -s flag which is stand for size command for empty the file completely.

```
sudo cat /etc/machine-id
sudo truncate -s 0 /etc/machine-id
sudo cat /etc/machine-id
```

![proxmox-img][5]

8- There is also symbolic link for machine-id and this is a different machine id file actually it's located in **/var/lib/dbus/machine-id** If it is already linked You do not need to that but We can check and link the file to **/etc/machine-id** file.


```
sudo ls -lrth /var/lib/dbus/machine-id
sudo ln -s  /etc/machine-id /var/lib/dbus/machine-id
sudo ls -lrth /var/lib/dbus/machine-id
```
![proxmox-img][6]


# Clenaing the Template

1- Before crateing template We should cleaning current image. we could possibly get it and i'm notgoing to go into too much detail on this there's all kinds of things that you could do to clean up an image.

2- Clean apt database

```
sudo apt clean
```

3- Remove un-using packages/orphan packages that are just installed for no apparent reason or no longer have a use and what sudo apt auto remove does is that allows you to remove those orphan packages.

```
sudo apt autoremove
```

![proxmox-img][7]


# Creating Template

1- Before creating a template. If you need to install or setup anything You can do it and crate a template from it. Since I use Proxmox I would recommned you to istannnl QEMU Agent and with that way You do not need to install QEMU agent after creating VM in promox everytime. 

2- I have already installed it so I will skip that part and shut down my VM for crating template now.

```
sudo poweroff
```

3- We can open proxmox web console and make sure VM is stopped state and We can right click to the VM. We should see **Convert to Template** option. We just need to click it and Proxmox will ask for a confirmation.

![proxmox-img][8]

4- After couple of seconds the VM icon will change and We will have it as an template.

![proxmox-img][9]

5- Before using it let's remove attached drive/cd/dvd from template VM.

6- What we're going to do is click on hardware and what we're going to do here is actually remove the attachment to the iso for the virtual disk so i'll click edit then i'll click do not use any media i'll click okay so now that's done.

![proxmox-img][10]

7- Now We need to add **cloud-init drive** to the template.

8- Next i'll click add and then what i'll do is add a cloud init drive for the storage i'll drop this down and i'll choose local lvm let's go ahead and create it and as you can see here we have a cloud init drive listed in the list of hardware here.

![proxmox-img][11]

![proxmox-img][12]

![proxmox-img][13]

9- We can edit cloud-init now and set some configuration for template. Go and click **Cloud-init** tab item.

10- In there We can set deafult user of template password, DNS , ssh key etc.

11- After seeting up cloud-init We can regenare the template or You may already set that while you had were seeting your VM.

![proxmox-img][14]

12- Now We are ready to use out new template.

13- Let's right click the VM template and teh click **clone**

14- We can enter VM name and also You should choose full clone. Full clone is better it's an entire copy of that particular template it's not a differential or anything like that it's a full-blown copy the target storage.

![proxmox-img][15]

15- After couple seconds We will see new created VM from template and You can start the VM.

![proxmox-img][16]

16- Once our VM up and running I recommend that we do is update the **hostname** and **hosts** the reason for that every created VMs from template will have same hostname so We need to change VM hostname.

```
sudo vi /etc/hostname
sudo vi /etc/hosts
```

17- We successsfully done all steps for creating template VM in Proxmox. 

Thanks for reading...

---

---

---

---

> :+1: :+1: :+1: :+1: :+1: :+1: :+1: :+1:

---

Guneycan Sanli.

---

[1]: ../assets/images/prxmx-vm-template/prxmx-img-1.jpg
[2]: ../assets/images/prxmx-vm-template/prxmx-img-2.jpg
[3]: ../assets/images/prxmx-vm-template/prxmx-img-3.jpg
[4]: ../assets/images/prxmx-vm-template/prxmx-img-4.jpg
[5]: ../assets/images/prxmx-vm-template/prxmx-img-5.jpg
[6]: ../assets/images/prxmx-vm-template/prxmx-img-6.jpg
[7]: ../assets/images/prxmx-vm-template/prxmx-img-7.jpg
[8]: ../assets/images/prxmx-vm-template/prxmx-img-8.jpg
[9]: ../assets/images/prxmx-vm-template/prxmx-img-9.jpg
[10]: ../assets/images/prxmx-vm-template/prxmx-img-10.jpg
[11]: ../assets/images/prxmx-vm-template/prxmx-img-11.jpg
[12]: ../assets/images/prxmx-vm-template/prxmx-img-12.jpg
[13]: ../assets/images/prxmx-vm-template/prxmx-img-13.jpg
[14]: ../assets/images/prxmx-vm-template/prxmx-img-14.jpg
[15]: ../assets/images/prxmx-vm-template/prxmx-img-15.jpg
[16]: ../assets/images/prxmx-vm-template/prxmx-img-16.jpg
