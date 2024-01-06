---
title: "How to Install Kubernetes on Debian"
layout: post
date: 2024-01-06 14:20
image: ../assets/images/k8s/main.png
headerImage: true
tag:
    - linux
    - debian
    - k8s
category: blog
author: guneycansanli
description: How to Install Kubernetes on Debian
---

# Before Installation of K8s?

- We will install K8s to Debian nodes. The lab will include 1 master and 1 worker node, We will use Debian 10 Buster. If you want to use different distros probably It works for nmost of Debian or Ubuntu Versions.
- Note: The recommendation is using at least 2 CPU for master node.

# K8s Installation

- You may need to edit your 2 machines hostnames. master-node and worker-node (slave)
- You need to follow up the steps for both master and slave nodes.

:one: Install Containerd Run time on All Nodes
- Containerd is the industry standard container run time and supported by Kubernetes. So, install containerd on all master and worker nodes.
- Before installing containerd, set the following kernel parameters on all the nodes.
{% highlight raw %}
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf 
overlay 
br_netfilter
EOF

modprobe overlay 
modprobe br_netfilter

cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-k8s.conf
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1 
net.bridge.bridge-nf-call-ip6tables = 1 
EOF
{% endhighlight %}

- To make above changes into the effect, run
{% highlight raw %}
sysctl --system
{% endhighlight %}
![k8s][1]

:two: We need  to install containerd and settingup contianerd for k8s.
{% highlight raw %}
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo apt-key add -
echo "deb [arch=amd64] https://download.docker.com/linux/debian buster stable" |sudo tee /etc/apt/sources.list.d/docker.list
apt update
apt install containerd
{% endhighlight %}

- Next, configure containerd so that it works with Kubernetes, run beneath command on all the nodes.
{% highlight raw %}
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
{% endhighlight %}

- Set cgroupdriver to systemd on all the nodes,
- Edit the file ‘/etc/containerd/config.toml’ and look for the section ‘[plugins.”io.containerd.grpc.v1.cri”.containerd.runtimes.runc.options]’ and change ‘SystemdCgroup = false’ to ‘SystemdCgroup = true‘
{% highlight raw %}
vi /etc/containerd/config.toml
{% endhighlight %}
- Save and exit
![k8s][6]

- Restart and enable containerd service on all the nodes,
{% highlight raw %}
systemctl restart containerd
systemctl enable containerd
{% endhighlight %}
![k8s][7]

:three: Install curl for both nodes.
{% highlight raw %}
apt install curl -y
{% endhighlight %}

:four: Add the Kubernetes signing key on both the nodes.
{% highlight raw %}
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add
{% endhighlight %}

- I f you get any error regarding to gnupg, gnupg2 and gnupg1 probably You need to install one of it ex. apt-get install -y gnupg2
![k8s][2]

:five: Add Kubernetes Repository on both the nodes.
- We will be using Xenial Kubernetes Repository
{% highlight raw %}
apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
{% endhighlight %}
![k8s][3]
- Update apt afte radding new repo
{% highlight raw %}
apt update
{% endhighlight %}

:six: Install Kubeadm and some k8s tools on both the nodes.
- This is the final step for installation. Installing kubeadm on both the nodes 
{% highlight raw %}
apt update
apt install kubelet kubeadm kubectl -y
apt-mark hold kubelet kubeadm kubectl
{% endhighlight %}
![k8s][4]

- We successfully installed K8s and We can deploy K8s cluster now

# Install Kubernetes Cluster with Kubeadm

- Now, we are all set to initialize Kubernetes cluster, run following command only from master node,

:one: We need to disable SWAP memory (SWAP RAM), on both the nodes as Kubernetes does not perform properly on systems that is using swap memory.
{% highlight raw %}
swapoff -a
{% endhighlight %}
- You may need to comment out fstab entry for SWAP (For permanently disable SWAP) :+1: :+1: :+1:
![k8s][5]

:two: Before Initialize K8s I would like to change hostname of VMs for better understanding
{% highlight raw %}
hostnamectl set-hostname "k8s-master.local"      // Run on master node
hostnamectl set-hostname "k8s-worker01.local"    // Run on 1st worker node
{% endhighlight %}

{% highlight raw %}
-IP-  k8s-master.local     k8s-master
-IP-  k8s-worker01.local   k8s-worker01
{% endhighlight %}
![k8s][9]

:three: Initialize K8s in Master-node i.e master-node
- Run the following command only in master-node
{% highlight raw %}
kubeadm init --control-plane-endpoint=k8s-master
{% endhighlight %}
![k8s][10]
- Above output confirms that control plane has been initialized successfully. In the output, we have commands for regular user for interacting with the cluster and also the command to join any worker node to this cluster.

- To start interacting with cluster, run following commands on master node,
{% highlight raw %}
$ mkdir -p $HOME/.kube
$ sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$ sudo chown $(id -u):$(id -g) $HOME/.kube/config
{% endhighlight %}

- And It gives you another command for join any worker to the cluster.
{% highlight raw %}
kubeadm join k8s-master:6443 --token r6yvos.wqdlt5dkmrw2kx0f \
        --discovery-token-ca-cert-hash sha256:1fad2cd211a21a7a49fb569518ace419c4aa292790d0754c5677c67acd552116 
{% endhighlight %}

- Run following kubectl command to get nodes and cluster information,
{% highlight raw %}
$ kubectl get nodes
$ kubectl cluster-info
{% endhighlight %}
![k8s][11]
:boom: :boom: :boom: :boom: :boom: :boom: :boom: 

- On your worker nodes, join them to the cluster by running the command that was displayed when you initialized the master node. It will look something like this ‘Kubeadm join’

- Note: Copy worker node join command from output and run it in worker(slave) node.
![k8s][12]

:four: Check the nodes status by running following command from master node,
{% highlight raw %}
kubectl get nodes
{% endhighlight %}
![k8s][13]

- To make nodes status ready, we must install POD network addons like Calico or flannel.

:five: Setup Pod Network Using Calico
- On the master node, run beneath command to install calico,
{% highlight raw %}
kubectl apply -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/calico.yaml
{% endhighlight %}

- and verify the status of Calico pods, run
{% highlight raw %}
kubectl get pods -n kube-system
{% endhighlight %}
![k8s][14]

- After waiting couple minutes all pods will be ready and If you check nodes , It will show you all nodes are ready state.
{% highlight raw %}
kubectl get nodes
{% endhighlight %}
![k8s][15]


:arrow_forward: :arrow_forward: :arrow_forward: You are ready for deploy applications. Enjoy with your Kubernetes Cluster 


---
---

> :blush: :star: :boom: :fire: :+1: :eyes: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/k8s/k8s1.jpg
[2]: ../assets/images/k8s/k8s2.jpg
[3]: ../assets/images/k8s/k8s3.jpg
[4]: ../assets/images/k8s/k8s4-1.jpg
[5]: ../assets/images/k8s/k8s5.jpg
[6]: ../assets/images/k8s/k8s-1-2.jpg
[7]: ../assets/images/k8s/k8s-1-3.jpg
[8]: ../assets/images/k8s/k8s-1-2.jpg
[9]: ../assets/images/k8s/k8s5-1.jpg
[10]: ../assets/images/k8s/k8s6.jpg
[11]: ../assets/images/k8s/k8s7.jpg
[12]: ../assets/images/k8s/k8s8.jpg
[13]: ../assets/images/k8s/k8s9.jpg
[14]: ../assets/images/k8s/k8s10.jpg
[15]: ../assets/images/k8s/k8s11.jpg
