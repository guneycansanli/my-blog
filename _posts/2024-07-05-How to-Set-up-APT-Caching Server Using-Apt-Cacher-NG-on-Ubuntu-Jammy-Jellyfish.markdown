---
title: "How to Set up APT-Caching Server Using Apt-Cacher NG on Ubuntu Jammy Jellyfish"
layout: post
date: 2024-07-05 14:20
image: ../assets/images/apt-cacher/main.jpg
headerImage: true
tag:
    - vm
    - proxmox
    - apt-cacher
category: blog
author: guneycansanli
description: How to Set up APT-Caching Server Using Apt-Cacher NG on Ubuntu Jammy Jellyfish
---

# Introduction

Apt-Cacher NG is a caching proxy server for Debian-based Linux distributions like Ubuntu, Debian, and Linux Mint. It locally caches packages from official repositories when you install them using the apt command. Subsequent installations of the same packages fetch them from the local cache, saving time and bandwidth.

Here's how to set up an APT-Caching server using Apt-Cacher NG on Ubuntu 22.04 (Jammy Jellyfish)

---

# Prerequisites

1- 2 Ubuntu server (Client and caching proxy server)

---

# My Setup

My Server Setup:

```
Apt Cache Server OS   : Ubuntu 22.04.4 LTS
Apt Cache IP Address  : 192.168.1.175
Apt Cache Hostname    : apt-cacher
Default Port	      : 3142
```

My Client Setup

```
Client OS             : Ubuntu 22.04.4 LTS
Client IP Address     : 192.168.1.176
Client Hostname       : ubuntu-jammy-server
```

---

# Step 1 – Install Apt-Cacher-NG

1- **Apt-Cacher-NG package** is included in the Ubuntu default repository. We can install via:

```
apt-get update -y
apt-get install apt-cacher-ng -y
```

2- After you can apt commands , System may wants to verify the new package installation and also may wants to restart some services. This is new in in ubuntu 22.04 to be up-to-date kernel and services. Since We focused apt-cacher I don not care other services or restarting any services. We can choose yes and press enter.

![apt-cacher][1]

![apt-cacher][2]

3- Once the Apt-Cacher-NG package is installed, start the Apt-Cacher-NG service and enable it to start at system reboot.

```
systemctl start apt-cacher-ng
systemctl enable apt-cacher-ng
```

![apt-cacher][3]

4- We can check the status of Apt-Cacher-NG and make sure service is running.

```
systemctl status apt-cacher-ng
```

![apt-cacher][5]

5- Apt-Cacher-NG listens on port 3142 as default. If your Firewall enable You need to add necessary rules, I do not use any FW so I will skip that part.

6- We can check listening port 3142

```
ss -altnp | grep apt
```

![apt-cacher][5]

7- After installation is completed, the apt-cacher-ng will start automatically. Now open and edit the cache-ng configuration file located under the ‘/etc/apt-cacher-ng‘ directory.

```
vim /etc/apt-cacher-ng/acng.conf
```

8- Next, we need to uncomment the following lines as suggested, if it is commented remove the ‘#‘ from the beginning. In this directory, all dpkg packages will be stored while installing or updating packages.

```
CacheDir: /var/cache/apt-cacher-ng
```

To enable the log we need to enable this line, By Default it will be enabled.

```
LogDir: /var/log/apt-cacher-ng
```

The apt-cacher will listen to port 3142, if you need to change the port, you can change the port.

```
Port:3142
```

![apt-cacher][6]

---

# Step 2 – Configure Apt-Cacher-NG HTTPS

1- By default, Apt-Cacher NG does not serve HTTPS repositories, so you will need to enable it. We can edit same file **/etc/apt-cacher-ng/acng.conf** and Uncomment the following line:

```
PassThroughPattern: .*
```

![apt-cacher][7]

2- Save and close the file and restart Apt-Cacher-NG service to apply the changes.

```
systemctl restart apt-cacher-ng
```

---

# Step 3 – Configure Client System to use Apt-Cacher NG

1- By default, all Ubuntu systems use the official Ubuntu repositories to download and install packages. To utilize Apt-Cacher NG for package installations, you need to configure your client systems accordingly.

2- In this case We create a new proxy configuration file on our **client server**

```
vi /etc/apt/apt.conf.d/00aptproxy
```

3- Add Add the following line to 00aptproxy file:

```
Acquire::http::Proxy "http://your-server-ip:3142";     ####Do not forget edit your-server-ip
```

![apt-cacher][8]

4- Save and close the file.

---

# Step 4 – Verify APT-Cacher NG

1- We are ready to test apt-cacher server and client now.

2- let’s try to install the Apache package on the Client system.

```
apt-get install apache2 -y
```

3- The above command will find, download, and install an Apache package from the Apt-Cache NG server.

4- We can verify that with checking the **apt-cacher.log** on apt-cacher server.

```
tail -f /var/log/apt-cacher-ng/apt-cacher.log
```

![apt-cacher][9]

5- Additionly apt-cacher-ng provides a web interface for all reports. We can access it via local port **http://your-server-ip:3142/acng-report.html**

![apt-cacher][10]

---

# Step 5 – Control Apt-Cacher NG Usage

1- We can also set up access control for Apt-Cache NG so that only authenticated hosts can download the package from the Apt-Cacher NG server.

2- We can use the /etc/hosts.allow and /etc/hosts.deny for controling access.

For example, to allow 192.168.1.175 to use Apt-Cacher NG server, edit the /etc/hosts.allow file:

```
vi /etc/hosts.allow
```

Add the following line:

```
apt-cacher-ng : 192.168.0.10 192.168.0.11
```

Save and close the file

![apt-cacher][11]

If We want to block the host 192.168.1.200 to use Apt-Cacher NG server, edit the /etc/hosts.deny file:

```
vi /etc/hosts.deny
```

Add the following line:

```
apt-cacher-ng : 192.168.1.100
```

Save and close and apt-cacher will deny that ips that you added.
![apt-cacher][12]

---

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
