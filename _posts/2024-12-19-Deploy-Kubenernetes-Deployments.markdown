---
title: "Deploy Voting App on K8s (Microservices)"
layout: post
date: 2024-12-19 14:20
image: ../assets/images/jenkins-email-noti/main.jpg
headerImage: true
tag:
    - kubernetes
    - k8s
    - docker
    - yaml
    - microservices
category: blog
author: guneycansanli
description: Deploy Voting App on K8s (Microservices)
---

# Deploy Voting App on K8s (Microservices)

### Introduction

This guide explains how to deploy a Voting App with a microservices architecture in a Kubernetes cluster. The application consists of five components: the Voting App, Worker, Redis, PostgreSQL, and Result App. Each component is containerized and communicates via Kubernetes services. This tutorial covers prerequisites, deployment steps, and service configurations for access from local.

* * *

# Prerequisites

1.  Kubernetes Cluster: A running Kubernetes cluster.
2.  kubectl: Installed and configured to manage the Kubernetes cluster.not personal SMTP server
3.  YAML Files: Deployment and Service configuration files for each component. (You can use my Public GitHub Repo)

* * *

# Summary of Components

1. Pods:
    Redis, PostgreSQL, Voting App, Worker App, Result App (5 total).

2. Services:
    Redis (ClusterIP): Internal access for Voting App and Worker App.
    PostgreSQL (ClusterIP): Internal access for Worker App and Result App.
    Voting App (NodePort): External access for users to cast votes.
    Result App (NodePort): External access for users to view results.

3. Worker App:
    No service required; it communicates directly with Redis and PostgreSQL.


![k8s-deploy][1]

# Deployment Steps

### Step 1: Deploy Redis Database

1. Create Redis Deployment:
    Deploy the Redis database using a Kubernetes Deployment YAML file.
    Ensure Redis listens on port 6379.

2. Expose Redis:
    Create a ClusterIP service named redis.
    This service allows the Voting App and Worker App to access Redis.

### Step 2: Deploy PostgreSQL Database

1. Create PostgreSQL Deployment:
    Deploy PostgreSQL with a Deployment YAML file.
    Configure it to listen on port 5432.
    Set environment variables for POSTGRES_USER and POSTGRES_PASSWORD as postgres.

2. Expose PostgreSQL:
    Create a ClusterIP service named db.
    This service enables the Worker App and Result App to connect to the database.

### Step 3: Deploy the Worker App

1. Create Worker Deployment:
    Deploy the Worker App using a Deployment YAML file.
    The Worker App processes votes from Redis and updates the PostgreSQL database.

2. No Service Needed:
    The Worker App does not require a Kubernetes service as it is not accessed by other components or users.

### Step 4: Deploy the Voting App

1. Create Voting App Deployment:
    Deploy the Voting App with a Deployment YAML file.
    Ensure it runs a Python web server on port 80.

2. Expose Voting App:
    Create a NodePort service to provide external access.
    Specify a port for external users to cast votes.

### Step 5: Deploy the Result App

1. Create Result App Deployment:
    Deploy the Result App using a Deployment YAML file.
    Ensure it runs a web server on port 80 to display results.

2. Expose Result App:
    Create a NodePort service for external access.
    Specify a port for users to view results.


* * *

# Step 3: Deployments and Services YAML Files 

You can use my public repo's yaml files to create deployments and services.

-
[URL of My GitHub Repo for K8s yamls](https://github.com/guneycansanli/k8s-training)
-

![k8s-deploy][2]


Once You pull repo , You will have yaml files , You do not need to use pod yamls , I have already converted them to deployment yamls so , You can create deployments and also regarding services. 

1. Create **Redis** deployment and service 
        ```
            kubectl create -f redis-deployment.yaml
            kubectl create -f pod-service-yamls/redis-service.yaml
        ```

2. Create **Postgres DB** deployment and service 
        ```
            kubectl create -f deployment-yamls/postgres-deployment.yaml 
            kubectl create -f pod-service-yamls/postgres-service.yaml
        ```

3. Create **Worker App** deployment, only deployment because worker app no need to listen or expose any ports
        ```
            kubectl create -f deployment-yamls/worker-app-deployment.yaml
        ```

4. Create **Voting App** deployment and service 
        ```
            kubectl create -f deployment-yamls/result-app-deployment.yaml  
            kubectl create -f pod-service-yamls/voting-app-service.yaml
        ```

5. Create **Results App** deployment and service 
        ```
            kubectl create -f deployment-yamls/voting-app-deployment.yaml  
            kubectl create -f pod-service-yamls/result-app-service.yaml 
        ```


Note: Since We are using deployments We can edit replicas of pods , I prefered to increase **voting app** replicas to 3, You can edit your replicas after you create deployment but in that case You need to run kubectl appy command like below:
 ```
        kubectl apply -f deployment-yamls/voting-app-deployment.yaml 
 ```

* * *

# Step 4: Checking Cluster Resources

We have created all deployments and service now We can check What we have cluster , Depends on your choise You can check resources seperatly , I check all resources with **kubectl**
 ```
        kubectl get all
 ```

![k8s-deploy][3]

1. We have 2 service are using **NodePort** so that means We can access these Pods from our local.
2. Note: **ClusterIp** is default service for communication between Pods/Containers
3. If you have K8s cluster in VM or/in diffrent network , You may need to do **Port Forwarding**
4. **Port Forward** to K8s host/s
 ```
       ssh -L 9000:10.101.202.69:80 guney@192.168.1.159(k8s worker or master) 
       ssh -L 9001:10.99.113.120:80 guney@192.168.1.159(k8s worker or master) 
 ```

5. You can vote animals fro voting app and check result app. 

![k8s-deploy][4]

![k8s-deploy][5]

# Conclusion

Today, we explored the process of creating Kubernetes (K8s) deployments and services in the context of microservices. Kubernetes offers a wide variety of resources and services, each playing a unique role in managing containerized applications. As part of our learning experience, we focused on collecting and organizing these components into a single file for easier management and understanding.

This article is aimed at beginners looking to understand how Kubernetes works, specifically in the deployment and orchestration of microservices. We will guide you through the various Kubernetes resources, their functions, and how they work together to support scalable, reliable applications. 

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

[1]: ../assets/images/k8s-deploy/k8s-deploy-1.jpg
[2]: ../assets/images/k8s-deploy/k8s-deploy-2.jpg
[3]: ../assets/images/k8s-deploy/k8s-deploy-3.jpg
[4]: ../assets/images/k8s-deploy/k8s-deploy-4.jpg
[5]: ../assets/images/k8s-deploy/k8s-deploy-5.jpg


