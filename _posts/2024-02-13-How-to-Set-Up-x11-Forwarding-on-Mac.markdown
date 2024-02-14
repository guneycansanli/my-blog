---
title: "How to Set Up x11 Forwarding on your Mac"
layout: post
date: 2024-02-13 14:20
image: ../assets/images/x11/x11.main.jpg
headerImage: true
tag:
    - linux
    - ubuntu
    - x11
category: blog
author: guneycansanli
description: How to Set Up x11 Forwarding on your Mac
---

# What x11 Forwarding ?

-   X11 forwarding is a mechanism that allows a user to start up remote applications, and then forward the application display to their local Windows machine
-   In this tutorial I'm going to show you how to set up ssh with x11 forwarding enabled on a mac and the reason you would want to do this is to display remote windows locally on your local machine so for example if you're working with a headless operating system like ubuntu server.

# Prerequisites

-   You need to have a Ubuntu server with GUI.
-   You need to have OpenSSH server installed on the server.
-   Make sure X11Forwarding option set to yes in /etc/ssh/sshd_config (Default is yes)

# Install GEDIT

-   There's a program called g edit it's a graphical text editor so if you want to install that on your computer you can do your remote server and to ubuntu in this case install degit to your server.
{% highlight raw %}
  apt install gedit
{% endhighlight %}

-   After installation done, Let's try to use gedit as I mentioned gedit is graphical editor so You need to have a window for it.
-   Try edit txt file with your SSH session.
{% highlight raw %}
  gedit text.txt
{% endhighlight %}
![x11][1]

-   I have above error because I did not set up any x11 forwarding to my OpenSSH server.
-   I get this error message saying cannot open display so let's go ahead and set that up the first thing we want to do is download x-quartz is the application that's going to sit there and listen for the x11 request.

# Download xquartz

-   We need to download and install xquartz to MAC.
-   You can find xquartz [here](https://www.xquartz.org/)
![x11][2]

-   Now let's go ahead and set up an ssh session with x11 forwarding enabled and the way we can do that is very simple this was our command before the only thing we're going to add to it is a -X Which will be enable x11 forwarding for our SSH session.

# Open SSH Session With x11 Forwading (-X)

-   We need to regular SSH connection with -X option.
{% highlight raw %}
  ssh -A -X -o ServerAliveInterval=60 guney@<IP-OF-SERVER>
{% endhighlight %}
![x11][3]

-   After I open my SSH session with x11 forwarding, My mac ran Xqartz itself
![x11][4]

-   Xquartz has opened here it's going to sit in the background and wait for a an x11 request so let's try it again let's try to do
    gedit test.txt and it'll take a little bit like, It's not the smoothest thing but eventually a window will appear and that window will be gedit so there this is a graphical user interface being served from our remote server.
{% highlight raw %}
  gedit test.txt
{% endhighlight %}

-   Here We have it. We got a graphical gedit and We can use it. Let's add some lines.
![x11][5]

-   Save and exit. You may see the logs in the terminal.
![x11][6]

-   You can read(cat) yout test.txt file and You can see the edited txt file.
![x11][7]

# More Applications With X11

-   There are a couple other applications or reasons that you want to do this besides a text editor one fun one is the x11 apps package so We can install it with.
{% highlight raw %}
  apt install x11-apps
{% endhighlight %}

-   Let's test xclock after installation done.
{% highlight raw %}
  xclock
{% endhighlight %}
![x11][8]

---

---

> :blush: :star: :boom: :fire: :+1: :eyes: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/x11/x11-1.jpg
[2]: ../assets/images/x11/x11-2.jpg
[3]: ../assets/images/x11/x11-3.jpg
[4]: ../assets/images/x11/x11-4.jpg
[5]: ../assets/images/x11/x11-5.jpg
[6]: ../assets/images/x11/x11-6.jpg
[7]: ../assets/images/x11/x11-7.jpg
[8]: ../assets/images/x11/x11-8.jpg
