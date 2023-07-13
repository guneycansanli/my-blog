---
title: "How To Install LXD on Debian 11 Buster Fast Guide"
layout: post
date: 2023-07-12 18:20
image: ../assets/images/lxd/main.jpg
headerImage: true
tag:
    - VM
    - linux
    - Debian
    - Virtualization
    - lxd
    - lxc
category: blog
author: guneycansanli
description: How To Install LXD on Debian 11 Buster Fast Guide
---

# Install LXD on Debian 10

1. Install updates on Debian box using the apt command.

{% highlight raw %}
$ sudo apt update
$ sudo apt upgrade
{% endhighlight %}

## Installing snap on Debian 10

1.  Snaps are a secure and scalable way to embed applications on Linux devices. A snap is an application containerised with all its dependencies. A snap can be installed using a single command on any device running Linux. With snaps, software updates are automatic and resilient.
2. Snaps are availables packages all distros
3. The app store for Linux -> https://snapcraft.io/

{% highlight raw %}
$ sudo apt install snapd
$ sudo snap install core
{% endhighlight %}

4. You can use snap command It should be under /snap/bin, You can add the path your $PATH or .bashrc 
5. snap is working like apt so you can download and install packages.

# Installing LXD snap on Debian 10

1. LCD and LXC are different but I am not going to explain it today, basiclly LXD engine or managament layer, lxc is worker side that allows You some container base processes (launch, stop, exec, attach...) 

{% highlight raw %}
sudo snap install lxd
{% endhighlight %}

2. The output should be displayed as **lxd 5.12-c63881f from Canonical✓ installed**

---

## Add user to the LXD group ?

{% highlight raw %}
sudo adduser {USER-Name-Here} lxd
{% endhighlight %}

## Configure LXD 

1. Using LXC requires initiliaze to LXD for running the firdt container.

{% highlight raw %}
$ sudo -i
$ lxd init
$ exit
$ lxc list
{% endhighlight %}

2. You can use default settings.
3. Press enter or use different configs your LXD

## Listing built-in LXD image for various Linux distros

{% highlight raw %}
 lxc image list images: | grep -i debian
 lxc image list images: | grep -i -E 'centos|ubuntu|opensuse'
 lxc image list images: alpine
{% endhighlight %}

Outputs from the 1. command:
![lxc][1]

- You can see list of images and use one of them.

# Creating your first Linux container

1. Image running or launching syntax should be below:

{% highlight raw %}
lxc launch images:{distro}/{version}/{arch} {container-name-here}
{% endhighlight %}

Example : **lxc launch -t t2.micro images:debian/11/amd64 aws-dev-t2-micro-debian**

2. After running command you can list ypur containers with **lxc list** command.

![lxc][2]

3. Here you can see container is running and below lxc list, container info.

4. We can send command to container with: 

{% highlight raw %}
lxc exec aws-dev-t2-micro-debian -- pwd
lxc exec aws-dev-t2-micro-debian -- ls -la
lxc exec aws-dev-t2-micro-debian -- top
lxc exec aws-dev-t2-micro-debian -- w
{% endhighlight %}

![lxc][3]

5. Some other commands

- lxc stop <container-name>
- lxc delete <container-name>
- lxc start <container-name>
- lxc delete <container-name>
- lxc info <container-name>
- lxc list --fast
- lxc list | grep RUNNING
- lxc exec containerName -- command
- lxc exec <container-name> {shell-name}
- lxc exec <container-name>  bash

- Desktop running (GUI):
 Make sure remote-viewer installed to see desktop console on Debian 10 desktop:

{% highlight raw %}
sudo apt install virt-viewer
{% endhighlight %}

- Here is how launce Arch Linux GNOME desktop using LXD with 4 vCPU and 4GiB RAM:

{% highlight raw %}
lxc launch images:archlinux/desktop-gnome archlinuxdesktop \
--vm \
-c security.secureboot=false \
-c limits.cpu=4 \
-c limits.memory=4GiB \
--console=vga
{% endhighlight %}

- lxc --help
- lxc {command} --help
- lxc list --help

---

> :bulb: Never stop learning, beacuse life never stops teaching.

> :memo: Thanks for reading.


Guneycan Sanli.

---

[1]: ../assets/images/lxd/lxc1.jpg
[2]: ../assets/images/lxd/lxc2.jpg
[3]: ../assets/images/lxd/lxc3.jpg
