---
title: "Send Push Notifications to Your Phone or Desktop via NTFY (Notify)"
layout: post
date: 2024-04-12 14:20
image: ../assets/images/ntfy/main.jpg
headerImage: true
tag:
    - linux
    - debian
    - NTFY
category: blog
author: guneycansanli
description: Send Push Notifications to Your Phone or Desktop via NTFY
---

# What is NTFY?

NFTY (Notify) is a simple HTTP based notification service. We can send notifications to your phone or desktop via scripts from any computer. It is a open source tool. Let's image that You are trying to do some long processes like **nmap** or dowloading a large file from internet or runnning a script that takes long time anything else... , so with notify We can have a notification after our process complete.

---

# Prerequisites

-   1 server/VM (I will use Debain Buster VM)
-   Docker installed on the box.
-   Doowloaded nfty app Your target device (Phone?)

---

# Installing Docker

Let's get started with Docker installation:

1- Update your repositories and install docker.

```
sudo apt update

# install a few prerequisite packages which let apt use packages over HTTPS:
sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common

#Then add the GPG key for the official Docker repository to your system:
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -

#Add the Docker repository to APT sources:
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"

#Next, update the package database with the Docker packages from the newly added repo:
sudo apt update

#Make sure you are about to install from the Docker repo instead of the default Debian repo:
apt-cache policy docker-ce

#Notice that docker-ce is not installed, but the candidate for installation is from the Docker repository for Debian 10 (buster).

# Finally, install Docker:
sudo apt install docker-ce
```

![nfty][1]

3- You may need to start docker service

```
    systemctl start docker && systemctl enable docker && systemctl status docker
```

# Running NFTY in docker Container

On the client server/VM :

1- I will run nfty in docker container but You may run/intsall on your host machine.
2- Here is details for other hosts [nofty](https://docs.ntfy.sh/install/)
3- Run/serve nfty in docker container.

```
    docker run -p 80:80 -itd binwiederhier/ntfy serve
```

![nfty][2]

3- Basiclly that is it. Server is setup and running. Let's chekc the WEB nfty interface.

---

# Access nfty Web Interface

1- You can access your nfty via port forwarding.
2- I used port 80 so I will open a port to my VM or You can use your VM's LAN IP to access.

![nfty][3]

![nfty][4]


---

# Example Exporting a General Purpose Mount

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

---

# Configuring the NFS Exports on the Host Server

1- Next, We will setup the NFS configuration file to set up the sharing of directory . On the host machine, open the **/etc/exports** file in your text editor via root.

2- The file sysntax should be **directory_to_share** **client(share_option1,...,share_optionN)**

![nfs][5]

3- We need to create a line for each of the directories that we plan to share. so make sure using right directory path and client IP.

4- In my case I will share **/var/nfs/general** directory and my client LAN ip (ubuntu-slave) is **192.168.64.21**

5- My conf : **/var/nfs/general 192.168.64.21(rw,sync,no_subtree_check)**

![nfs][6]

6- I will not explain the options **rw,sync,no_subtree_check** You may search the options and try different options. basiclly We gave both read and write access to the volume.

7- Once you update your NFS host configuration. You need to restart the NFS server.

```
    sudo systemctl restart nfs-kernel-server
```

![nfs][7]

---

# Creating Mount Points and Mounting Directories on the Client

1- Now time to use shared directory from NFS host. We need to prepare our client server/VM for using that directory.

2- In order to make the remote shares available on the client, we need to mount the directories on the host that we want to share to empty directories on the client.

3- We will create the directory for our mount , I will use same directory name which is **general**

```
    sudo mkdir -p /nfs/general
```

![nfs][8]

4- Now the enjoyable part. We can mount the directory with NFS remote/host directory.

```
    sudo mount host_ip:/var/nfs/general /nfs/general
```

5- My host ip is **192.168.64.20** so I will use

```
    sudo mount 192.168.64.20:/var/nfs/general /nfs/general
```

6- We can see mount points now with df -h
![nfs][9]

---

# Testing NFS Access

1- Let's test our NFS server. We can create some files and try to edit.

2- Let's create a file in client server/VM via sudo and check owner and group of the file.

```
    sudo touch /nfs/general/my-test.txt && sudo ls -lrth /nfs/general/my-test.txt
```

![nfs][10]

3- Because we mounted this volume without changing NFS’s default behavior and created the file as the client machine’s root user via the sudo command, ownership of the file defaults to nobody:nogroup. client superusers won’t be able to perform typical administrative actions, like changing the owner of a file or creating a new directory for a group of users, on this NFS-mounted share.

4- Let's add a line to the test file and check the same file from host server.

```
    # From client server
    sudo vi /nfs/general/my-test.txt

    #Add : Hi is line added from client.....
    #Save
```

![nfs][11]

5- And check file content from host server:

```
    sudo cat /var/nfs/general/my-test.txt
```

![nfs][12]

6- You can try same thing from host server and You will see changes from client side.

---

# Mounting the Remote NFS Directories at Boot

1- If You want to have NFS mount , You need to use automatically at boot by adding them to /etc/fstab file on the client.

2- Edit your **/etc/fstab** file and add your mount

```
    host_ip:/var/nfs/general    /nfs/general   nfs auto,nofail,noatime,nolock,intr,tcp,actimeo=1800 0 0
```

3- In my case :

```
    192.168.64.20:/var/nfs/general    /nfs/general   nfs auto,nofail,noatime,nolock,intr,tcp,actimeo=1800 0 0
```

![nfs][19]

---

---

---

> :+1: :+1: :+1: :+1: :+1: :+1: :+1: :+1:

---

Guneycan Sanli.

---

[1]: ../assets/images/ntfy/notfy1-1.jpg
[2]: ../assets/images/ntfy/notfy2.jpg
[3]: ../assets/images/ntfy/notfy3.jpg
[4]: ../assets/images/ntfy/notfy4.jpg
[5]: ../assets/images/nfs/nfs5.jpg
[6]: ../assets/images/nfs/nfs6.jpg
[7]: ../assets/images/nfs/nfs7.jpg
[8]: ../assets/images/nfs/nfs8.jpg

```

```
