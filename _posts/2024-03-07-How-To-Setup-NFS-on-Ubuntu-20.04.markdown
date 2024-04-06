---
title: "How To Setup NFS Server and Client on Ubuntu 20.04"
layout: post
date: 2024-04-04 14:20
image: ../assets/images/nfs/main2.jpg
headerImage: true
tag:
    - linux
    - ubuntu
    - nfs
category: blog
author: guneycansanli
description: How To Setup NFS Server and Client on Ubuntu 20.04
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

3- After above installation finish. We are ready to setup sharing directories on the host server/VM.

---

# Creating the Share Directories on the Host

1- Okay, We are ready to setup directories for sharing to clients but there are different settings for it. I will try root user example today.

2- NFS server does not allow that superusers on the client cannot write files as root. So basiclly client have a mount from NFS so even client's root user can not write files by default. The solution is using nobody user and nogroup for shared files/directories. Superusers can control everything on their own system. But when it comes to NFS, the shared directories aren't part of the client's system, so by default, the server stops superusers from doing certain things like writing files as root or changing ownership. Sometimes, trusted users on the client side need to do these tasks on the shared files, even though they don't need superuser access on the server. You can adjust the NFS settings to allow this, but it comes with a risk because those users could potentially gain control over the whole server system.

3- Let's figure it out with one example uses after We learnt some informations and risks.

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

[1]: ../assets/images/nfs/nfs1.jpg
[2]: ../assets/images/nfs/nfs2.jpg
[3]: ../assets/images/nfs/nfs3.jpg
[4]: ../assets/images/nfs/nfs4.jpg
[5]: ../assets/images/nfs/nfs5.jpg
[6]: ../assets/images/nfs/nfs6.jpg
[7]: ../assets/images/nfs/nfs7.jpg
[8]: ../assets/images/nfs/nfs8.jpg
[9]: ../assets/images/nfs/nfs9.jpg
[10]: ../assets/images/nfs/nfs10.jpg
[11]: ../assets/images/nfs/nfs11.jpg
[12]: ../assets/images/nfs/nfs12.jpg
[19]: ../assets/images/nfs/nfs19.jpg

```

```
