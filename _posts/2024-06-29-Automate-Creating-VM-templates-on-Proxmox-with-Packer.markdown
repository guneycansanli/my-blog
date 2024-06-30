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

Packer is a tool which can automate image builds. Packer has a lot of plugins today I will use proxmox plugin. Plugin can react via Proxmox API so We will define some Proxmox variables in the Packer code and It will perform the all manual processes. Once Packer complte it's job We will have a clean base image (VM template) in proxmox.  

1- I have forked a project from  [https://gitlab.com/Saderi/packer-proxmox](https://gitlab.com/Saderi/packer-proxmox). The project is integrated with Gitlab CI(Using gitlab runner and execute job remote) but I modified it using from local since My Proxmox server is in same Network with my local (Where I run Packer).

2- You can clone my project from [https://github.com/guneycansanli/packer-proxmox](https://github.com/guneycansanli/packer-proxmox)

3- If you want to check original article [https://medium.com/@saderi/create-vm-templates-on-proxmox-with-packer-84723b7e3919](https://medium.com/@saderi/create-vm-templates-on-proxmox-with-packer-84723b7e3919)

---

# Creating Proxmox API Token

1- Open your Proxmox Web and click **Datacenter**

2- Find out **API Tokens** under **Permissons**

3- Click **Add** for creating new api token.

4- Choose your user for owner of API Token. I will use root user.

5- We need to un-check privilage because We need to have all access.  

![proxmox-packer-vm-temp][2]

5- After you create API token copy your ID and token. You may need to save them in a safe place. 

![proxmox-packer-vm-temp][3]

---

# Packer Project

1- Let's inspect the project and check source code. Packer structure is not so complex so We can understand easily.

2- **credentials.pkr.hcl** is the file We keep out sensetive variables (API user, API Token and Proxmox server api url) 

![proxmox-packer-vm-temp][4]

4- **packer.pkr.hcl** is the file Packer uses plugings and initiliaze. I will not cover Packer structure in this guide If You want to check ref: [https://developer.hashicorp.com/packer/docs?ajs_aid=18a770ea-dd9a-474f-8f8e-05d0aade4cba&product_intent=packer](https://developer.hashicorp.com/packer/docs?ajs_aid=18a770ea-dd9a-474f-8f8e-05d0aade4cba&product_intent=packer)

![proxmox-packer-vm-temp][5]

5- **ubuntu-server-jammy.pkr.hcl** is the file We will define our VM Template's resources. Which image We will use or VM's hardware disk, cpu etc. 

6- We will also use cloud-init steps In the file (Running shell commands).

![proxmox-packer-vm-temp][6]

7- There is an important section **boot_command** This section can be different for other ISO images. For example We will use Ubuntu Jammy, When We run that image We need to push keyboard inputs to use auto boot loader. We will send command to the Prosmox our auto boot loader commands.

8- Packer will create a web server to send our commands to the Proxmox VM. 

```
autoinstall ds=nocloud-net\\;s=http://{{ .HTTPIP }}:{{ .HTTPPort }}/ ---<wait>
```

9- You may need to define a static ip. Some case Packer can not assing right IP but I did not have any problem liket that.

![proxmox-packer-vm-temp][7]

![proxmox-packer-vm-temp][8]

![proxmox-packer-vm-temp][9]

10- We need to use a file for Provisioning the VM Template for Cloud-Init Integration. We need to copy the file under **/etc/cloud/cloud.cfg.d/** 

![proxmox-packer-vm-temp][10]

11- **user-data** is the file We defined our auto VM installation variables. Packer will send out config to VM USing via web server during building the VM. 

![proxmox-packer-vm-temp][11]

---


# Running Packer Project

1- We are ready to run Packer and start to crate VM and also VM template.

2- We need to run packer init command for using proxmox plugin.

```
packer init packer.pkr.hcl 
```

3- Before running packer build We can check our config and validate We have no any issue in our code.

4- Open a terminal in same directory with Packer files and run packer validate command also We will provide our credantials.

```
packer validate -var-file "credentials.pkr.hcl" ubuntu-server-jammy.pkr.hcl
```

5- If It gives **The configuration is valid.** output We are good to run build command and We can also Watch the process from Proxmox server real-time.

6- One note before I run commad I deleted my previous VM template because I will use same VM name and also same VM ID Proxmox does not allow to that.

7- Run build command and watch the process.

```
packer build -var-file "credentials.pkr.hcl" ubuntu-server-jammy.pkr.hcl
```
![proxmox-packer-vm-temp][11]

![proxmox-packer-vm-temp][12]

8- Here is process , We do not touch anything on Proxmox and Packer does all steps for us.

9- Packer will wait until SSH connectin is successfully and will do Cloud-init or What We define in shell provisioner.

10- After all process done, We can able to see from logs as well as in Proxmox UI

![proxmox-packer-vm-temp][13]

![proxmox-packer-vm-temp][14]

11- We have succesfully created new VM template.


# Running VM from Template

1- We have new template and We just need to clone our current template as as new VM. Since We set up cloud-init We need to define cloud init setting in Proxmox.

2- Clone the image and deploy new image then put your settings to the cloud-init and regenerate the image.

![proxmox-packer-vm-temp][15]

![proxmox-packer-vm-temp][16]

3- Finally We have new VM from template via cloud-init settings.

![proxmox-packer-vm-temp][17]


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
[2]: ../assets/images/prxmx-automate-vm-tmp/packer-api-token.jpg
[3]: ../assets/images/prxmx-automate-vm-tmp/packer-api-token-1.jpg
[4]: ../assets/images/prxmx-automate-vm-tmp/project-1.jpg
[5]: ../assets/images/prxmx-automate-vm-tmp/project-2.jpg
[6]: ../assets/images/prxmx-automate-vm-tmp/project-3.jpeg
[7]: ../assets/images/prxmx-automate-vm-tmp/project-4.jpg
[8]: ../assets/images/prxmx-automate-vm-tmp/project-5.jpg
[9]: ../assets/images/prxmx-automate-vm-tmp/project-6.jpg
[10]: ../assets/images/prxmx-automate-vm-tmp/project-7.jpg
[11]: ../assets/images/prxmx-automate-vm-tmp/project-8.jpg
[12]: ../assets/images/prxmx-automate-vm-tmp/project-9.jpg
[13]: ../assets/images/prxmx-automate-vm-tmp/project-10.jpg
[14]: ../assets/images/prxmx-automate-vm-tmp/project-11.jpg
[15]: ../assets/images/prxmx-automate-vm-tmp/project-12.jpg
[16]: ../assets/images/prxmx-automate-vm-tmp/project-13.jpg
[17]: ../assets/images/prxmx-automate-vm-tmp/project-14.jpg

