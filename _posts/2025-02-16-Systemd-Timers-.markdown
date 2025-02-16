---
title: "Systemd Timers Cron Alternative"
layout: post
date: 2025-02-16 12:20
image: ../assets/images/systemdtimer/main.jpg
headerImage: true
tag:
    - linux
    - cron
    - systemd
category: blog
author: guneycansanli
description: Use Systemd Timers Instead of Cronjobs
---

# A Beginner's Guide to Systemd Timers  

## Introduction  

Systemd timers offer a modern and flexible way to schedule automated tasks in Linux. Unlike traditional cron jobs, systemd timers integrate directly with systemd services, providing enhanced logging, better dependency management, and improved error handling.  

We can use systemd timers insteaed of **cron**

In this guide, you'll learn how to create and manage systemd timers with practical examples.


---

## What Are Systemd Timers?

A systemd timer is a unit file in systemd that schedules tasks at specific intervals or times. It serves as a replacement for **cron** while offering:  

- **Centralized logging and monitoring** via `journalctl`  
- **Tighter integration with systemd services**  
- **Flexible scheduling options** using time-based and calendar-based triggers  
- **Improved error handling** and dependency management  

To list all available timers on your system, run:  
```sh
systemctl list-timers --all
```
or

```sh
systemctl status *timer
```

![timer][1]

![timer][2]

---

## How Systemd Timers Work

Systemd timers consist of two main unit files:

1. Timer Unit File – Defines when a task should run.
2. Service Unit File – Specifies what task should be executed.

For example, to run a Memory check script every minutes:
 - A service file defines the backup script execution.
 - A timer file schedules the service execution at a specific time.

You can control timers using standard systemctl commands:

```sh
systemctl start mytimer.service  
systemctl stop mytimer.service
systemctl enable mytimer.timer  
systemctl status mytimer.timer  
```

## Understanding Systemd Timer Expressions

Systemd timers use a structured time format:

```plaintext
* *-*-* *:*:*
│ │ │ │ │ │ │
│ │ │ │ │ │ └──── Seconds (0 - 59)
│ │ │ │ │ └────── Minutes (0 - 59)
│ │ │ │ └──────── Hours (0 - 24)
│ │ │ └────────── Day of Month (1 - 31)
│ │ └──────────── Month (1 - 12)
│ └────────────── Year
└──────────────── Day of Week (Sun - Sat)
```

### Example Expressions

<table style="border-collapse: collapse; width: 100%;">
  <tr style="background-color: #4CAF50; color: white;">
    <th style="border: 1px solid black; padding: 8px;">Schedule</th>
    <th style="border: 1px solid black; padding: 8px;">Systemd Timer Expression</th>
    <th style="border: 1px solid black; padding: 8px;">Description</th>
  </tr>
  <tr style="background-color: #e7f7ff;">
    <td style="border: 1px solid black; padding: 8px;">Every 10 minutes</td>
    <td style="border: 1px solid black; padding: 8px;"><code>*-*-* *:0/10:00</code></td>
    <td style="border: 1px solid black; padding: 8px;">Runs every 10 minutes</td>
  </tr>
  <tr style="background-color: #e7f7ff;">
    <td style="border: 1px solid black; padding: 8px;">Every Monday at 8 AM</td>
    <td style="border: 1px solid black; padding: 8px;"><code>Mon *-*-* 08:00:00</code></td>
    <td style="border: 1px solid black; padding: 8px;">Runs every Monday at 8 AM</td>
  </tr>
  <tr style="background-color: #e7f7ff;">
    <td style="border: 1px solid black; padding: 8px;">First of every month</td>
    <td style="border: 1px solid black; padding: 8px;"><code>*-*-01 00:00:00</code></td>
    <td style="border: 1px solid black; padding: 8px;">Runs at midnight on the 1st of each month</td>
  </tr>
  <tr style="background-color: #e7f7ff;">
    <td style="border: 1px solid black; padding: 8px;">Every weekday at noon</td>
    <td style="border: 1px solid black; padding: 8px;"><code>Mon..Fri *-*-* 12:00:00</code></td>
    <td style="border: 1px solid black; padding: 8px;">Runs at noon from Monday to Friday</td>
  </tr>
</table>


## Creating a Systemd Timer

- Step 1: Create a Service File 

1. First, create a service file to define the task.
2. I will use **free** command as an example and I will check free memory on the box 
3. For systemd service creation You need to create **unit** file 

```bash
vi /etc/systemd/system/myMemoryMonitor.service 
```

![timer][3]

4. Let's Check status of service
```bash
systemctl status myMemoryMonitor.service 
```

5. Let's start the sevice
```bash
systemctl start myMemoryMonitor.service 
```

![timer][4]

6. As You can see above output myMmyMemoryMonitor Service started and finished after **free** command ran. 
7. We can check **journal logs** to see more detailed service logs.

```bash
journalctl -S today -u myMemoryMonitor.service 
```

![timer][5]

- Step 2: Create the Timer File

  1. Now, create a timer file to schedule the service execution.
  2. We need to create a new file **myMemoryMonitor.timer** (same name of service with .timer) , in **/etc/systemd/system**


```bash
vi /etc/systemd/system/myMemoryMonitor.timer 
```

![timer][6]

3. We can enable **timer** service
```bash
systemctl start myMemoryMonitor.timer 
```

4. We can wtach logs real time to see what services will do.
```bash
journalctl -S today -f -u myMemoryMonitor.service
```

5. If you start timer **myMemoryMonitor.service** will run every minutes.

![timer][7]

## Step 3: Enable and Start the Timer

- Once We have verified We are ready to enable **timer** service 
- Reload systemd, enable, and start the timer:

```bash
sudo systemctl daemon-reload
sudo systemctl enable myMemoryMonitor.service
sudo systemctl start myMemoryMonitor.service
```

- Let's verify timer service added to **list-timers**

```bash
systemctl list-timers --all
```

![timer][8]


# Conclusion
Systemd timers provide a robust, flexible alternative to cron jobs with better logging and service management. By learning to use systemd timers, you can automate tasks efficiently while maintaining system stability.

---

You can also check out this article: [https://coady.tech/systemd-timer-vs-cron/)](https://coady.tech/systemd-timer-vs-cron/)

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

[1]: ../assets/images/systemdtimer/timer1.jpg
[2]: ../assets/images/systemdtimer/timer2.jpg
[3]: ../assets/images/systemdtimer/timer3.jpg
[4]: ../assets/images/systemdtimer/timer4.jpg
[5]: ../assets/images/systemdtimer/timer5.jpg
[6]: ../assets/images/systemdtimer/timer6.jpg
[7]: ../assets/images/systemdtimer/timer7.jpg
[8]: ../assets/images/systemdtimer/timer-8.jpg



