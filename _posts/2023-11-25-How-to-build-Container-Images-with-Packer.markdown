---
title: "How to build Container Images with Packer"
layout: post
date: 2023-11-25 14:20
image: ../assets/images/packer/main.png
headerImage: true
tag:
    - linux
    - docker
    - packer
category: blog
author: guneycansanli
description: How to Parallel builds Docker Container Images with Packer
---

# What is Packer?

- Packer is a tool that lets you create identical machine images for multiple platforms from a single source template. Packer can create golden images to use in image pipelines.
- Packer is kind of IaC tool, You can build images and automate them for your different environment. You can build different type of images like AWS AMIs, Docker images, VM ware images and more. You can use a template like hcl, json or Yaml. Packer includes some features and plugins for manipulate for images. You can create base images which is include softwares and additional packages. Packer is an open source tool for creating identical machine images for multiple platforms from a single source configuration. Packer is lightweight, runs on every major operating system, and is highly performant, creating machine images for multiple platforms in parallel.

# Install Packer to Debian

- HashiCorp officially maintains and signs packages for the following Linux distributions.
- Add the HashiCorp GPG key.
{% highlight raw %}
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
{% endhighlight %}

- Add the official HashiCorp Linux repository.
{% highlight raw %}
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
{% endhighlight %}

- Update and install.
{% highlight raw %}
sudo apt-get update && sudo apt-get install packer
{% endhighlight %}
![packer][1]

- Verifying the Installation
{% highlight raw %}
 $ packer
 Usage: packer [--version] [--help] <command> [<args>]
{% endhighlight %}

# Build an Image and Push the image to Docker Hub

- With Packer installed, it is time to build your first image. For speed, simplicity's sake, and because it's free to download, you will build a Docker container. You can download Docker from https://docs.docker.com/get-docker/ if you don't have it installed.
- Write Packer template, A Packer template is a configuration file that defines the image you want to build and how to build it. Packer templates use the Hashicorp Configuration Language (HCL).
- Create a file docker-ubuntu.pkr.hcl
- Add following HCL block to it and save the file.
{% highlight raw %}
packer {
  required_plugins {
    docker = {
      version = ">= 0.0.7"
      source  = "github.com/hashicorp/docker"
    }
  }
}

variable "docker_image" {
  type    = string
  default = "ubuntu:xenial"
}

source "docker" "ubuntu" {
  image  = var.docker_image
  commit = true
}

source "docker" "ubuntu-bionic" {
  image  = "ubuntu:bionic"
  commit = true
}

build {
  name    = "learn-packer"
  sources = [
    "source.docker.ubuntu",
    "source.docker.ubuntu-bionic",
  ]

  provisioner "shell" {
    environment_vars = [
      "FOO=hello world",
    ]
    inline = [
      "echo Adding file to Docker Container",
      "echo \"FOO is $FOO\" > example.txt",
    ]
  }

  provisioner "shell" {
    inline = ["echo Running ${var.docker_image} Docker image."]
  }

  post-processors {
    post-processor "docker-tag" {
        repository =  "ubuntu-xenial"
        only = ["docker.ubuntu"]
        tags = ["ubuntu-xenial"]
      }
  }
  post-processors {
    post-processor "docker-tag" {
        repository =  "gnyscnsnli/packer"
        only = ["docker.ubuntu-bionic"]
        tags = ["ubuntu-bionic"]
      }
    post-processor "docker-push" {
        login = true
        login_username = "gnyscnsnli"
        login_password = "Cemal61ilayda*."
        only = ["docker.ubuntu-bionic"]
    }
  }
}
{% endhighlight %}

- Packer Block
- The packer {} block contains Packer settings, including specifying a required Packer version.
- In addition, you will find required_plugins block in the Packer block, which specifies all the plugins required by the template to build your image. Even though Packer is packaged into a single binary, it depends on plugins for much of its functionality. Some of these plugins, like the Docker Builder which you will to use, are built, maintained, and distributed by HashiCorp — but anyone can write and use plugins.
- Each plugin block contains a version and source attribute. Packer will use these attributes to download the appropriate plugin(s)..
- The source block configures a specific builder plugin, which is then invoked by a build block.
- The build block defines what Packer should do with the Docker container after it launches.
- Initialize Packer configuration, Initialize your Packer configuration 
- You can
{% highlight raw %}
packer init .
{% endhighlight %}
![packer][3]



- Build images
{% highlight raw %}
 $  packer build .
{% endhighlight %}



---
---

> :metal: :metal: :metal: :metal: :metal: :metal: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/packer/packer1.jpg
[2]: ../assets/images/packer/packer2.jpg
[3]: ../assets/images/packer/packer3.jpg
[4]: ../assets/images/packer/
[5]: ../assets/images/packer/
[6]: ../assets/images/packer/
[7]: ../assets/images/packer/
[8]: ../assets/images/packer/
[9]: ../assets/images/packer/
[10]: ../assets/images/packer/
[11]: ../assets/images/packer/
[12]: ../assets/images/packer/