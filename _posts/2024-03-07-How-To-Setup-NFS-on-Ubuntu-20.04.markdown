---
title: "How To Setup and NFS on Ubuntu 20.04"
layout: post
date: 2024-04-04 14:20
image: ../assets/images/nfs/main.jpg
headerImage: true
tag:
    - linux
    - ubuntu
    - nfs
category: blog
author: guneycansanli
description: How To Setup and NFS on Ubuntu 20.04
---

# NFS Introduction ?

NFS is a protocol for distributed file systems, enabling you to mount remote directories on your server. This facilitates managing storage from different locations and writing to it from multiple clients. It offers a standard and efficient method for accessing remote systems over a network, ideal for regular resource sharing. This guide covers installing NFS software on Ubuntu 20.04, configuring mounts on both server and client sides, and managing remote shares through mounting and unmounting.

---

# Prerequisites

-   2 Ubuntu VM, or Server, 1 is for host 1 is for client.

---

# Installing the Dependencies

On the host server/vm:

1- Install the nfs-kernel-server package, which will allow you to share your directories.

```
    sudo apt update
    sudo apt install nfs-kernel-server
```

![nfs][1]

On the client server/VM :

1- We have done host server so now We can configure client server.

2- To set up the client server, it's essential to install the nfs-common package, enabling NFS capabilities while excluding server functionalities. Remember to update the local package index beforehand to ensure you're working with the latest information.

```
    sudo apt update
    sudo apt install nfs-common
```

![nfs][2]

3- After above installation finish. We are reayd to setup share diretories on the host server/Vm.

# Creating the Share Directories on the Host

1- Okay, We are ready to setup directories for sharing to clients but there are different settings for it. We will set 2 different various of that.

2- NFS server does not allow that superusers on the client cannot write files as root. So basiclly client have a mount from NFS so even client's root user can not write files by default.

3-However You can use other method like trusted user in client serverfor managing NFS but It comes with also couple risks, as such a user could gain root access to the entire host system.

4- Let's figure it out some exaple uses after We learnt some informations and riks.

# Example 1 Exporting a General Purpose Mount

1- let's say we're setting up a way to share files between computers using NFS. We're making it so that even if someone has full control over their computer (like being an admin or having root access), they won't automatically have the same control over the shared files. This setup can be handy for storing files from a content management system or for letting users share project files without worrying about someone messing with important system stuff.

2- First, make the share directory on host server/VM

```
    sudo mkdir /var/nfs/general -p
```

3- We created the directory via sudo so owner and group will be **root** .

![nfs][3]

4- NFS uses any root operations on the client to the nobody:nogroup as a security measure. Therefore, we need to change the directory ownership to match those credentials. So let's chown the diretory.

![nfs][4]

5- Okay , We are ready share/export this directory.

# Configuring the NFS Exports on the Host Server

1- Next, We will setup the NFS configuration file to set up the sharing of directory . On the host machine, open the **/etc/exports** file in your text editor via root.

2- The file sysntax should be **directory_to_share** **client(share_option1,...,share_optionN)**

![nfs][5]

3- We need to create a line for each of the directories that we plan to share. so make sure using right directory path and client IP.

4- In my case I will share **/var/nfs/general** directory and my client LAN ip (ubuntu-slave) is **192.168.64.21**

5- My conf : **/var/nfs/general 192.168.64.21(rw,sync,no_subtree_check)**

![nfs][6]

THIS ARTICLE IS IN PROGRESS-----------

2- Then install the dependencies

```
    sudo apt install ca-certificates curl openssh-server postfix tzdata perl
```

3- During the postfix installation, a configuration window will appear. Choose “Internet Site” and enter your server’s hostname as the mail server name. This will allow GitLab to send email notifications.

4- Choose “Internet Site” and then select OK.
![gitlab][1]

5- You should also enter hostname. Mail name
![gitlab][2]

6- Now that you have the dependencies installed, you’re ready to install GitLab.

---

# Installig GitLab

1- With the dependencies in place, you can install GitLab. This process leverages an installation script to configure your system with the GitLab repositories.

2- Move into the /tmp directory and then download the installation script:

```
    cd /tmp
    curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh
```

![gitlab][3]

3- Run the installer.

```
    sudo bash /tmp/script.deb.sh
```

![gitlab][4]

4- The script sets up your server to use the GitLab maintained repositories. This lets you manage GitLab with the same package management tools you use for your other system packages. Once this is complete, you can install the actual GitLab application with apt

```
    sudo apt install gitlab-ce
```

5- This installs the necessary components on your system.
![gitlab][5]

---

# Editing the GitLab Configuration File

1- Before you can use the application, update the configuration file and run a reconfiguration command. First, open GitLab’s configuration file with your preferred text editor

```
    sudo vi /etc/gitlab/gitlab.rb
```

2- Find the external_url configuration line. Update it to match your domain if you have DNS and public(WAN) IP and make sure to change http to https to automatically redirect users to the site protected by the Let’s Encrypt certificate.

3- At that point I will use my localhost only. I have not public DNS and Public IP.
![gitlab][6]

4- Once you’re done making changes, save and close the file. You can also enable couple of more options like SSL or contact email.

Run the following command to reconfigure GitLab.

```
    sudo gitlab-ctl reconfigure
```

5- This is a automated process so You should wait until command configure GitLAb for you. You will see long output on the terminal.
![gitlab][7]

---

# Access GitLab Web Interface

1- With GitLab installed and configured, open your web browser and enter your server’s IP address or hostname.

2- User Name: **root**

3- Password : **Find from /etc/gitlab/initial_root_password**

```
    sudo cat /etc/gitlab/initial_root_password
```

![gitlab][8]

4- If you run GitLab in your LAN you can access LAN ip from browser.
![gitlab][9]

![gitlab][10]

---

---

---

> :blush: :star: :boom: :fire: :+1: :eyes: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/gitlab/gitlab1.jpg
[2]: ../assets/images/gitlab/gitlab2.jpg
[3]: ../assets/images/gitlab/gitlab3.jpg
[4]: ../assets/images/gitlab/gitlab4.jpg
[5]: ../assets/images/gitlab/gitlab5.jpg
[6]: ../assets/images/gitlab/gitlab6.jpg
[7]: ../assets/images/gitlab/gitlab7.jpg
[8]: ../assets/images/gitlab/gitlab8.jpg
[9]: ../assets/images/gitlab/gitlab9.jpg
[10]: ../assets/images/gitlab/gitlab10.jpg

```

```
