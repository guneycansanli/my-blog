---
title: "Setting up EFK Stack on Kubernetes"
layout: post
date: 2025-01-18 12:20
image: ../assets/images/argo-rollouts/argo-main.jpg
headerImage: true
tag:
    - argo
    - k8s
category: blog
author: guneycansanli
description: Setting up EFK Stack on Kubernetes
---

# Setting Up and Using Argo Rollouts on Kubernetes

### Introduction

Argo Rollouts is an open-source tool for Kubernetes designed to enable advanced deployment strategies like blue-green and canary   deployments. While Kubernetes offers native rolling update mechanisms, they have limitations such as restricted control over rollout speed and traffic distribution. Argo Rollouts overcomes these constraints, giving you greater flexibility and control over deployments.

In this article, we’ll cover the setup of Argo Rollouts and demonstrate how to use it for deploying applications.

---

## Workflow of Argo Rollouts

Argo Rollouts integrates with tools like **Ingress Controllers**, **Argo CD** for visualization, and **Prometheus** for monitoring. Below is an explanation of its workflow:

1. An application is already running in the cluster.
2. To initiate a rollout, update the image or configuration in the manifest file.
3. Argo Rollouts deploys the updated version alongside the existing version.
4. Traffic is gradually shifted from the old to the new version based on the strategy defined in the manifest.
5. The Ingress Controller manages traffic routing, while Prometheus collects metrics during the rollout process.

---

## Installing Argo Rollouts

### Prerequisites
- A functional Kubernetes cluster.
- `kubectl` configured to interact with the cluster.

### Installation Steps

To keep Argo Rollouts isolated, create a dedicated namespace:
```bash
kubectl create namespace argo-rollouts
```


### Step 2: Install Argo Rollouts

- Run the following command to install Argo Rollouts in the namespace:
```bash
kubectl apply -n argo-rollouts -f https://raw.githubusercontent.com/argoproj/argo-rollouts/stable/manifests/install.yaml
```

- This command installs the latest stable version of Argo Rollouts in the argo-rollouts namespace

![argo][1]


### Step 3: Install the Argo Rollouts Plugin (Linux-amd64) for kubectl


- Download the plugin
```bash
curl -LO https://github.com/argoproj/argo-rollouts/releases/latest/download/kubectl-argo-rollouts-linux-amd64
```

- Make it executable
```bash
chmod +x ./kubectl-argo-rollouts-linux-amd64
```

- Move the binary to your PATH
```bash
sudo mv ./kubectl-argo-rollouts-linux-amd64 /usr/local/bin/kubectl-argo-rollouts
```

- Verify the installation
```bash
kubectl argo rollouts version
```

![argo][2]


---

## Deploying Applications with Argo Rollouts

### Step 1: Deploy the Initial Application

1. Create the Manifest File
2. Create a file named nginx-rollouts.yaml with the following content:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Rollout
metadata:
  name: nginx-rollout
  namespace: default
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx-container
        image: nginx:1.24-alpine
        ports:
        - containerPort: 80
  strategy:
    canary:
      steps:
      - setWeight: 20
      - pause: {duration: 10}
      - setWeight: 50
      - pause: {duration: 10}
      - setWeight: 70
      - pause: {duration: 10}
      - setWeight: 100
```

- This deploys an older version (nginx:1.24-alpine) with 3 replicas. The Canary strategy distributes traffic incrementally, starting at 20% for the new version.

3. Apply the Manifest File
```bash
kubectl apply -f nginx-rollouts.yaml
```

4. Monitor the Rollout
```bash
kubectl argo rollouts get rollout nginx-rollout --watch
```

![argo][3]

### Step 2: Roll Out a New Version

1. Update the Manifest File, Edit the **nginx-rollouts.yaml** file to update the nginx container image to the latest version:
```yaml
image: nginx:latest
```

2. Apply the Updated Manifest File
```bash
kubectl apply -f nginx-rollouts.yaml
```

3. Monitor the Rollout
```bash
kubectl argo rollouts get rollout nginx-rollout --watch
```

- Traffic gradually shifts in the steps defined (20%, 50%, 70%, and finally 100%).

![argo][4]

- Additionally You do not have to wait until new verison's deployment You can Promote or Abort
- Promote Immediately: If the new version is stable and ready
```bash
kubectl argo rollouts promote nginx-rollout
```

- Abort Rollout: If issues arise with the new version:
```bash
kubectl argo rollouts abort nginx-rollout
```

---

## Using the Argo Rollouts Dashboard

- Arge Rollout also has a simple GUI that you can follow up deployments and check details.
- You can run below command and lunch GUI
```bash
kubectl argo rollouts dashboard
```

- Open the dashboard at http://localhost:3100 to inspect the rollout process in detail. 
- If you cluster is in remote host You need to open tunnel 

![argo][5]


![argo][6]

- You can click and check rollout's details.

![argo][7]


---


## Conclusion

By following these steps, you can install Argo Rollouts, set up the kubectl plugin, and deploy applications using advanced strategies like Canary. With features like traffic control and monitoring, Argo Rollouts ensures reliable and flexible application deployments.


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

[1]: ../assets/images/argo-rollouts/argo-1.jpg
[2]: ../assets/images/argo-rollouts/argo-2.jpg
[3]: ../assets/images/argo-rollouts/argo-3.jpg
[4]: ../assets/images/argo-rollouts/argo-4.jpg
[5]: ../assets/images/argo-rollouts/argo-5.jpg
[6]: ../assets/images/argo-rollouts/argo-6.jpg
[7]: ../assets/images/argo-rollouts/argo-7.jpg


