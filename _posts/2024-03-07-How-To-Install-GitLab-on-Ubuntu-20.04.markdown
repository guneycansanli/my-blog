---
title: "How To Install and Configure GitLab on Ubuntu on Ubuntu 20.04"
layout: post
date: 2024-03-07 14:20
image: ../assets/images/gitlab/main.jpg
headerImage: true
tag:
    - linux
    - ubuntu
    - gitlab
category: blog
author: guneycansanli
description: How To Install and Configure GitLab on Ubuntu on Ubuntu 20.04
---

# GitLab Introduction ?

GitLab is an open-source application primarily used to host Git repositories. Gitlab has same features as GitHub, GitLab also is a populer tool for CI automations.

The GitLab project enables you to create a GitLab instance on your own hardware with a minimal installation mechanism. This guide I will install and configure GitLab Community Edition on an Ubuntu server.

---

# Prerequisites

-   Ubuntu VM, or Server.

---

# Installing the Dependencies

1- Before installing GitLab, it is important to install the software that it leverages during installation and on an ongoing basis. The required software can be installed from Ubuntu’s default package repositories.

```
    sudo apt update
```

2- Then install the dependencies

```
    sudo apt install ca-certificates curl openssh-server postfix tzdata perl
```

3- During the postfix installation, a configuration window will appear. Choose “Internet Site” and enter your server’s hostname as the mail server name. This will allow GitLab to send email notifications.

![gitlab][1]

4- Choose “Internet Site” and then slect OK.

![gitlab][1]

5- Now that you have the dependencies installed, you’re ready to install GitLab.

---

# Installig GitLab

1- With the dependencies in place, you can install GitLab. This process leverages an installation script to configure your system with the GitLab repositories.

2- Move into the /tmp directory and then download the installation script:

```
    cd /tmp
    curl -LO https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh
```

![gitlab][3]

3- Run the installer.

```
    sudo bash /tmp/script.deb.sh
```

![gitlab][4]

4- The script sets up your server to use the GitLab maintained repositories. This lets you manage GitLab with the same package management tools you use for your other system packages. Once this is complete, you can install the actual GitLab application with apt

```
    sudo apt install gitlab-ce
```

5- This installs the necessary components on your system.
![gitlab][5]

---

# Editing the GitLab Configuration File

1- Before you can use the application, update the configuration file and run a reconfiguration command. First, open GitLab’s configuration file with your preferred text editor

```
    sudo vi /etc/gitlab/gitlab.rb
```

2- Find the external_url configuration line. Update it to match your domain if you have DNS and public(WAN) IP and make sure to change http to https to automatically redirect users to the site protected by the Let’s Encrypt certificate.

3- At that point I will use my localhost only. I have not public DNS and Public IP.

![gitlab][6]

4- Once you’re done making changes, save and close the file. You can also enable couple of more options like SSL or contact email.

Run the following command to reconfigure GitLab.

```
    sudo gitlab-ctl reconfigure
```

5- This is a automated process so You should wait until command configure GitLAb for you. You will see long output on the terminal.
![gitlab][7]

---

# Access GitLab Web Interface

1- With GitLab installed and configured, open your web browser and enter your server’s IP address or hostname.

2- User Name: **root**

3- Password : **Find from /etc/gitlab/initial_root_password**

```
    sudo cat /etc/gitlab/initial_root_password
```

---

# Configure the PHP for Zabbix Frontend

1- Edit php_value date.timezone line to match your time zone.

```
    vi /etc/zabbix/apache.conf
```



2- After you edit php file, You need to restart services again. It you will not restart You may have see error When you are Configuring Settings for the Zabbix Web Interface.

```
    systemctl restart zabbix-server zabbix-agent apache2
```

---

# Accessing Zabbix Frontend

1- We are ready to access Zabbix UI.

2- You just need to open your browser and reach your VM's LAB ip and /zabbix.

3- If you try http://your_server_ip_or_domain/zabbix. You should see the Zabbix setup wizard. Follow the steps to complete the setup.


---

---

---

---

> :blush: :star: :boom: :fire: :+1: :eyes: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/gitlab/gitlab1.jpg
[2]: ../assets/images/gitlab/gitlab2.jpg
[3]: ../assets/images/gitlab/gitlab3.jpg
[4]: ../assets/images/gitlab/gitlab4.jpg
[5]: ../assets/images/gitlab/gitlab5.jpg
[6]: ../assets/images/gitlab/gitlab6.jpg
[7]: ../assets/images/gitlab/gitlab7.jpg
[8]: ../assets/images/gitlab/gitlab1.jpg
[9]: ../assets/images/gitlab/gitlab1.jpg

```

```
