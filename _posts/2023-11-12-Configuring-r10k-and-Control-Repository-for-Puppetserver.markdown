---
title: "Configuring r10k and Control Repository for Puppetserver"
layout: post
date: 2023-11-12 14:20
image: ../assets/images/puppet/puppet-r10k/main.jpg
headerImage: true
tag:
    - linux
    - debian
    - puppet
category: blog
author: guneycansanli
description: How to Install and Setup Puppet Server-Agent in Debian 10(Buster)
---

# What is r10k?

- In Puppet, we have a code management tool known as r10k that helps in managing environment configurations related to different kind of environments that we can configure in Puppet such as development, testing, and production. This helps in storing environment-related configuration in the source code repository.
- We can manage our control repository and different environments with different branches. r10k helps us to deploy our code to puppetserver using with remote repository in this article I am going to use github and configuration can be SSH or https. I ahve already a public repository in github.

# Steps

- Install and configure r10k to Puppetserver.
- Create a new repository and pushing the code.
- Pulling the code to Pupperserver via r10k.
- Running agent.

# Install and configure r10k to Puppetserver

- Before installing r10k You may aware of ruby and gem because , It may cause couple of problems I won't give details ruby and gem but If you have issue, You may search ruby and gem versions or dependencies for r10k.
- Install r10k using Puppet embeded ruby gem.
- In my case my versions like below
- You can find available versions in https://rubygems.org/gems/r10k/versions/ 
{% highlight raw %}
root@puppet-master:~/playground# /opt/puppetlabs/puppet/bin/ruby -v
ruby 2.5.9p229 (2021-04-05 revision 67939) [x86_64-linux]
root@puppet-master:~/playground# /opt/puppetlabs/puppet/bin/gem -v
2.7.6.3
{% endhighlight %}

{% highlight raw %}
/opt/puppetlabs/puppet/bin/gem install --no-rdoc r10k -v 3.0.0
{% endhighlight %}
![r10k][1]

- Verify r10k installed.
![r10k][2]

# Create a new repository and pushing the code 

- We need to have a control repo for Puppet code.
- I will use Github public repo.
- Create a new repo.
![r10k][3]

- If you have a repository now We need to configure r10k configuration in Puppetserver.
- Create r10k folder and r10k.yaml file under /etc/puppetlabs/r10k
{% highlight raw %}
mkdir /etc/puppetlabs/r10k
{% endhighlight %}
![r10k][4]

- r10k.yaml should have cachedir and source. r10k pulls the code using with this configuration.
- basedir is actual puppetserver code
- With this configuration your all branches will be define puppet environments.
- vi /etc/puppetlabs/r10k/r10k.yaml
{% highlight raw %}
 ---
 :cachedir: '/var/cache/r10k'

 :sources:
         :itvraag:
                 remote: 'https://github.com/guneycansanli/puppet_control_repo.git'
                 basedir: '/etc/puppetlabs/code/environments'
{% endhighlight %}
![r10k][5]

- You can find example cotrol repos in GitHub or You may use my control repo https://github.com/guneycansanli/puppet_control_repo.git
- There are different type of Puppet code structure but using roles and profiles are more flexible for your development.
- After you pull or copy the code. You can add Puppet mods to Puppet file and You need to intall modeules to your control repo with r10k puppetfile install.
![r10k][6]

- You need to install r10k to your development environment for using this feature.
- I use my local env and I have already installed r10k to MAC M1 
![r10k][7]

- You can push the code after install the Puppetfile.
- You should change the default brach name to Production , Puppet uses production environment default otherwise you need to specified the --environmet while running Puppet agent.
![r10k][8]


# Pulling the code to Pupperserver via r10k  

- Now our control repo is ready to use, before run Puppet agent We need pull the code in Puppetserver because Puppet agent uses Puppetserver code
- We need to run r10k deploy command in Puppetserver.
{% highlight raw %}
/opt/puppetlabs/puppet/bin/r10k deploy environment -pv debug 
{% endhighlight %}
![r10k][9]

- You can check the code after r10k deploy enviroment.
![r10k][10]


# Running agent

- The last step is running the puppetagent. 
- I used same code in main and production branch so does not matter which brach I used.
{% highlight raw %}
puppet agent -t --environment=main
puppet agent -t --environment=production
puppet agent -t --environment
{% endhighlight %}
![r10k][11]

- I had apache2 profile and role, after puppet agent ran succesfull and verified that my agent has apache2 server.
![r10k][12]

---
---

> :metal: :metal: :metal: :metal: :metal: :metal: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/puppet/puppet-r10k/puppet-r10k-3.jpg
[2]: ../assets/images/puppet/puppet-r10k/puppet-r10k-4.jpg
[3]: ../assets/images/puppet/puppet-r10k/puppet-r10k-1.jpg
[4]: ../assets/images/puppet/puppet-r10k/puppet-r10k-2.jpg
[5]: ../assets/images/puppet/puppet-r10k/puppet-r10k-8.jpg
[6]: ../assets/images/puppet/puppet-r10k/puppet-r10k-9.jpg
[7]: ../assets/images/puppet/puppet-r10k/puppet-r10k-10.jpg
[8]: ../assets/images/puppet/puppet-r10k/puppet-r10k-11.jpg
[9]: ../assets/images/puppet/puppet-r10k/puppet-r10k-12.jpg
[10]: ../assets/images/puppet/puppet-r10k/puppet-r10k-13.jpg
[11]: ../assets/images/puppet/puppet-r10k/puppet-r10k-14.jpg
[12]: ../assets/images/puppet/puppet-r10k/puppet-r10k-15.jpg