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

-   1 server/VM (I will use Debain Buster VM).
-   Docker installed on the box.
-   Domain name.
-   Cloudflare account for tunnel from WAN to You local network via tunnel.
-   Dowloaded nfty app Your target device (Phone?).

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

3- Basiclly that is it. Server is setup and running. Let's check nfty WEB interface.

---

# Access nfty Web Interface

1- You can access your nfty via port forwarding.
2- I used port 80 so I will open a port to my VM or You can use your VM's LAN IP to access.

![nfty][3]

![nfty][4]

# Cloudflare Tunnel Setup

1- If you have a VM via public IP You can skip this part.

2- I have a VM in my LAN so I have no public ip assinged to my local VM.

3- I need to reach from public internet to my private network so , I will setup a **Claudflare** tunnel with using my personal domain. After You created Cloudflare account You need to add Website to your account which is should be your DNS (You can choose freeplan and cludflare will provide you Nameservers with that nameservers You need to edit your Domain's nameservers)

4- If You have not domain name You may buy a new domain name. I can recommned **porkbun** [porkbun](https://porkbun.com/) , It is a good provider for support and also renewal prices are cheapier than other providers.

5- If you have Domain name with We can follow-up our next steps, Create a **cloudflare** zero trust account (It is free).

6- You need to open Zero Trust section and create account for zero trust.

![nfty][5]

![nfty][6]

7- It will ask you chosing a plan You chosee free plan. To be honest I have not so much knowladge for other plans but We can use free plan and It is no cost. It will be enough for our project.

8- Once You are in Cloudflare zero trust dashboard. Click on the network menu option and click on tunnels.

![nfty][7]

9- We will create a tunnel to our home network.

10- Click on add tunnel and select Cloudflared option then name it.

![nfty][8]

11- We are ready for creating tunnel now!!!

12- Cloudfalre provide diffrent way to set up tunnel, We have already Docker installed on our VM so using Docker container would the best option.

13- Click on Docker and It will generate the command for you.

![nfty][9]

14- Go copy the docker command and run it on our on-prem machine/VM.

15- You may need to add -itd after docker run command for running container background.

![nfty][10]

16- You will see the tunnel is connected in Cludflare dashboard.

![nfty][11]

17- Okay tunnel is good, Now We need to use our Domain name with sub-domain to point the tunnel connector. Click on next.

18- Now We can add our domain with Internal IP of target VM/machine.

![nfty][12]

19- Okay , We should be good for test. But before test We need to setup target machine ntfy setup. I will use my phone to set up taking notification. We need to install **ntfy** app to the phone. You can serach in the market and intsall it.

![nfty][13]

20- After install **nfty** app (Do not forget allow notification from the app :D). We need to change default server.


21- Okay now We need to subscribe a topic and We can have notification from that topic. I have created **guney** topic and subscribe. nfty has a command line interface as you could see in WEB interface. So We have a topic and We have changed our deafult server which is our domain name and also It uses cloudflare tunnel to reach our private network.

![nfty][14]

![nfty][15]

22- Now We can test send notification. Use any terminal and send a curl request with message and toipc name.

```
    curl -v -d "Hey Guney test notification from VM" testntfy.guneycansanli.com/guney
```
![nfty][16]

23- Now If you check Your phone You can see notification also If you check your Web interface You can subscribe same topic in there and You can haev same notifications in there. 

![nfty][17]

![nfty][18]


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
[5]: ../assets/images/ntfy/notfy5.jpg
[6]: ../assets/images/ntfy/notfy6.jpg
[7]: ../assets/images/ntfy/notfy7.jpg
[8]: ../assets/images/ntfy/notfy8.jpg
[9]: ../assets/images/ntfy/notfy10.jpg
[10]: ../assets/images/ntfy/notfy11.jpg
[11]: ../assets/images/ntfy/notfy12.jpg
[12]: ../assets/images/ntfy/notfy13.jpg
[13]: ../assets/images/ntfy/notfy14.jpg
[14]: ../assets/images/ntfy/15.jpg
[15]: ../assets/images/ntfy/notfy15.jpg
[16]: ../assets/images/ntfy/notfy16.jpg
[17]: ../assets/images/ntfy/notfy17.jpg
[18]: ../assets/images/ntfy/notfy18.jpg

```

```
