---
title: "How To Install and Configure Zabbix on Ubuntu 20.04"
layout: post
date: 2024-02-28 14:20
image: ../assets/images/vnc/main.jpg
headerImage: true
tag:
    - linux
    - ubuntu
    - zabbix
category: blog
author: guneycansanli
description: How To Install and Configure Zabbix on Ubuntu 20.04
---

# What is Zabbix ?

Zabbix is open source monitoring tool. You can monitor networks and applications such as virtual machines, network devices, and web applications.

Zabbix uses several options for collecting metrics, including agentless monitoring of user services and client-server architecture. Zabbix supports encrypted communication between the server and connected clients, so your data is protected while it travels over insecure networks.

You can set up Zabbix with mySql or PostgresSQL.You can also store historical data in NoSQL databases like Elasticsearch and TimescaleDB.

---

# Prerequisites

-   2 Ubuntu 20.04 server with a non-root administrative user.

---

# Update Your System

1- Before start installation Zabbix make sure Ubuntu is up-to-date.

```
    sudo apt update
    sudo apt upgrade
```

2- For the latest security patches and software updates is important for your system.

---

# Install Zabbix Server

1- Let's Install Zebbix server.
2- Zabbix provides the packages for easy installation.

```
    wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1+focal_all.deb
    dpkg -i zabbix-release_5.0-1+focal_all.deb
    apt update
```

![zabbix][1]

2- I am using Zabbix 5.0, but make sure to check for the latest version.

---

# Install Zabbix Components

1- Once the repository is set up, install Zabbix server, frontend, and agent:

```
    apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-agent -y
```

2- The commands will install Zabbix server, responsible for gathering data, along with the frontend for web-based interaction. Additionally, it includes the installation of the Zabbix agent, which monitors local resources specifically on the Zabbix server.

---

# Set Up the Database

1- Zabbix uses databe as I mentioned that is why We need to set up a database. I will go with MySQL database.

```
    apt install mysql-server
    mysql_secure_installation
```

2- You may set root password your mySql service. You can follow up the installation steps When you run mysql_secure_installation
3- I used basic password for mySql.

4- After installing MySQL, create a database and user for Zabbix

5- Log in to MySQL as the root user:

```
    mysql -u root -p
```

![zabbix][2]

6- Create the Zabbix database with UTF-8 character support:

```
   mysql> create database zabbix character set utf8 collate utf8_bin;
```

![zabbix][3]

7- Then create a user that the Zabbix server will use, give it access to the new database, and set the password for the user:

```
   mysql>  create user zabbix@localhost identified by 'your_zabbix_mysql_password';
```

```
   mysql>  grant all privileges on zabbix.* to zabbix@localhost;
```

![zabbix][4]

8- That takes care of the user and the database. Exit out of the database console.

```
   mysql>  quit;
```

9- Now We need to create table and other DB stuff for Zabbix.
10- The Zabbix installation provided you with a file that sets this up. Run the following command to set up the schema and import the data into the zabbix database. We will use zcat since the data in the file is compressed.
11- Once you run command use your zabbix DB user's password and wait couple of minutes.

```
   zcat /usr/share/doc/zabbix-server-mysql*/create.sql.gz | mysql -uzabbix -p zabbix
```

![zabbix][5]

12- Then You need to change Zabbix's configuration for using mySql. You need to set the database password in the Zabbix server configuration file. Open the configuration file in your preferred text editor. I will use vi.

```
   vi /etc/zabbix/zabbix_server.conf
```

13- Find the Option: DBPassword and uncomment the DBPassword and enter your DB user's password.
![zabbix][6]

14- Save and Exit.

---

# Restart and Enable Zabbix Server

1- After configuring, restart the Zabbix server and make it start at system boot:

```
    systemctl restart zabbix-server zabbix-agent apache2
    systemctl enable zabbix-server zabbix-agent apache2
```

---

# Configure the PHP for Zabbix Frontend

1- Edit php_value date.timezone line to match your time zone.

```
    vi /etc/zabbix/apache.conf
```

![zabbix][7]

