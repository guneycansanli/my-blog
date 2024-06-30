---
title: "Automate Creating VM templates on Proxmox with Packer"
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
description: Automate Creating VM templates on Proxmox with Packer
---

# Introduction

This tutorial shows how automate creating VM template in Proxmox

---

# Prerequisites

1- Proxmox server

2- Packer 1.7.2 or higher

3- Proxmox API token with permissions to create VMs and templates

---

# Why Do We need Automate VM template creation?

1- You may have multiple Proxmox server or planned a migration.

2- You can keep your VM template as source code and manage it regarding your requirements

3- You can learn automate Linux installation(cloud-config) and cloud-init

4- You can create different templates using source code (Add/Edit/Delete new variables in source code) and You do not need to follow first manual installation method.

---

# Using Packer Automating 

![proxmox-packer-vm-temp][1]

Packer is a tool which can automate image builds. Packer has a lot of plugins today I will use proxmox-iso plugin. Plugin can react via Proxmox API so We will define some Proxmox variables in the Packer code and It will perform the all manual processes. Once Packer complte it's job We will have a clean base image (VM template) in proxmox.  

1- I have forked a project from  [https://gitlab.com/Saderi/packer-proxmox](https://gitlab.com/Saderi/packer-proxmox). The project is integrated with Gitlab CI(Using gitlab runner and execute job remote) but I modified it using from local since My Proxmox server is in same Network with my local (Where I run Packer).

2- You can clone my project from [https://github.com/guneycansanli/packer-proxmox](https://github.com/guneycansanli/packer-proxmox)

3- If you want to check original article [https://medium.com/@saderi/create-vm-templates-on-proxmox-with-packer-84723b7e3919](https://medium.com/@saderi/create-vm-templates-on-proxmox-with-packer-84723b7e3919)

---

# Creating Proxmox API Token

1- Open your Proxmox Web and click **Datacenter**

2- Find out **API Tokens** under **Permissons**

3- Click **Add** for creating new api token.

4- Choose your user for owner of API Token. I will use root user.

![proxmox-packer-vm-temp][2]

5- After you create API token copy your ID and token. You may need to save them in a safe place. 

![proxmox-packer-vm-temp][3]

---

# Packer Project

1- Let's inspect the project and check source code. Packer structure is not so complex so We can understand easily.

2- **credentials.pkr.hcl** is the file We keep out sensetive variables (API user, API Token and Proxmox server api url) 

![proxmox-packer-vm-temp][4]

4- **packer.pkr.hcl** is the file Packer uses plugings and initiliaze. I will not cover Packer structure in this guide If You want to check ref: [https://developer.hashicorp.com/packer/docs?ajs_aid=18a770ea-dd9a-474f-8f8e-05d0aade4cba&product_intent=packer](https://developer.hashicorp.com/packer/docs?ajs_aid=18a770ea-dd9a-474f-8f8e-05d0aade4cba&product_intent=packer)

5- **ubuntu-server-jammy.pkr.hcl** is the file We will define our VM Template's resources. Which image We will use or VM's hardware disk, cpu etc. 

6- We will also use cloud-init steps In the file (Running shell commands).

![proxmox-packer-vm-temp][5]

7- There is an important section **boot_command** This section can be different for other ISO images. For example We will use Ubuntu Jammy, When We run that image We need to push keyboard inputs to use auto boot loader. We will send command to the Prosmox our auto boot loader commands.

![proxmox-packer-vm-temp][6]

8- Packer will create a web server to send our commands to the Proxmox VM. 

```
autoinstall ds=nocloud-net\\;s=http://{{ .HTTPIP }}:{{ .HTTPPort }}/ ---<wait>
```

9- You may need to define a static ip. Some case Packer can not assing right IP but I did not have any problem liket that.

![proxmox-packer-vm-temp][7]


---


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


# Cleaning the Template

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

[1]: ../assets/images/prxmx-automate-vm-tmp/packer.jpeg
[2]: ../assets/images/prxmx-automate-vm-tmp/api-token.jpg
[3]: ../assets/images/prxmx-automate-vm-tmp/api-token-1.jpg
[4]: ../assets/images/prxmx-automate-vm-tmp/project-1.jpg
[5]: ../assets/images/prxmx-automate-vm-tmp/project-2.jpg
[6]: ../assets/images/prxmx-automate-vm-tmp/project-3.jpg
[7]: ../assets/images/prxmx-automate-vm-tmp/project-4.jpg
[8]: ../assets/images/prxmx-automate-vm-tmp/project-5.jpg
[9]: ../assets/images/prxmx-automate-vm-tmp/project-6.jpg

