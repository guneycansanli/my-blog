---
title: "Shell In A Box – Web-Based SSH Terminal for Linux Access"
layout: post
date: 2025-02-27 12:20
image: ../assets/images/siab/main.jpg
headerImage: true
tag:
    - linux
    - shell
category: blog
author: guneycansanli
description: Shell In A Box – Web-Based SSH Terminal for Linux Access (shellinabox)
---

# Shell In A Box – Web-Based SSH Terminal for Linux Access

Shell In A Box (or shellinabox) is a web-based terminal emulator developed by Markus Gutschke. It features a built-in web server that allows users to access and control their Linux server via SSH using a browser with AJAX, JavaScript, and CSS support—without requiring additional plugins like FireSSH.

This guide covers installing and configuring Shellinabox for secure SSH access through a web browser, especially useful when access is limited to HTTPS traffic due to firewall restrictions.

---

## Installing Shellinabox

Shellinabox is available in the default repositories of Debian-based Linux distributions and can be installed with the following command:

### Install on Debian, Ubuntu & Mint:
```bash
$ sudo apt install openssl shellinabox
```

![siab][1]


## Configuring Shellinabox

By default, Shellinabox runs on TCP port `4200` and listens on `localhost`. To enhance security, you can change the default port to a random one, such as `6161`.
A self-signed SSL certificate is automatically created in `/var/lib/shellinabox` to facilitate HTTPS connections.

Modify the configuration file:
```bash
$ sudo vi /etc/default/shellinabox
```

Update the following lines:

```bash
# Start shellinaboxd automatically
SHELLINABOX_DAEMON_START=1

# Set Shellinabox to listen on port 6175
SHELLINABOX_PORT=6161

# Disable beep sounds
SHELLINABOX_ARGS="--no-beep"

# Define SSH server IP
OPTS="-s /:SSH:192.168.1.175"

# Restrict access to localhost if needed
OPTS="-s /:SSH:192.168.1.175 --localhost-only"
```

![siab][2]


After editing, restart and verify the service:
```bash
$ sudo systemctl restart shellinabox
$ sudo systemctl status shellinabox
```

![siab][3]

To check if Shellinabox is running on the specified port:
```bash
$ sudo netstat -nap | grep shellinabox
```

## Firewall Configuration (If You have FW)

Ensure that the firewall allows traffic through the chosen port (`6175`).

### On Debian, Ubuntu & Mint:
```bash
$ sudo ufw allow 6175/tcp
$ sudo ufw allow from 192.168.1.175 to any port 6161
```

## Accessing Linux SSH Terminal via Browser

Open your web browser and navigate to:
```bash
https://Your-IP-Address:6161
```
Log in with your Linux username and password to access the SSH terminal.

![siab][4]

## Additional Features

Right-click within the terminal to customize the interface and access additional options.

![siab][5]

For more details, visit the official [Shellinabox GitHub repository](https://github.com/shellinabox/shellinabox).

---

* * *

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

[1]: ../assets/images/siab/siab-1.jpg
[2]: ../assets/images/systemdtimer/timer2.jpg
[3]: ../assets/images/systemdtimer/timer3.jpg
[4]: ../assets/images/systemdtimer/timer4.jpg
[5]: ../assets/images/systemdtimer/timer5.jpg
[6]: ../assets/images/systemdtimer/timer6.jpg
[7]: ../assets/images/systemdtimer/timer7.jpg
[8]: ../assets/images/systemdtimer/timer-8.jpg



