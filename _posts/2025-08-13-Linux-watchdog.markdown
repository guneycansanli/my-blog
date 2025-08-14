---
title: "Linux Watchdog - Automatic Recovery for Unresponsive Systems"
layout: post
date: 2025-08-13 11:00
image: ../assets/images/watchdog/main.jpg
headerImage: true
tag:
    - linux
    - watchdog
category: blog
author: guneycansanli
description: Linux Watchdog - Automatic Recovery for Unresponsive Systems
---

# Introduction

Any Linux system — from a small edge device to a large server — can unexpectedly freeze. When that happens on a remote machine, the only “fix” might be a manual reboot, which isn’t much help if you’re nowhere near the hardware.

The Linux Watchdog is a built-in safety mechanism that can automatically restart an unresponsive system, reducing downtime and eliminating the need for someone to physically press the reset button.


## 1. How the Watchdog Works

A watchdog is essentially a countdown timer that must be regularly reset (“kicked”) by the system. If the timer expires without a kick, it assumes the system has crashed and performs a reboot.

Two main types exist:

- Hardware Watchdog – A physical timer chip independent of the CPU. More reliable because it works even if the operating system is locked.
- Software Watchdog – A kernel module or process that performs the same role, but may fail if the OS is completely frozen.

---

## 2. Identifying Your Watchdog Devices

Watchdog devices appear in /dev. To check:

```bash
ls -l /dev | grep watchdog

#Example output:
crw-------  1 root root  10, 130 Aug 14 12:34 watchdog
crw-------  1 root root 252,   0 Aug 14 12:34 watchdog0
```

![watch][1]

What these mean:

- **/dev/watchdog** – The main watchdog interface.
- **/dev/watchdog0** – The actual hardware watchdog device.

Files are not readable that’s expected — these devices are not readable text files; they are control interfaces.

---

## 3. Manually Using the Watchdog

To start the watchdog timer:

```bash
echo 1 > /dev/watchdog
```

To keep it alive, feed it at intervals:

```bash
while true; do
    echo 1 > /dev/watchdog
    sleep 10
done
```

To stop it before the timeout expires:

```bash
echo V > /dev/watchdog
```

⚠️ If you start the watchdog and do not feed it, your system will reboot when the timer runs out.

---

## 4. Automating with the Watchdog Daemon

Linux provides a ready-to-use watchdog daemon that can monitor multiple health indicators.

```bash
sudo apt update
sudo apt install watchdog
```

![watch][2]

```bash
sudo systemctl enable watchdog
sudo systemctl start watchdog
```

![watch][3]

## 5. Configuring /etc/watchdog.conf

Edit the configuration file:

```bash
sudo nano /etc/watchdog.conf
```

Example:


```bash

#watchdog device
watchdog-device = /dev/watchdog

#interval
interval = 10

# Reboot if CPU load average exceeds this threshold
max-load-1 = 30

# Minimum free memory in MB
min-memory = 2

# Check if a specific network interface is up
interface = enp1s0

# Give watchdog process higher scheduling priority
realtime = yes
priority = 1
```

![watch][4]

After changes:

```bash
sudo systemctl restart watchdog
```

## 6. Watchdog in /sys/class/watchdog/

The watchdog device exposes runtime settings here:

```bash
ls /sys/class/watchdog/
```

Example output:

```bash
watchdog0
```

Check the current timeout:

```bash
cat /sys/class/watchdog/watchdog0/timeout
```

![watch][5]

Set a new timeout (e.g., 45 seconds):

```bash
echo 45 | sudo tee /sys/class/watchdog/watchdog0/timeout
```

## 7. Testing the Watchdog

You can simulate a system hang by overloading the CPU:

```bash
:(){ :|:& };:
```

Once the system becomes unresponsive, the watchdog should trigger a reboot.

## 8. Service-Level Watchdog with systemd

You can also monitor specific services so they are restarted automatically if they stop responding.

Example service unit:

```bash
[Unit]
Description=My Background Service

[Service]
ExecStart=/usr/local/bin/my-service
Restart=on-failure
WatchdogSec=20s

[Install]
WantedBy=multi-user.target
```


**WatchdogSec** tells systemd to expect periodic “keep-alive” messages from the service. If it doesn’t get them, it restarts the service.

---

## 9. Best Practices

Prefer hardware watchdogs if available.
Choose reasonable timeouts — too short can cause false triggers, too long delays recovery.

Monitor logs with:

```bash
journalctl -u watchdog
```

or

```bash
#That need to edit conf file to send logs 
grep watchdog /var/log/syslog
```

Treat watchdogs as a last line of defense, not a substitute for fixing bugs.

# Conclusion

The Linux watchdog acts like an invisible hand that presses the reset button when your system freezes. By configuring it properly, you can ensure that even if something goes wrong, recovery happens automatically and with minimal downtime.
For remote servers, headless machines, or critical applications, enabling the watchdog can turn a risky single point of failure into a self-healing system.

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/watchdog/watchdog-1.jpg
[2]: ../assets/images/watchdog/watchdog-2.jpg
[3]: ../assets/images/watchdog/watchdog-3.jpg
[4]: ../assets/images/watchdog/watchdog-4.jpg
[5]: ../assets/images/watchdog/watchdog-5.jpg




