---
title: "How to use autofs to mount NFS shares"
layout: post
date: 2024-04-20 14:20
image: ../assets/images/nfs-autofs/main.jpg
headerImage: true
tag:
    - linux
    - ubuntu
    - nfs
    - mount
category: blog
author: guneycansanli
description: How to use autofs to mount NFS shares
---

# Introduction

NFS, or Network File System, offers a convenient method to link remote drives to a local Linux system over a network, essentially making them appear as if they're part of the local storage. With a speedy 10 gigabit Ethernet connection and remote storage like SSDs or NVMe drives, the performance can rival that of local storage, making it an ideal solution for centralized storage accessible by multiple Linux machines.

However, there's a small inconvenience when it comes to automatically mounting these remote shares using the /etc/fstab file. When the server is online, everything works smoothly. But if there's a network issue or the server isn't available, Linux can take longer to boot as it waits for timeouts while trying to mount remote shares that may not exist at the moment.

To address this issue, we can turn to a tool called autofs. It offers a solution where remote shares are only mounted when they're actually accessed, rather than during the boot process. This helps streamline the boot time of Linux clients, as they don't have to wait for potentially unavailable remote shares during startup.

---

# Installing autofs

I have already one NFS server and client. I have another guide gor setting up NFS.

1- Install autofs on the client, not the server.

```
sudo apt install autofs
```

2- Thst is it after installation completed You can verify that the autofs files have been placed in the etc directory:

```
sudo apt install autofs
```

![autofs][1]

# Configure autofs

There are different type of using autofs and each file have different purposes usage. autofs has its own man page for more details about operation.

auto.master is the main file autofs will check in order to mount an NFS share. Optionally, we can use custom files in auto.master.d, but this is not necessary and requires extra setup. We can use auto.master for direct mapping. It should point to
/etc/auto.misc for direct mapping.

An auto.master entry consists of three parts:

mount_point-map_fil-options-

1- Edit auto.master for direct mapping

2- Add **/- /etc/auto.misc** and save the file.

```
sudo vi /etc/auto.master
```

![autofs][2]

3- Edit auto.misc

```
sudo vi /etc/auto.misc
```

4- Add your auto.misc entry (Similar to fstab) , I have used **/nfs/general -fstype=nfs4,rw 192.168.64.20:/var/nfs/general** in fstab but I do not need to use it in fstab anymore.

**/nfs/myshare -fstype=nfs4,rw < NFS Server IP >:/media/myshare**

![autofs][3]
The exact paths and address will depend upon your network and NFS server.

5- Remove or comment out NFS mount in fstab.

```
sudo vi /etc/fstab
```

![autofs][4]

6- Restart autofs to apply the changes

```
sudo service autofs restart
sudo service autofs status
```

![autofs][5]

# Why We Should not use fstab?

/etc/fstab is reliable for static storage setups but can cause boot delays if a drive is unavailable. To tackle this, autofs dynamically mounts remote shares when needed, avoiding boot delays associated with /etc/fstab. If the share exists, use it. If not, skip it, and move on. There is no added boot delay since /etc/fstab is not needed when mounting the NFS share.

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

[1]: ../assets/images/nfs-autofs/autofs1.jpg
[2]: ../assets/images/nfs-autofs/autofs2.jpg
[3]: ../assets/images/nfs-autofs/autofs3.jpg
[4]: ../assets/images/nfs-autofs/autofs4.jpg
[5]: ../assets/images/nfs-autofs/autofs5.jpg
[6]: ../assets/images/nfs-autofs/autofs6.jpg
[7]: ../assets/images/nfs-autofs/autofs7.jpg
