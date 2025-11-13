---
title: "Ctop – A Powerful CLI Tool for Monitoring Docker Containers in Real Time"
layout: post
date: 2025-11-11 11:00
image: ../assets/images/ctop/main.jpg
headerImage: true
tag:
    - docker
    - ctop
    - monitoring
    - tui
category: blog
author: guneycansanli
description: Ctop – A Powerful CLI Tool for Monitoring Docker Containers in Real Time
---

## Introduction
When working with **Docker containers**, monitoring performance is just as important as running your applications. The command-line tool **ctop** provides a quick and easy way to visualize what’s happening inside your containers — similar to how the classic `top` command shows system processes.

With **ctop**, you can view real-time metrics for all running containers such as **CPU usage, memory consumption, network traffic, and disk I/O**, all in a single, clean terminal interface.

You can check the website for more details: 👉 [https://ctop.sh/](https://ctop.sh/)

----------

###  **What is ctop?**

**ctop** is an open-source, cross-platform monitoring tool written in Go. It works much like the familiar `top` or `htop` commands but focuses on containerized environments.

It supports **Docker** natively and also works with **runC**, with plans to add connectors for other container and orchestration systems like **Kubernetes** in future versions.

In short, ctop helps you:

-   View all running and stopped containers at a glance
    
-   Inspect resource usage for each container
    
-   Quickly identify performance bottlenecks
    
-   Drill down into single-container views for more details
    

----------

###  **How to Install ctop on Linux**

There are two main ways to install **ctop**:

#### **1. Install via Binary**

You can check github page for other installations. 

You can directly download and install the binary for your system:

```bash
sudo wget https://github.com/bcicen/ctop/releases/download/v0.7.7/ctop-0.7.7-linux-amd64 -O /usr/local/bin/ctop
sudo chmod +x /usr/local/bin/ctop
``` 

![ctop][1]

After that, run it with:

```bash
#Run with permitted user or with sudo or root
ctop
``` 

![ctop][2]

This will open a real-time dashboard of all your Docker containers.

----------

#### **2. Run ctop Using Docker**

If you prefer not to install it directly on your system, you can run it inside a container:

```bash
docker run --rm -ti --name=ctop \
  -v /var/run/docker.sock:/var/run/docker.sock \
  quay.io/vektorlab/ctop:latest
  ``` 

This approach keeps your host clean while still giving you full monitoring access.

----------

###  **Basic Usage Examples**

Once ctop is running, you’ll see a list of all containers (both active and stopped).  
You can use the **arrow keys** to navigate and **Enter** to inspect a specific container in detail.

Here are a few useful commands and options:

Command/Description

Launch the main interface:

```bash
ctop
```

Show only active (running) containers:

```bash
ctop -a
```

Filter containers with names containing whoami:

```bash
ctop -f whoami
```

![ctop][3]


Sort containers by memory usage:

```bash
ctop -s mem
```

![ctop][4]

Show help and available options:

```bash
ctop -h
```

Show help and available options

----------

### **Inspecting a Single Container**

If you highlight a container and press **Enter**, ctop opens a detailed view showing only that container.  
Here you can see:

-   Real-time CPU and memory usage
    
-   Network I/O statistics
    
-   Block I/O (disk operations)
    
-   Uptime and container state
    

For example, if you have a container running a web app called `myapp_web_1`, you can quickly inspect it:

```bash
ctop -f myapp_web_1
``` 

You can use the Up and Down arrow keys to highlight a container and click Enter to select it. You will see a menu as shown in the following screenshot. 

![ctop][5]

Single view shows more details for container

![ctop][6]

----------

###  **Conclusion**

**ctop** is a simple but powerful CLI utility that provides a “top-like” view for Docker containers.  
It’s lightweight, requires no setup, and gives you instant insight into container performance metrics.

Whether you’re managing a few containers or dozens of microservices, **ctop** is a must-have tool for every Docker user.

For more details, check out the official GitHub repository:  
👉 [https://github.com/bcicen/ctop](https://github.com/bcicen/ctop)

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/ctop/ctop-1.jpg
[2]: ../assets/images/ctop/ctop-2.jpg
[3]: ../assets/images/ctop/ctop-3.jpg
[4]: ../assets/images/ctop/ctop-4.jpg
[5]: ../assets/images/ctop/ctop-5.jpg
[6]: ../assets/images/ctop/ctop-6.jpg