2- After you edit php file, You need to restart services again. It you will not restart You may have see error When you are Configuring Settings for the Zabbix Web Interface.

```
    systemctl restart zabbix-server zabbix-agent apache2
```

---

# Accessing Zabbix Frontend

1- We are ready to access Zabbix UI.
2- You just need to open your browser and reach your VM's LAB ip and /zabbix.
3- If you try http://your_server_ip_or_domain/zabbix. You should see the Zabbix setup wizard. Follow the steps to complete the setup.
![zabbix][8]

![zabbix][9]

---

# Configuring Settings for the Zabbix Web Interface

1- You will see Welcome page. It is user-friendly so just follow the steps.
2- On the next screen, you will see the table that lists all of the prerequisites to run Zabbix.
![zabbix][10]

3- All values show OK so We are good to go next steps.
4- The next screen asks for database connection information.
5- We are running mySql on local so default setting is fine just need to put right DB password that You used when you created ZAbbix user for DB.
![zabbix][11]

6- On the next screen, you can leave the options at their default values.
![zabbix][12]

7- Next sreen shows summary of you configs.
8- Click Next step to proceed to the final screen.

9- The Front-end setup is now complete. Click Finish to proceed to the login screen. The default user is **Admin** and the password is **zabbix**.
![zabbix][13]

10- Let's login to Zabbix.
![zabbix][14]

11- And here We are.
![zabbix][15]

---

---

# Installing and Configuring the Zabbix Agent

1- We setup Zabbix successfully.
2- Now We will setup a Zabbix agent and will monitor it.
3- Now you need to configure the agent software that will send monitoring data to the Zabbix server.
4- SSH to client node(Second VM)
5- Before start installation Zabbix make sure Ubuntu is up-to-date.

```
    sudo apt update
    sudo apt upgrade
```

6- Just like on the Zabbix server, run the following commands to install the repository configuration package

```
    wget https://repo.zabbix.com/zabbix/5.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_5.0-1+focal_all.deb
    dpkg -i zabbix-release_5.0-1+focal_all.deb
```

![zabbix][16]

7- Next, update the package index.

```
    apt update
```

8- We are ready to install the Zabbix agent.

```
    apt install zabbix-agent -y
```

![zabbix][17]

9- While Zabbix supports certificate-based encryption, setting up a certificate authority is beyond the scope of this tutorial. But you can use pre-shared keys (PSK) to secure the connection between the server and agent.
10- First, generate a PSK.

```
    sh -c "openssl rand -hex 32 > /etc/zabbix/zabbix_agentd.psk"
```

11- Check your key.

```
    cat /etc/zabbix/zabbix_agentd.psk
```

![zabbix][18]

12- Save your key We will use it later.
13- Now edit the Zabbix agent settings to set up its secure connection to the Zabbix server. Open the agent configuration file in your text editor.

```
    vi /etc/zabbix/zabbix_agentd.conf
```

14- Each setting within this file is documented via informative comments throughout the file, but you only need to edit some of them.
15 -First you have to edit the IP address of the Zabbix server. Find the following section.
![zabbix][19]

16- By default, Zabbix server connects to the agent. But for some checks (for example, monitoring the logs), a reverse connection is required. For correct operation, you need to specify the Zabbix server address and a unique host name.

17- Find the section that configures the active checks and change the default values.
![zabbix][20]

18- Next, find the section that configures the secure connection to the Zabbix server and enable pre-shared key support. Find the TLSConnect section, which looks like this.
![zabbix][21]

19- Next, locate the TLSAccept section, which looks like this.
![zabbix][22]

20- Next, find the TLSPSKIdentity section, which looks like this.
![zabbix][23]

21- You’ll use this as the PSK ID when you add your host through the Zabbix web interface.
22- Then set the option that points to your previously created pre-shared key. Locate the TLSPSKFile option.
![zabbix][24]

23- Save and close the file. Now you can restart the Zabbix agent and set it to start at boot time.

```
    systemctl restart zabbix-agent
    systemctl enable zabbix-agent
```

24- For good measure, check that the Zabbix agent is running properly.
```
    systemctl status zabbix-agent
```

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
