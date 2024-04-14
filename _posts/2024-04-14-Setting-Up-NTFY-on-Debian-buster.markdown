---
title: "Send Push Notifications to Your Phone or Desktop via NTFY (Notify) [NOT COmpleted]"
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

```

```
