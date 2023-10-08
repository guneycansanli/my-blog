---
title: "How to Set up Chrony as NTP Server and Client"
layout: post
date: 2023-10-07 14:20
image: ../assets/images/chrony/chronyMain.jpeg
headerImage: true
tag:
    - linux
    - chrony
    - time
category: blog
author: guneycansanli
description: How to Set up Chrony as NTP Server and Client
---

# What is Chrony?

-  Chrony is a solution for time synchronization before it, We could know about legacy NTP ,Network Time Protocol, is one of the oldest and most established time synchronization protocols. It is highly accurate and can achieve sub-millisecond synchronization in a well-configured network. NTP relies on a hierarchical model of time servers, with a primary reference source (stratum 0) such as a GPS clock or atomic clock.
-  Chrony is a more modern time synchronization solution that was designed to improve upon NTP's accuracy and reliability. It utilizes a different algorithm that can quickly synchronize a system's clock, even if it has been offline or has experienced significant time drift. Chrony can achieve better accuracy than NTP in certain scenarios.
-  Chrony is better for some of ways like If your system not active 7/24 or If you use lower power mode.
-  Better response to rapid changes in the clock frequency, which is useful for virtual machines that have unstable.

# Chrony Server and client

-  We will configure chrony server and client advantage of using server and client is We can have same time for our network so When we want to trouble shoot any error It would help us for RCA or any log analysis.

# Install chrony and start service

-   If you don't have chrony you can install with your packer manager (apt, apt-get, yum. dnf ...)
{% highlight raw %}
apt install chrony -y
{% endhighlight %}

-   After Chrony is installed, start and enable the Chronyd service using the systemctl command below.
{% highlight raw %}
systemctl enable chrony
systemctl start chrony
{% endhighlight %}

# Configure chrony server

-   In this step I will cnfigure node1 as a NTP server and I will use open NTP server which is closest to me.
-   You can use ntp pool servers.
-   I will use option 'iburst' that allows the Chronyd service to make the first update of the clock shortly after the start.
-   Another important option is 'allow' that is for our clinets can use the NTP server as reference.
-   you need to edit chrony.conf file.

{% highlight raw %}
nano /etc/chrony.conf
{% endhighlight %}

{% highlight raw %}

# list servers

server 0.north-america.pool.ntp.org iburst
server 1.north-america.pool.ntp.org iburst
server 2.north-america.pool.ntp.org iburst
server 3.north-america.pool.ntp.org iburst

#Allowed clients
allow 192.168.64.0/24
{% endhighlight %}

![chrony1][1]

-   Now, let's run the following chronyc command below to verify the sources of the NTP server pool that is currently used. You should see the list of current NTP server sources that are used by your server.
{% highlight raw %}
chronyc sources
{% endhighlight %}
-   You can also get detailed information via the '-v' option as verbose.
{% highlight raw %}
chronyc sources -v
{% endhighlight %}
![chrony2][2]

---

# Setting up Chrony as NTP Client

-   We will configure chrony client, Intallation process is same as chrony server, only the difference will be configuration.
-   Install chrony to client
{% highlight raw %}
apt install chrony -y
{% endhighlight %}

-   After Chrony is installed, start and enable the Chronyd service using the systemctl command below.
{% highlight raw %}
systemctl enable chrony
systemctl start chrony
{% endhighlight %}

-   We are ready to edit chrony config file
{% highlight raw %}
vi /etc/chrony.conf
{% endhighlight %}

-   On the server directive, change the NTP server source with your NTP server. In this example, the NTP Server is running the server with IP address '192.168.64.15'.
-   Also, you can see additional options on the server directive"
    -   The iburst option allows the Chronyd service to make the first update of the clock shortly after the start.
    -   The prefer option will prioritize the NTP Server source among other servers without prefer option.
{% highlight raw %}
server 192.168.64.15 iburst prefer
{% endhighlight %}
![chrony3][3]
-   Save the file and exit the editor when you're done.

-   Now, run the following command to restart the Chrony service and apply new configurations.
{% highlight raw %}
sudo systemctl restart chronyd
{% endhighlight %}

-   Finally, run the following chronyc command to verify the current status of NTP on the 'node2' machine.
{% highlight raw %}
chronyc tracking
{% endhighlight %}
-   You should see the 'node2' machine is connected and synchronized the time to the NTP Server 'ntp.hwdomain.io', which is the server IP address '192.168.64.15'.
![chrony4][4]

-   You can also verify the detailed NTP data via the chronyc command below.
{% highlight raw %}
chronyc ntpdata
{% endhighlight %}
![chrony5][5]

---

> :metal: :metal: :metal: :metal: :metal: :metal: :metal:

---

> :memo: Thanks for reading and do not forget set your watch correct time :clock1: :clock4: :clock9: :clock2: :joy: :joy:

Guneycan Sanli.

---

[1]: ../assets/images/chrony/chrony1.jpeg
[2]: ../assets/images/chrony/chrony2.jpg
[3]: ../assets/images/chrony/chrony3.jpg
[4]: ../assets/images/chrony/chrony4.jpg
[5]: ../assets/images/chrony/chrony5.jpg
