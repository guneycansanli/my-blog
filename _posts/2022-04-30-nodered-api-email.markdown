---
title: "Node-RED HTTP Request (REST API) and E-mail Sending"
layout: post
date: 2022-04-30 13:39
image: ../assets/images/nodered/nodered.png
headerImage: true
tag:
- nodered
- api
- email
category: blog
author: guneycansanli
description: How can we do api call with node-red and using smtp?
---

# Introduction

[Node-RED][1] is developed by IBM and It is a platform which is no need many coding. It based NodeJS so If you know JS you probably will be able to understand much more easily. You can develop IOT projects with Node-RED. It is also open source.

---

## Node-RED Installing

You can install **Node-RED** to you local or in Docker. You can the official documentation and follow the steps. I will focus to API and E-mail sending today.

## Quick Review

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

![docker install-1][6]

![docker install-2][7]

3-) Use the following command to set up the stable repository. To add the nightly or test repository, add the word nightly or test (or both) after the word stable in the commands below.

{% highlight html %}
 $ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
{% endhighlight %}

# Set up the repository

---

## Install Docker Engine

1-) Update the apt package index, and install the latest version of Docker Engine, containerd, and Docker Compose, or go to the next step to install a specific version:

{% highlight html %}
 $ sudo apt-get update
 $ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
{% endhighlight %}

2-) To install a specific version of Docker Engine, list the available versions in the repo, then select and install:

a. List the versions available in your repo:

{% highlight html %}
 $ apt-cache madison docker-ce
{% endhighlight %}

{% highlight raw %}
![docker install-4][8]
{% endhighlight %}

![docker install-4][8]

As you see list of avaible versions for you repo and next step is installing the spesific versions just select the name of version from second coloumn it should be like that **5:18.09.1~3-0~ubuntu-xenial**

{% highlight html %}
 $ sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io docker-compose-plugin
{% endhighlight %}

3-) Verify that Docker Engine is installed correctly by running the hello-world image.

{% highlight html %}
 $  sudo docker run hello-worlddocker-compose-plugin
{% endhighlight %}

that is all you installed Docker on your Ubuntu so time to enjoy Docker :)

[1]: https://nodered.org/
[2]: http://releases.ubuntu.com/20.04/
[3]: https://www.virtualbox.org/
[4]: https://www.vmware.com/
[5]: https://docs.docker.com/engine/install/ubuntu/
[6]: ../assets/images/docker-install/docker-install-1.PNG
[7]: ../assets/images/docker-install/docker-install2.PNG
[8]: ../assets/images/docker-install/docker-install4.PNG


