---
title: "How to Deploy Kubernetes Applications using OpenTofu"
layout: post
date: 2024-05-15 14:20
image: ../assets/images/opentofu/opentofu-main.jpg
headerImage: true
tag:
    - k3s
    - kubernetes
    - opentofu
category: blog
author: guneycansanli
description: How to Deploy Kubernetes Applications using OpenTofu
---

# Introduction

In this guide , I will deploy kubernetes Web applications using OpenTofu.

---

# Prerequisites

1- Local kubernetes cluster or different orchestration systems, I will use k3s
2- Ubuntu or Debian server/VM
3- Installed opentofu

# Intall/Setup a local K3S

[K3s.io](https://k3s.io/) is a Lightweight Kubernetes cluster perfect for development or edge deployments. K3s is a CNCF Sandbox Project Originally developed by Rancher.

Note: You may need to disable your FW.

1- Let's use 1 line command and install k3s to our VM.

```
$ sudo curl -sfL https://get.k3s.io | INSTALL_K3S_EXEC="server --write-kubeconfig-mode=644" sh -
```

2- If You saw **[INFO] systemd: Starting k3s** , You can check cluster nodes and verify Cluster is ready.

![opentofu][1]

2- We decided our kernel version so , We need to download it to our VM , We can use wget to dowload kernel.

```
$ kubectl get nodes
```

![opentofu][2]

3- A kubeconfig file is a configuration file utilized by kubectl, the command-line interface for managing Kubernetes clusters. This file contains crucial information such as cluster details, authentication credentials, contexts, and other settings required for connecting to and communicating with a Kubernetes cluster. It outlines the API server address for the cluster, the methods of authentication (like certificates or tokens), and the default namespace for running commands. The kubeconfig file enables users to handle multiple Kubernetes clusters and seamlessly switch between them using kubectl.

4- Check the name of the Kubernetes context by requesting information about the kubeconfig file:

```
$ kubectl config view
```

![opentofu][3]

5- Now We can use kubectl for manage our cluster. In this example the Kubernetes cluster name is **default**.

# Install OpenTofu

1- Previously named OpenTF, OpenTofu is a fork of Terraform that is open-source, community-driven, and managed by the Linux Foundation.

2- Download the OpenTofu installation package using wget

```
$ wget https://github.com/opentofu/opentofu/releases/download/v1.6.0/tofu_1.6.0_amd64.deb
```

3- Install OpenTofu

```
$ sudo dpkg --install tofu_1.6.0_amd64.deb
```

4- Test OpenTofu installation

```
$ tofu version
```

![opentofu][4]

# Create an OpenTofu plan file

1- This example demonstrates the deployment of an application called "color" from the Docker repository **itwonderlab/color**. The color application modifies the background color of its web page based on the value set in the COLOR environment variable. Update the kubernetes_deployment resource configuration as needed, along with the name of the containerized application that will be deployed.

2- Also for accessing the Application from outside the Kubernetes cluster a Load Balancer or a NodePort service is needed to expose the port. Kubernetes will route service traffic to pods with label keys and values matching this selector specified, in our example the label app with a value "color" is used as the selector.

3- I will add both Load Balancer and NodePort to out plan file.

4- Here is the **color_app.tf** file.

```
# Copyright (C) 2018 - 2023 IT Wonder Lab (https://www.itwonderlab.com)
#
# This software may be modified and distributed under the terms
# of the MIT license.  See the LICENSE file for details.
# -------------------------------- WARNING --------------------------------
# IT Wonder Lab's best practices for infrastructure include modularizing
# Terraform configuration.
# In this example, we define everything in a single file.
# See other tutorials for Terraform best practices for Kubernetes deployments.
# -------------------------------- WARNING --------------------------------
terraform {
  required_version = "> 1.5"
}

#-----------------------------------------
# Default provider: Kubernetes
#-----------------------------------------
provider "kubernetes" {

  #kubeconfig file, if using K3S set the path
  config_path = "/etc/rancher/k3s/k3s.yaml"

  #Context to choose from the config file. Change if not default.
  #config_context = "local-k3s"
}

#-----------------------------------------
# KUBERNETES: Deploy App
#-----------------------------------------
resource "kubernetes_deployment" "color" {
    metadata {
        name = "color-blue-dep"
        labels = {
            app   = "color"
            color = "blue"
        } //labels
    } //metadata

    spec {
        selector {
            match_labels = {
                app   = "color"
                color = "blue"
            } //match_labels
        } //selector
        #Number of replicas
        replicas = 3
        #Template for the creation of the pod
        template {
            metadata {
                labels = {
                    app   = "color"
                    color = "blue"
                } //labels
            } //metadata
            spec {
                container {
                    image = "itwonderlab/color"   #Docker image name
                    name  = "color-blue"          #Name of the container specified as a DNS_LABEL. Each container in a pod must have a unique name (DNS_LABEL).

                    #Block of string name and value pairs to set in the container's environment
                    env {
                        name = "COLOR"
                        value = "blue"
                    } //env

                    #List of ports to expose from the container.
                    port {
                        container_port = 8080
                    }//port

                    resources {
                        requests = {
                            cpu    = "250m"
                            memory = "50Mi"
                        } //requests
                    } //resources
                } //container
            } //spec
        } //template
    } //spec
} //resource

#-------------------------------------------------
# KUBERNETES: Add a LoadBalancer
#-------------------------------------------------
resource "kubernetes_service" "color-service-lb" {
  metadata {
    name = "color-service-lb"
  } //metadata
  spec {
    selector = {
      app = "color"
    } //selector
    session_affinity = "ClientIP"
    port {
        port        = 18080
        target_port = 8080
        node_port   = 30007
    } //port
    type = "LoadBalancer"
  } //spec
} //resource


#-------------------------------------------------
# KUBERNETES: Add a NodePort
#-------------------------------------------------
resource "kubernetes_service" "color-service-np" {
  metadata {
    name = "color-service-np"
  } //metadata
  spec {
    selector = {
      app = "color"
    } //selector
    session_affinity = "ClientIP"
    port {
      port      = 8080
      node_port = 30085
    } //port
    type = "NodePort"
  } //spec
} //resource
```

5- Copy above plan and save it.

# Initialize the OpenTofu Plan and Providers

1- OpenTofu needs to download the providers used by the plan into the .terraform directory. Run tofu init to initialize.

2- Run **tofu init**

```
$ tofu init
```

![opentofu][5]

# Execute OpenTofu Plan and Apply

1- Run the OpenTofu plan command to see what resources will be created, changed, or destroyed:

```
$ tofu plan
```

2- Tofu plan command will show what is the desired state of plan, this command does not apply the plan, I will give uf summary of changes.

![opentofu][6]

3- End of the output You will see:

```
Plan: 1 to add, 0 to change, 0 to destroy.
───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
Note: You didn't use the -out option to save this plan, so OpenTofu can't guarantee to take exactly these actions if you run "tofu apply" now.
```

4- Run the OpenTofu apply command to deploy the application into Kubernetes:

```
$ tofu apply
```

![opentofu][7]

4- End of the output You will see:

```
Plan: 3 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  OpenTofu will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value:
```

5- At that point opentofu is waiting an input and We should enter **yes**.

![opentofu][8]

# Check that the Application has been deployed

1- Check the Kubernetes pods:

```
$ kubectl get pods
```

![opentofu][9]

2- Check the Kubernetes NodePort and load balancer (Using a LoadBalancer is preferred as the Load Balancer gives access to multiple Kubernetes nodes. K3s provides a load balancer known as ServiceLB (formerly Klipper LoadBalancer).

```
$ kubectl get services
```

![opentofu][10]

# Access the Application with a Web Browser

1- We can access the pods via load balancer or nodeports.

2- I will test with Loadbalancer port

```
color-service-lb   LoadBalancer   10.43.87.194   10.0.2.15     18080:30007/TCP   111m
```

3- Port forward to your VM's port 30007 and access from your local port. 

4- I forwarded my local 9090 port to VM's port 30007. 

![opentofu][11]

5- We successfuly deployed k3s pods and services. 

6- If you want to find orginal tutorial You can find it here: [https://www.itwonderlab.com/kubernetes-with-opentofu/](https://www.itwonderlab.com/kubernetes-with-opentofu/)


Thanks for reading...

---

---

---

---

> :+1: :+1: :+1: :+1: :+1: :+1: :+1: :+1:

---

Guneycan Sanli.

---

[1]: ../assets/images/opentofu/opentofu1.jpg
[2]: ../assets/images/opentofu/opentofu2.jpg
[3]: ../assets/images/opentofu/opentofu3.jpg
[4]: ../assets/images/opentofu/opentofu4.jpg
[5]: ../assets/images/opentofu/opentofu5.jpg
[6]: ../assets/images/opentofu/opentofu6.jpg
[7]: ../assets/images/opentofu/opentofu7.jpg
[8]: ../assets/images/opentofu/opentofu8.jpg
[9]: ../assets/images/opentofu/opentofu9.jpg
[10]: ../assets/images/opentofu/opentofu10.jpg
[11]: ../assets/images/opentofu/opentofu11.jpg
