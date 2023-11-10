---
title: "How to Install and Setup Puppet Server-Agent in Debian 10(Buster)"
layout: post
date: 2023-11-07 14:20
image: ../assets/images/puppet/puppet-main.png
headerImage: true
tag:
    - linux
    - debian
    - puppet
category: blog
author: guneycansanli
description: How to Install and Setup Puppet Server-Agent in Debian 10(Buster)
---

# Introduction?

-  In this article, I am going to explain how to install and configure Puppet server and puppet agent in Debian 10(Buster), I have used Puppet 6 for this project so If you use different linux distros or different Puppet version, You may use different installation steps

# Steps

- Install and configure Puppet Agent
- Install and configure Puppet Server
- Puppet Agent - Server CA Sign  
- Config a basic manifest and configure Nginx server on agent

# Install Puppet Agent

- While agent has no need any additional configuration We can install and run puppet agent service.
- You may add hosts name in your /etc/hosts file 
- I will use puppet-agent.local hostname for agent node
- I will use puppet-master.local for master node
- Add the puppet repo to agent, install puppet-agent and start puppet service.
{% highlight raw %}
wget https://apt.puppet.com/puppet6-release-buster.deb
dpkg -i puppet6-release-buster.deb
apt update
apt install puppet-agent
systemctl start puppet
systemctl enable puppet
{% endhighlight %}

![puppet][1]

- Config the puppet server name for puppet agent 
{% highlight raw %}
/opt/puppetlabs/bin/puppet config set server 'puppet-master.local' --section main
{% endhighlight %}

# Install Puppet Server

- Add the puppet repo to master node, install puppet server and start puppet server service.
{% highlight raw %}
wget https://apt.puppet.com/puppet6-release-buster.deb
dpkg -i puppet6-release-buster.deb
apt update
apt install puppetserver
systemctl start puppetserver
systemctl enable puppetserver
{% endhighlight %}

- If puppet server can not run, You may need to change puppet server configuration
- You may need to decrease -Xms and -Xmx variable , -Xmx option and -Xms option in combination are used to limit the Java heap size.
![puppet][2]

- Make sure puppet server is up and running
![puppet][3]


# Puppet Agent - Server CA Sign 

- Puppet communicates with using CA
- We need request CA from agent and sign CA in master node
- If you run puppet agent command in agent node You will see an error that shows , Puppet server should sign CA
- Run /opt/puppetlabs/bin/puppet agent -t
- This command will output an error, stating that no certificate has been found. This error is because the generated certificate needs to be approved by the Puppet master.
- After you run command , You can go back puppet master and list CA
{% highlight raw %}
/opt/puppetlabs/bin/puppetserver ca list
{% endhighlight %}
![puppet][4]

- And sign CA with node name
{% highlight raw %}
/opt/puppetlabs/bin/puppetserver ca sign --certname puppet-agent.local
{% endhighlight %}
![puppet][5]

- And sign CA with node name
{% highlight raw %}
/opt/puppetlabs/bin/puppetserver ca sign --certname puppet-agent.local
{% endhighlight %}


- Return to the Puppet agent nodes and run the Puppet agent again:
{% highlight raw %}
/opt/puppetlabs/bin/puppetserver ca list
{% endhighlight %}
- You should see the agent could connected to the server.

---

# Config a basic manifest and configure Nginx server on agent

- Install apt and nginx module on the server:
{% highlight raw %}
puppet module install puppet-nginx
puppet module install puppetlabs-apt
{% endhighlight %}

- Create file site.pp in folder /etc/puppetlabs/code/environments/production/manifests
{% highlight raw %}
vi /etc/puppetlabs/code/environments/production/manifests/site.pp
{% endhighlight %}

- Your manifest should include node name
![puppet][6]


- In agent run the puppet agent command to install nginx
{% highlight raw %}
/opt/puppetlabs/bin/puppet agent -t
{% endhighlight %}
![puppet][7]

- Finally tou can check if ngingx installed succusfully
![puppet][8]

---

> :metal: :metal: :metal: :metal: :metal: :metal: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/puppet/puppet-agent-1.jpg
[2]: ../assets/images/puppet/puppet-master-2.jpg
[3]: ../assets/images/puppet/puppet-master-srv.jpg
[4]: ../assets/images/puppet/puppet-master-ca.jpg
[5]: ../assets/images/puppet/puppet-master-ca-sign.jpg
[6]: ../assets/images/puppet/puppet-master-manifest.jpg
[7]: ../assets/images/puppet/puppet-agent-run.jpg
[8]: ../assets/images/puppet/puppet-agent-run-final.jpg