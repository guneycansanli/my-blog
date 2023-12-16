---
title: "How to Parallel Docker image builds with Packer"
layout: post
date: 2023-12-14 14:20
image: ../assets/images/packer/main.png
headerImage: true
tag:
    - linux
    - docker
    - packer
category: blog
author: guneycansanli
description: How to Parallel Docker image builds with Packer
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
        login_password = "**********"
        only = ["docker.ubuntu-bionic"]
    }
  }
}
{% endhighlight %}
![packer][2]

- Packer Block
- The packer {} block contains Packer settings, including specifying a required Packer version.
- In addition, you will find required_plugins block in the Packer block, which specifies all the plugins required by the template to build your image. Even though Packer is packaged into a single binary, it depends on plugins for much of its functionality. Some of these plugins, like the Docker Builder which you will to use, are built, maintained, and distributed by HashiCorp — but anyone can write and use plugins.
- Each plugin block contains a version and source attribute. Packer will use these attributes to download the appropriate plugin(s)..
- The source block configures a specific builder plugin, which is then invoked by a build block.
- The build block defines what Packer should do with the Docker container after it launches.
- Initialize Packer configuration, Initialize your Packer configuration.
- Parellel build is a very useful and important feature of Packer. For example, Packer can build an Amazon AMI and a VMware virtual machine in parallel provisioned with the same scripts, resulting in near-identical images.
{% highlight raw %}
packer init .
{% endhighlight %}
![packer][3]



- Build images 
{% highlight raw %}
 $  packer build .
{% endhighlight %}
![packer][4]


- After build images succesfully We can verify that We have images
- We used parallel images that means 2 different image with different tags and also We just push the only 1 image to repository
![packer][5]

- If We check our Dockerhub repository We can verify that We have only 1 image.
![packer][6]

---
---

> :metal: :metal: :metal: :metal: :metal: :metal: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/packer/packer1.jpg
[2]: ../assets/images/packer/packer2.jpg
[3]: ../assets/images/packer/packer3.jpg
[4]: ../assets/images/packer/packer5.jpg
[5]: ../assets/images/packer/packer6.jpg
[6]: ../assets/images/packer/packer7.jpg
