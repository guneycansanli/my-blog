---
title: "Managing Log Files with Logrotate on Debian10"
layout: post
date: 2023-12-08 22:00
image: ../assets/images/logrotate/main.png
headerImage: true
tag:
    - linux
    - logrotate
    - debian
category: blog
author: guneycansanli
description: Managing Log Files with Logrotate
---

# What is Packer?

- Controlling the sizes of log files on a Linux server is crucial due to their continuous growth. As log files accumulate, they can consume valuable storage space, strain server resources, and cause performance and memory issues. To address this problem, log rotation is commonly employed. It involves renaming or compressing log files before they become too large, while also removing or archiving old logs to free up storage space. On most Linux distributions, the preferred tool for log rotation is the logrotate  program, which we will be focusing on in this tutorial.
- In this tutorial We will create a script which is logs randomly and We will compress the logs with logrotate.

# Prerequisites

- Before proceeding with the rest of this tutorial, please ensure that you have:

- A basic knowledge of working with the Linux command line.
- We'll be using Debian 10(buster) throughout in this guide but everything should work even if you're on some other distribution.
- Prior knowledge of how to work with system log files on Linux.

# Getting started with Logrotate

- The logrotate daemon is pre-installed and active by default in Debian and most mainstream Linux distributions. If Logrotate is not installed on your machine, ensure to install it first through your distribution's package manager.

- You can test if you have already logrotate installed with:
{% highlight raw %}
logrotate --version
{% endhighlight %}
![logrotate][1]

- The Logrotate daemon uses configuration files to specify all the log rotation details for an application. The default setup consists of the following aspects:
- /etc/logrotate.conf: this is the main configuration file for the Logrotate utility. It defines the global settings and defaults for log rotation that are applied to all log files unless overridden by individual Logrotate configuration files in the /etc/logrotate.d/ directory.
- /etc/logrotate.d: this directory includes files that configure log rotation policies specific to the log files produced by a individual applications or services.
- There are so much details for log rotation You can find easily by searching especially logrotation commands (rotate, compress, daily, size, create, notifempty...)
- We will make a project for it today.

# Configuring log rotation for a custom application

- We will create a script that is running and create randomly logs and We will configure logrotate for it.
- Use below script and run it background.
- To simulate an application that writes logs continuously to a file, create the following bash script somewhere on your filesystem. It writes fictional but realistic-looking log records to a file every second:
{% highlight raw %}
#!/bin/bash

logfile="/var/log/logify/log_records.log"

# Function to generate a random log record
 generate_log_record() {
    local loglevel=("INFO" "WARNING" "ERROR")
    local services=("web" "database" "app" "network")
    local timestamps=$(date +"%Y-%m-%d %H:%M:%S")
    local random_level=${loglevel[$RANDOM % ${#loglevel[@]}]}
    local random_service=${services[$RANDOM % ${#services[@]}]}
    local message="This is a sample log record for ${random_service} service."

    echo "${timestamps} [${random_level}] ${message}"
 }

 # Main loop to write log records every second
 while true; do
    log_record=$(generate_log_record)
    echo "${log_record}" >> "${logfile}"
    sleep 1
 done
{% endhighlight %}

- Save the file, then make it executable:
{% highlight raw %}
chmod +x logify.sh
{% endhighlight %}
![logrotate2][2]

- Afterward, create the /var/log/logify directory using elevated privileges, then change the ownership of the directory to your user so that the script can write files to the directory:
{% highlight raw %}
mkdir /var/log/logify
{% endhighlight %}

- You are ready for run our script , You may run it as a background process
{% highlight raw %}
./logify.sh &
{% endhighlight %}

- You can verify script is logging to /var/log/logify/log_records.log
{% highlight raw %}
cat /var/log/logify/log_records.log
{% endhighlight %}
![logrotate3][3]

- At this stage, you must set up a log rotation policy to prevent the log_records.log file from growing too large and taking up valuable disk space on the server. There are two main options for doing this:
- Create a new Logrotate configuration file and place it in the /etc/logrotate.d/ directory to perform log rotation according to the system's default schedule (it runs once per day by default but you can change it.)

# Creating a standard Logrotate configuration

- In this section, you will create a standard configuration file for your application logs and place it in the /etc/logrotate.d/ directory. Go ahead and create a new logify file in the /etc/logrotate.d/ directory with your text editor:
{% highlight raw %}
vi /etc/logrotate.d/logify
{% endhighlight %}

- Add the following text to the file:
{% highlight raw %}
/var/log/logify/*.log
{
    hourly
    missingok
    rotate 7
    compress
    notifempty
}
{% endhighlight %}
![logrotate4][4]
- As you can see I used hourly but You can change this configuration with different options.
- The configuration above applies to all the files ending with .log in the /var/log/logify/ directory. We've already discussed what each directive here does earlier, so we won't go over that again here.
- Save the file and test the new configuration by executing the command below. The --debug option instructs logrotate to operate in test mode where only debug messages are printed.
{% highlight raw %}
logrotate /etc/logrotate.conf --debug
{% endhighlight %}
- You can see your new configuration and all other configurations
![logrotate5][5]

- If you want to test that the log rotation works without without waiting for the specified schedule, you can use the -f/--force option like this:
{% highlight raw %}
logrotate -f /etc/logrotate.d/logify
{% endhighlight %}

- You will observe that the old log file was renamed and compressed and a new one was created:
{% highlight raw %}
ls /var/log/logify/
{% endhighlight %}
![logrotate6][6]

- Another way to verify if a particular log file is rotating or not, and to check the last date and time of its rotation, examine the /var/lib/logrotate/status file (or /var/lib/logrotate/logrotate.status on Red Hat systems) like this:
{% highlight raw %}
 cat /var/lib/logrotate/status | grep 'logify'
{% endhighlight %}
![logrotate7][7]

- There are different ways for logrotation like Creating a system-independent Logrotate configuration, Running commands or scripts before or after log rotation, Debugging Logrotate problems. 
- I wanted to show you how logrotate works and how it usefull logrotate for avoid for high disk usage.
- I hope It would be usefull for you.

---
---

> :metal: :metal: :metal: :metal: :metal: :metal: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/logrotate/logrotate1.jpg
[2]: ../assets/images/logrotate/logrotate2.jpg
[3]: ../assets/images/logrotate/logrotate3.jpg
[4]: ../assets/images/logrotate/logrotate4.jpg
[5]: ../assets/images/logrotate/logrotate5.jpg
[6]: ../assets/images/logrotate/logrotate7.jpg