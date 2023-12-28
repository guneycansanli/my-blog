---
title: "How to use /etc/rc.local at boot"
layout: post
date: 2023-12-30 14:20
image: ../assets/images/rc-local/main.jpg
headerImage: true
tag:
    - linux
    - debian
category: blog
author: guneycansanli
description: How to use /etc/rc.local at boot
---

# What is rc.local?

- The rc.local script in some Linux distributions and Unix systems is a superuser startup script, usually located under the directory /etc/etc/rc.d. The file name rc refers to Run Control.
- Rc.local is an obsolete script kept for compatibility purposes for systemV systems.
- A run level is a state of the system, indicating whether it is in the process of booting or rebooting or shutting down, or in single-user mode, or running normally.  The traditional init program handles these actions by switching to the corresponding run level.  Under Linux, the run levels are by convention: S while booting, 0 while shutting down, 6 while rebooting, 1 in single-user mode and 2 through 5 in normal operation.  Runlevels 2 through 5 are known as multiuser runlevels since they allow multiple users to log in, unlike run level 1 which is intended for only the system administrator.
- Once init loads, it first runs the /etc/rc.d/rc.sysinit script, which sets the environment path, starts the swap, checks the file systems, and executes all other steps required for system initialization and then it will read it’s configuration file /etc/inittab.

# /etc/rc.local file

- The /etc/rc.local file plays a role in system configuration, particularly during the transition from single-user mode to a multiuser run level. In single-user mode, basic system configuration is performed, followed by the loading of the default run level from /etc/inittab. As the run level changes, traditional init systems execute rc scripts to start and stop system services. The /etc/rc.local script is reserved for system administrators and runs after normal system services are started during the switch to a multiuser run level. It is commonly used for custom services or tasks such as initializing devices without creating complex initialization scripts in /etc/rc.d/init.d/. While not essential for most installations, /etc/rc.local serves a purpose for specific cases where it is required.

# When to Avoid Using /etc/rc.local:

- Starting Application Services:

  - Issue: While rc.local can launch applications during system boot, it lacks the ability to stop applications gracefully during system shutdown.
  - Risk: Applications may not stop correctly, potentially causing problems on the next boot.
  - Recommendation: It is advisable to create proper init scripts for application services, ensuring both startup and shutdown procedures are handled gracefully.

- Network/Firewall Configuration:

  - Issue: Using rc.local for network or firewall tasks is discouraged as it's not the designated location for such configurations.
  - Risk: Incorrect placement of network or firewall settings can lead to misconfigurations.
  - Recommendation: Network and firewall configurations should be placed in their designated locations, typically under the /etc/sysconfig directory.

# When to Use /etc/rc.local:

- Task Involving Basic Script/Command Execution:
Criteria: If the task primarily involves running a simple script or command without modifying network or application service configurations.
Examples: Running standalone scripts, initializing devices, or performing basic system-level tasks.
Consideration: Suitable for tasks that don't interfere with the network or application service settings and do not require intricate initialization scripts.
- In summary, /etc/rc.local is appropriate for straightforward tasks like running scripts or commands that do not involve complex configurations of network settings or application services. For more sophisticated tasks, such as starting and stopping applications or adjusting firewall rules, it is recommended to use dedicated init scripts and designated configuration directories.

- In Debian 10 (Buster) and later versions, the use of the rc.local file has been deprecated in favor of other methods for running commands or scripts at boot time. The recommended alternatives include using systemd services or cron jobs. Here are two common alternatives:
1. Using Systemd
2. Using Cron

- Even rc.local file has been deprecated We can use it with additional steps
- First we need to create a service file:
{% highlight raw %}
root@debian10-master:~# cat /etc/systemd/system/rc-local.service
[Unit]
Description=/etc/rc.local
ConditionPathExists=/etc/rc.local

[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
StandardOutput=tty
RemainAfterExit=yes
SysVStartPriority=99

[Install]
WantedBy=multi-user.target
{% endhighlight %}
![rc-local][1]


- Then we create the rc.local file
{% highlight raw %}
cat /etc/rc.local
#!/bin/sh -e
#
# rc.local
#
# This script is executed at the end of each multiuser runlevel.
# Make sure that the script will "exit 0" on success or any other
# value on error.
#
# In order to enable or disable this script just change the execution
# bits.
#
# By default this script does nothing.
 
exit 0
{% endhighlight %}

- Set the permissions:
{% highlight raw %}
chmod +x /etc/rc.local
{% endhighlight %}
- And enable the service to start during boot
{% highlight raw %}
systemctl enable rc-local
{% endhighlight %}
- And finaly start the service
{% highlight raw %}
systemctl start rc-local
{% endhighlight %}
- You can view the status of the service with:
  - And enable the service to start during boot
{% highlight raw %}
systemctl status rc-local
{% endhighlight %}
![rc-local][2]

- You can now add what you want back into the rc.local file, of course this is not the ‘correct’ way of doing, best would be to add a service for your scripts/programs in systemd where possible.


# Demonstration: Using /etc/rc.local on Debian 10

- Objective: Illustrate the utilization of the /etc/rc.local file on a Debian 10 system by incorporating a command to create a directory and a script.

  - Choice of System: For this demonstration, Debian 10 will be utilized.
  - Task in /etc/rc.local:
  - Command: Add a command to create a directory and a script.
  - Path Specification: Utilize complete paths to ensure command execution, especially if the PATH variable is not consulted.

{% highlight raw %}
mkdir /home/this-is-rclocal-directory
echo "Hi hi hi hi"
{% endhighlight %}
![rc-local][3]

- Just crate a sample command or script for rc.local
- After reboot the node you can check the script was ran or not.
![rc-local][4]

---
---

> :blush: :star: :boom: :fire: :+1: :eyes: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/rc-local/rc-local1.jpg
[2]: ../assets/images/rc-local/rc-local2.jpg
[3]: ../assets/images/rc-local/rc-local4.jpg
[4]: ../assets/images/rc-local/rc-local3.jpg

