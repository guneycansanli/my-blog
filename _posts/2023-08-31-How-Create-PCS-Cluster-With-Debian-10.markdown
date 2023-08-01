---
title: "How to Install and Configure Cluster with Two Nodes With PCS Debian 10"
layout: post
date: 2023-08-31 14:20
image: ../assets/images/pcs/pcsMain.jpg
headerImage: true
tag:
    - VM
    - linux
    - Debian
    - Virtualization
    - pcs
    - HA
    - Cluster
category: blog
author: guneycansanli
description: How to Install and Configure Cluster with Two Nodes With PCS Debian 10
---

# Why HA?
High-availability software is used to operate high-availability clusters. In a high-availability IT system, there are different layers (physical, data link, network, transport, session, presentation, and application) that have different software needs. If a server in the cluster fails, another server or node can take over immediately to help ensure the application or service supported by the cluster remains operational. Using high-availability clusters helps ensure there is no single point of failure for critical IT and reduces or eliminates downtime. HA is a critical feature for your servers. Using HA Clusters should be good searced there are many diffrent HA structure (Data, Traffic, Load balancing, A/A, A/P ...)

## Prerequisites
Today I will try to tell creating HA Cluster with Pacemaker and Corosync with 2 Debian 10 VM. We need to have 2 different Linux VM for creating a cluster. Be aware that necessary security rules (Firewall, ssh and some ports *depends of your Linux distro*) and networking(2 dynamic or static IP) should be done.

##  Install Pacemaker and Corosync
1. The first step is to install PCS, Pacemaker and Corosync on both nodes. To do this, open terminal and run following command −

{% highlight raw %}
sudo apt install pcs corosync pacemaker
{% endhighlight %}

---

## Configure Cluster
The next step is to configure cluster. We will start by configuring Corosync cluster messaging system. To do this, open file /etc/corosync/corosync.conf in your preferred text editor and add following lines −


{% highlight raw %}
totem {
   version: 2
   secauth: off
   cluster_name: mycluster
   transport: udpu
}

nodelist {
   node {
      ring0_addr: 192.168.64.13
      name: debian1
   }

   node {
      ring0_addr: 192.168.64.14
      name: debian2
   }
}
{% endhighlight %}


- IP addresses depend on your VM's IP *ring0_addr: 192.168.64.13, ring0_addr: 192.168.64.14* 
- Hostnames can be different for your VMs.
- There are different type Cluster configs.

## Start Corosync Service 
1. You should start the service 

{% highlight raw %}
sudo systemctl start corosync
{% endhighlight %}
![coro][1]

## Configure Pacemaker Cluster Resource Manager
The next step is to configure Pacemaker cluster resource manager. To do this, open file /etc/pacemaker/pcs.conf in your preferred text editor and add following lines


{% highlight raw %}
pcs cluster setup --name mycluster <hostname of node 1> <hostname of node 2>
pcs cluster enable --all
{% endhighlight %}
![pcs][2]

Replace <hostname of node 1> and <hostname of node 2> with hostnames of two nodes.

## Configure Cluster Resources

The final step is to configure cluster resources. We need start by configuring a virtual IP (Floating IP) for HA. Floating IPs (now named Reserved IPs). A Floating IP is an IP address that can be instantly moved from one Droplet to another Droplet in the same datacenter.

{% highlight raw %}
pcs resource create virtual_ip ocf:heartbeat:IPaddr2 ip=<IP address of virtual IP> cidr_netmask=24 op monitor interval=30s
{% endhighlight %}

- You can create a IP Which has same sub-net as your VMs
- You can verify the Virtual IP with: 
{% highlight raw %}
sudo pcs config
{% endhighlight %}
![pcs2][3]

- We can add a Apache resources for testing the web server.
- Configure a simple Apache web server that will be used to test cluster. To do this, run following command
{% highlight raw %}
pcs resource create apache ocf:heartbeat:apache
{% endhighlight %}

- Apache2 should be installed before it.
- This command will create a cluster resource for Apache web server. You can now access web server by navigating to virtual IP address in your web browser.


## Verify Cluster Status

- You can verify and see the cluster status with : 'pcs status' command.
- If you got error restart the services for both nodes.

![pcs3][4]

- I ran the command in the Debian2 node and You can see Debian1 is active now and all traffic and resources running on the Debian1. 

- Right now I can reach Debian1's Web server 
![pcs4][5]

- I will disable the Debian1's Pacemaker service and I will try to connect Web server again.
{% highlight raw %}
sudo systemctl stop pacemaker
{% endhighlight %}


![pcs5][6]
- Here is the Debian2 hosts all resources
- If you hit the VirtualIP you can get reply from it.

>  :metal:  :metal:  :metal:  :metal:  :metal:  :metal:  :metal:

---

> :bulb: Never stop learning, beacuse life never stops teaching.

> :memo: Thanks for reading.


Guneycan Sanli.

---

[1]: ../assets/images/pcs/coro.jpg
[2]: ../assets/images/pcs/pcs.jpg
[3]: ../assets/images/pcs/pcs2.jpg
[4]: ../assets/images/pcs/pcs3.jpg
[5]: ../assets/images/pcs/pcs4.jpg
[6]: ../assets/images/pcs/pcs5.jpg
