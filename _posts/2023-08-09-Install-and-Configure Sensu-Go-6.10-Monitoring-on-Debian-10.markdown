---
title: "Install and Configure Sensu-Go Monitoring on Debian 10"
layout: post
date: 2023-07-31 14:20
image: ../assets/images/sensu/sensuMain.png
headerImage: true
tag:
    - sensu
    - sensu-go
    - monitoring
category: blog
author: guneycansanli
description: How to Install and Configure Sensu-Go Monitoring on Debian 10
---

# Sensu Monitoring Tool 
 Sensu(Sensu Core) is a free open-source monitoring tool. Sensu known as Sensu Go now. Sensu and Sensu Go are both open-source monitoring and event management tools, but they represent different versions of the same project, with Sensu Go being the newer and more advanced version. Here are the key differences.

- Sensu: Sensu (often referred to as "Sensu Classic") has a monolithic architecture where all components are tightly integrated into a single package. It uses Redis for storing data and RabbitMQ for handling message queues.
- Sensu Go: Sensu Go is designed with a modern microservices architecture. It decouples its components and services, making them easier to deploy, manage, and scale. It uses an embedded etcd data store for configuration and state management, and supports both the agent-based and agentless monitoring approaches.


## Components of Sensu Go

- Sensu Agents: These are little clients that run on the infrastructure components you wish to keep track of. Agents are entities that are automatically registered with Sensu and are in charge of creating check and metric events to transmit to the backend event pipeline.
- Sensu backend: This is an embedded transport and etcd datastore, allowing you to create flexible and automated workflows for routing metrics and alerts. Sensu backends need permanent storage for their embedded database, as well as disk space for local asset caching and a few accessible ports.
- Sensuctl: This is a command-line utility used to manage Sensu’s resources. It creates, reads, updates, and deletes resources, events, and entities by using Sensu’s HTTP API.

##  Step 1: Install Sensu Backend
1. Start by adding Sensu repository

{% highlight raw %}
curl -s https://packagecloud.io/install/repositories/sensu/stable/script.deb.sh | sudo bash
{% endhighlight %}
![sensu1][1]


2. Install Sensu Backend:
{% highlight raw %}
apt install sensu-go-backend
{% endhighlight %}
---

## Step 2: Start Sensu Backend
1. To start sensu backend, download the configuration file to the directory

{% highlight raw %}
curl -L https://docs.sensu.io/sensu-go/latest/files/backend.yml -o /etc/sensu/backend.yml
{% endhighlight %}

2. Start and enable the Sensu backend service.
{% highlight raw %}
 systemctl start sensu-backend
 systemctl enable sensu-backend
{% endhighlight %}

3. Verify sensu backend service status
![sensu2][2]


## Step 3: Configure Sensu Credentials
1. You just need to set environment variables with a username and password string in this initialization phase. 

{% highlight raw %}
export SENSU_BACKEND_CLUSTER_ADMIN_USERNAME=username
export SENSU_BACKEND_CLUSTER_ADMIN_PASSWORD=password
{% endhighlight %}


Once the backend is operational, execute "sensu-backend init" to establish your Sensu admin credentials. Prior to initialization, ensure that you've added sensu-backend to your $PATH by exporting it.
{% highlight raw %}
###sensu-backend locate###
$ whereis sensu-backend
sensu-backend: /usr/sbin/sensu-backend

###export sensu-backend###
$ echo "export PATH=\$PATH:/usr/sbin" | sudo tee -a /etc/profile
export PATH=$PATH:/usr/sbin
$ source /etc/profile

###initialize sensu-backend###
$ sensu-backend init
{% endhighlight %}

## Step 4: Access Sensu Web UI
- Sensu provides a web interface designed for event and alert visualization. Confirm whether the interface is actively listening on the designated port, which is typically set to 3000 by default.


{% highlight raw %}
 $ ss -plunt |grep 3000
 tcp   LISTEN 0 ...
{% endhighlight %}

Use the command below to send a request to the backend to ensure it is up and running.
{% highlight raw %}
 $ curl http://127.0.0.1:8080/health
"Alarms":null, ...
{% endhighlight %}

Open your web browser and go to http://<your-server-ip>:3000
![sensu3][3]

Use your credentials created above to login. After Successful login you will get the following dashboard:
![sensu4][4]

## Step 5: Install Sensuctl command line utility

Sensuctl is a command-line utility for managing Sensu resources. It creates, reads, updates, and deletes resources, events, and entities by using Sensu’s HTTP API.
Sensuctl repository to the server:

{% highlight raw %}
curl -s https://packagecloud.io/install/repositories/sensu/stable/script.deb.sh | sudo bash
{% endhighlight %}

Install sensuctl by running the following command:
{% highlight raw %}
apt install sensu-go-cli
{% endhighlight %}


Run sensuctl configure and log in with your user credentials, namespace, and Sensu backend URL to begin using sensuctl. To set default values for sensuctl, follow these steps:
{% highlight raw %}
sensuctl configure -n --username username --password passord --namespace default --url 'http://127.0.0.1:8080'
{% endhighlight %}

Non-interactive mode is triggered by the -n flag in this case. To see your user profile, run **sensuctl config view**.

You can now update the admin password with the utility tool by entering the command:
{% highlight raw %}
sensuctl user change-password --interactive
{% endhighlight %}
![sensu][5]


## Step 6: Install and Configure Sensu Go Agent

Subsequently, you'll need to install the Sensu Go Agent package on every system you wish to monitor. In this demonstration, we'll install the agent on debian10. Simply run the provided command to perform the installation:

Start by Adding the Sensu repository.
{% highlight raw %}
curl -s https://packagecloud.io/install/repositories/sensu/stable/script.deb.sh | sudo bash
{% endhighlight %}


Run the following command to install Sensu Go Agent.
{% highlight raw %}
apt install sensu-go-agent
{% endhighlight %}

Once the agent is installed, run the following command to get the agent configuration file:
{% highlight raw %}
curl -L https://docs.sensu.io/sensu-go/latest/files/agent.yml -o /etc/sensu/agent.yml
{% endhighlight %}

Start Sensu agent
{% highlight raw %}
systemctl start sensu-agent
{% endhighlight %}

Confirm sensu agent status: 
![sensu][6]

Configure sensu-agent by specify the Sensu backend URL as below.

Start Sensu agent
{% highlight raw %}
vi /etc/sensu/agent.yml
name: "node1"
namespace: "default"
backend-url:
  - "ws://192.168.64.14:8081"  # (Sensu-go back-end)
{% endhighlight %}

Restart the service after saving the changes:
**systemctl restart sensu-agent**

Now, go back to the Sensu dashboard page and reload it. The agent should have been included, as you can see or you can use sensuctl to confirm the event:
{% highlight raw %}
sensuctl entity list
{% endhighlight %}
![sensu][7]


>  :metal:  :metal:  :metal:  :metal:  :metal:  :metal:  :metal:

---

> :memo: Thanks for reading.


Guneycan Sanli.

---

[1]: ../assets/images/sensu/sensu1.jpg
[2]: ../assets/images/sensu/sensu2.jpg
[3]: ../assets/images/sensu/sens3.jpg
[4]: ../assets/images/sensu/sens4.jpg
[5]: ../assets/images/sensu/sensu5.jpg
[6]: ../assets/images/sensu/sensu6.jpg
[7]: ../assets/images/sensu/sensu7.jpg
