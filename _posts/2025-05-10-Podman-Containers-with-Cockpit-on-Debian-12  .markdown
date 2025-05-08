---
title: "Managing Your Podman Containers with Cockpit on Debian 12"
layout: post
date: 2025-05-04 11:00
image: ../assets/images/docker-multi-stage/main.jpg
headerImage: true
tag:
    - docker
    - image
category: blog
author: guneycansanli
description: Managing Your Podman Containers with Cockpit on Debian 12  
---

# Managing Your Podman Containers with Cockpit on Debian 12  
**Written by:** Tom Donohue  
**Last Updated:** _20 December 2024_

If you’re someone who spends a lot of time in the terminal managing your containers, that’s great! But if you need a break from constant typing and prefer a **UI-based** approach, I’ve got something that might interest you. Enter **Cockpit**, a fantastic tool that lets you manage your containers from a sleek web interface. 🖥️

So let’s take a closer look at how to **monitor and manage Podman containers** using Cockpit on **Debian 12**. 💻

---

## What You’ll Learn in This Guide

- **Installing Cockpit** and the Podman component on Debian 12  
- **Launching the Podman API** to interact with Cockpit  
- **Monitoring your Podman containers** in the Cockpit UI  
- Searching for container images from your browser  
- Next steps after setting up your containers

---

## Installing Podman on Debian 12

Before diving into Cockpit, let’s make sure **Podman** is installed on your **Debian 12** system.

### 1. Update your system:

```bash
sudo apt update
```

### 2. Install Podman:

```bash
sudo apt install -y podman
```

### 3. Verify the installation:

```bash
podman --version
```

This will show you the version of Podman installed, confirming the installation was successful.

---

## Installing and Starting Cockpit on Debian 12

Once you’ve got **Podman** installed, the next step is to get **Cockpit** set up for managing your containers via a web interface.

### 1. Install Cockpit:

```bash
sudo apt install -y cockpit
```

### 2. Install the Cockpit Podman component:

```bash
sudo apt install -y cockpit-podman
```

### 3. Enable and start the Cockpit service:

To ensure Cockpit runs when your system boots, enable the cockpit socket:

```bash
sudo systemctl enable --now cockpit.socket
```

### 4. Verify Cockpit is running:

Check that Cockpit is actively running with the following command:

```bash
systemctl status cockpit
```

If everything is working, you should see something like this:

```bash
● cockpit.service - Cockpit Web Service
     Loaded: loaded (/lib/systemd/system/cockpit.service; enabled; vendor preset: enabled)
     Active: active (running) since [date]; [time] ago
```

---

## Start Podman’s API for Cockpit Interaction

Now, for Cockpit to interact with Podman, you need to start the **Podman API**. This will allow Cockpit to see the containers you’re running.

1. **Start the Podman API** service:

```bash
systemctl --user start podman
```

2. **Check the Podman API service status:**

```bash
systemctl --user status podman
```

You should see output indicating the service is active and running:

```bash
● podman.service - Podman API Service
     Loaded: loaded (/usr/lib/systemd/user/podman.service; static)
     Active: active (running) since [date]; [time] ago
     Main PID: [PID] (podman)
```

---

## Monitoring Your Podman Containers in Cockpit

Now that you have everything set up, you can start managing and monitoring your containers via Cockpit.

### 1. Log into Cockpit

Visit the following URL in your web browser:

```plaintext
http://localhost:9090
```

You’ll be prompted to log in using your **Debian 12** credentials (username and password).

---

### 2. Access the Podman Containers View

Once you’ve logged in, you’ll be directed to the Cockpit dashboard. From the left-hand menu, click on **Podman containers** to access the view where you can monitor your containers.

Here, you can:

- View all running containers and images
- Click on containers to expand the view for more information
- Monitor CPU and memory usage in real time
- Access logs and get a console to interact with the container directly

---

## Get a Console to Run Commands in Containers

One of the coolest features of Cockpit is the ability to interact with containers directly via a console in your web browser. This means you can:

- Run commands in the container as if you were using `podman exec` in the terminal
- Debug your containers or make adjustments without leaving the web interface

### To access the console:

1. Go to the **Console** tab for a specific container.
2. You’ll be presented with a terminal interface directly within the web browser.

---

##  Searching for and Pulling Container Images

Cockpit also allows you to search for container images directly from public registries.

### Steps:

1. Navigate to the **Images** section.
2. Click on **Get New Image**.
3. Use the search form to find the image you want from the registry, then select it.

Once you’ve selected the image, you can pull it directly into your Podman environment.

---

With **Podman** and **Cockpit** working together, you now have a powerful tool to manage your containers in a graphical interface.

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/docker-multi-stage/multi-1.jpg
[2]: ../assets/images/docker-multi-stage/multi-2.jpg
[3]: ../assets/images/docker-multi-stage/multi-3.jpg

