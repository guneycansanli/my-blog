---
title: "Docker Intallation on Ubuntu"
layout: post
date: 2022-04-27 23:19
image: /assets/images/docker-ubuntu.png
headerImage: true
tag:
- docker
- ubuntu
- devOps
category: blog
author: guneycansanli
description: What is the steps of intalling docker on ubuntu?
---

## Introduction

[Docker][1] is an application that is using with containers. With the containers you can create one or more environment than you can run your applications in it. They are similar to virtual machines. Docker has so many feature like volume mapping, docker host, docker network, images and more.

With this article I will try to explain Docker installation on [Ubuntu 20.04.3][2] on **CLI** (Command Line Interface) let's say *terminal* level. Why I choose **CLI** level because If you will work with a server or minimal operation system which has not GUI you must work with commands and CLI. Other advantage is learn that what is going infrastructure.

Before start If you use Windows you should create a VM with  [Virtual Box][3] or [VMware][4] and than installing Ubuntu.

There are installation methods for Docker, In my opinion follow the **Install using the repository** installation method is the easiest and most understandable method. If you want to learn other methods you can check the [Docker Docks for ubuntu][5].

## Install using the repository

Before you install Docker Engine for the first time on a new host machine, you need to set up the Docker repository. Afterward, you can install and update Docker from the repository.

# Set up the repository

1-) Update the **apt** (Advanced Package Tool) and install to required packages.

{% highlight html %}
 $ sudo apt-get update
 $ sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
{% endhighlight %}

2-) Add Docker’s official GPG key:

{% highlight html %}
 $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
{% endhighlight %}

{% highlight raw %}
![docker install-1][/image/url]
{% endhighlight %}

![docker install-1][6]

---

[1]: https://docker.com
[2]: http://releases.ubuntu.com/20.04/
[3]: https://www.virtualbox.org/
[4]: https://www.vmware.com/
[5]: https://docs.docker.com/engine/install/ubuntu/
[6]: ../assets/images/docker-install/docker-install-1.PNG


