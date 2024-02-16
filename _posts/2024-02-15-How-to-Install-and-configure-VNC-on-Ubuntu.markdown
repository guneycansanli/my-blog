---
title: "How to Install and Configure VNC on Ubuntu 20.04"
layout: post
date: 2024-02-13 14:20
image: ../assets/images/vnc/main.jpg
headerImage: true
tag:
    - linux
    - ubuntu
    - x11
category: blog
author: guneycansanli
description: How to Install and Configure VNC on Ubuntu 20.04 Tutorial
---

# What is VNC ?

-   VNC allows you connect to it securely through an SSH tunnel.

# Prerequisites

-   One Ubuntu 20.04 server with a non-root administrative user.
-   A local computer with a VNC client installed for MAC I use REAL VNC, VNC Viewer

# Install VNC and Desktop Environment

1. There are different desktop environment adn VNC servers, I will install packages for the latest Xfce desktop environment and the TightVNC package available from the official Ubuntu repository.

2. Update list of packages

```
    sudo apt update
```

3. Now install Xfce along with the xfce4-goodies package, which contains a few enhancements for the desktop environment

```
    sudo apt install xfce4 xfce4-goodies
```

![vnc][1]

4. During installation, you may be prompted to choose a default display manager for Xfce. A display manager is a program that allows you to select and log in to a desktop environment through a graphical interface. You’ll only be using Xfce when you connect with a VNC client, and in these Xfce sessions you’ll already be logged in as your non-root Ubuntu user. So for the purposes of this tutorial, your choice of display manager isn’t pertinent. Select either one and press ENTER.

5. Once It's done. We can install TigerVNC server.

```
    sudo apt install tightvncserver
```

6. Next, run the vncserver command to set a VNC access password, create the initial configuration files, and start a VNC server instance.

```
    vncserver
```

7. After You run vncserver. You nedd to create a password for accessingyour machine remotely.
8. You can see details after You cinfigured your password.  
   ![vnc][2]

9. You can use vncserver commands and check more details about your server configs. vncserber -list will show your vnc sessions and port informations. You will use RFB PORT for accessing remotely.

```
    vncserver -list
```

![vnc][3]

10. At this point, the VNC server is installed and running. Now let’s configure it to launch Xfce and give us access to the server through a graphical interface.

# Configuring the VNC Server

1. The VNC server needs to know which commands to execute when it starts up. Specifically, VNC needs to know which graphical desktop environment it should connect to.
2. The commands that the VNC server runs at startup are located in a configuration file called xstartup in the .vnc folder under your home directory. The startup script was created when you ran the vncserver command in the previous step, but you’ll create your own to launch the Xfce desktop.
3. Before start configuration first kill your latest session running on 5901 port.

```
    vncserver -kill :1
```

![vnc][4]

4. Before you modify the xstartup file, back up the original.

```
    mv ~/.vnc/xstartup ~/.vnc/xstartup.bak
```

5. Now create a new xstartup file and open it in a text editor, such as vim.

```
    vi ~/.vnc/xstartup
```

6. Then add the following lines to the file.

```
#!/bin/bash
xrdb $HOME/.Xresources
dbus-launch startxfce4
```

![vnc][5]

7. Make sure the file is executable.

```
chmod +x ~/.vnc/xstartup
```

8. Restart VNC server but this time use localhost flag. This binds the VNC server to your server’s loopback interface. This will cause VNC to only allow connections that originate from the server on which it’s installed. That mean is WE should open a port for connection. That is why localhost.

```
vncserver -localhost
```

![vnc][6]

# Connectingto VNC Desktop

1. Create an SSH connection on your local computer with open tunnel.
2. You can do this via the terminal on Linux or macOS with the following ssh command.

```
ssh -A -o ServerAliveInterval=60 -L  9901:localhost:5901 guney@192.168.64.20
```

3. You may use different SSH options or different local ports.
4. If you are succefully open tunnel to remote server.
   ![vnc][7]

5. Once the tunnel is running, use a VNC client to connect to localhost:9901 (My port). You’ll be prompted to authenticate using the password you set.
   ![vnc][8]

# Running VNC as a System Service

1. By setting up the VNC server to run as a systemd service you can start, stop, and restart it as needed like other services.
2. You can also use systemd’s management commands to ensure that VNC starts when your server boots up.
3. First, create a new unit file called /etc/systemd/system/vncserver@.service

```
sudo vi /etc/systemd/system/vncserver@.service
```

4. The @ symbol at the end of the name will let us pass in an argument you can use in the service configuration. You’ll use this to specify the VNC display port you want to use when you manage the service.

5. Add the following lines to the file. Be sure to change the value of User, Group, WorkingDirectory, and the username in the value of PIDFILE to match your username.

```
[Unit]
Description=Start TightVNC server at startup
After=syslog.target network.target

[Service]
Type=forking
User=guney
Group=guney
WorkingDirectory=/home/guney/

PIDFile=/home/guney/.vnc/%H:%i.pid
ExecStartPre=-/usr/bin/vncserver -kill :%i > /dev/null 2>&1
ExecStart=/usr/bin/vncserver -depth 24 -geometry 1280x800 -localhost :%i
ExecStop=/usr/bin/vncserver -kill :%i

[Install]
WantedBy=multi-user.target
```

![vnc][9]

6. Next, make the system aware of the new unit file.

```
sudo systemctl daemon-reload
```

7. Enable the unit file.

```
sudo systemctl enable vncserver@1.service
```

8. Stop the current instance of the VNC server if it’s still running, Then start it as you would start any other systemd service

```
vncserver -kill :1
sudo systemctl start vncserver@1
```

6. You can verify service status.

```
vncserver -kill :1
sudo systemctl status vncserver@1
```

![vnc][10]

7. Finally, You can test remote connection again.
   ![vnc][11]

---

---

---

> :blush: :star: :boom: :fire: :+1: :eyes: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/vnc/vnc-1.jpg
[2]: ../assets/images/vnc/vnc-2.jpg
[3]: ../assets/images/vnc/vnc-3.jpg
[4]: ../assets/images/vnc/vnc-4.jpg
[5]: ../assets/images/vnc/vnc-5.jpg
[6]: ../assets/images/vnc/vnc-6.jpg
[7]: ../assets/images/vnc/vnc-7.jpg
[8]: ../assets/images/vnc/vnc-8.jpg
[9]: ../assets/images/vnc/vnc-9.jpg
[10]: ../assets/images/vnc/vnc-10.jpg
[11]: ../assets/images/vnc/vnc-11.jpg

```

```
