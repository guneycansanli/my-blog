---
title: "Setting up Cloudflare Tunnel with Traefik Reverse Proxy and Docker"
layout: post
date: 2025-05-12 11:00
image: ../assets/images/traefik/main.jpg
headerImage: true
tag:
    - traefik
    - reverse-proxy
    - docker
category: blog
author: guneycansanli
description: Step-by-Step Guide Setting up Cloudflare Tunnel with Traefik Reverse Proxy and Docker
---

## Overview

This guide will show you how to set up a Cloudflare Tunnel to securely route traffic to your services, using **Traefik** as the reverse proxy and **Docker** for containerization. We'll walk through the following steps:

1. **Setting up Cloudflare Tunnel (formerly Argo Tunnel)**
2. **Configuring Traefik as a Reverse Proxy**
3. **Deploying Services like `whoami and static web site` with Traefik Labels**
4. **Setting Up Cloudflare DNS and Handling TLS**


![traefik][1]

---

### What is Cloudflare Tunnel?
Cloudflare Tunnel (formerly known as Argo Tunnel) is a secure method to expose local servers or services to the internet without exposing your network directly. It tunnels the traffic to Cloudflare's network, handling traffic encryption, and leaving the security to Cloudflare.

### What is Traefik?
**Traefik** is a modern reverse proxy and load balancer that integrates well with Docker and Kubernetes. Traefik automatically detects services running inside Docker containers and can configure itself using labels on the containers.

## Prerequisites

Before starting, you should have the following:

1. **Cloudflare Account**: You need an active Cloudflare account with a domain added.
2. **Docker**: Ensure Docker is installed on your machine.
3. **Docker Compose**: We will use Docker Compose for easy management of containers.
4. **Cloudflare Tunnel Token**: You need a Cloudflare Tunnel (Argo Tunnel) token for authentication.

---

## Step 1: Set Up Cloudflare Tunnel

### 1.1 Cloudflare Tunnel (`cloudflared`)

1. Create a Docker container to run the Cloudflare Tunnel.

2. Generate a Cloudflare Tunnel token from the Cloudflare dashboard and use it in the Docker container environment.

```yml
version: "3.9"
services:
  tunnel:
    container_name: cloudflared-tunnel
    image: cloudflare/cloudflared
    restart: unless-stopped
    command: tunnel run
    environment:
      - TUNNEL_TOKEN=<YOUR-TOKEN>
    networks:
      - cftunnel-transport
```


### 1.2. Tunnel to Cloudflare


```bash
sudo docker-compose up -d tunnel
```

### 1.3. Adding Public Hostname to Tunnel

Once You saw Cloudflare tunnel shows **Healthy** now Your tunnel can access your docker network **cftunnel-transport**

Your target is reaching to **Traefik** reverse proxy docker container so We will use Container name sinc Traefik places in same network We will no need to any ip. 


![traefik][3]


![traefik][5]

### 1.4. Creating Origin Server TLS 

Since Trafik will handle HTTPS in our network We need to create TLS certificate for it , You can create this in Cloudflare (encryption between Cloudflare and your origin server)

Even though Cloudflare terminates TLS at their edge, by default, the connection between Cloudflare and your origin (Traefik) can be unencrypted, unless you explicitly secure it. Using a Cloudflare Origin TLS certificate ensures that the traffic remains encrypted all the way from the client to your server.

Since We're using a Cloudflare Tunnel (cloudflared), the tunnel connects from your origin (Traefik) to Cloudflare. With an Origin Certificate on Traefik:

- Cloudflare can enforce that only valid, trusted TLS connections are accepted.
- You prevent accidental exposure of HTTP on the local network or through misconfigurations.

Note: Copy the key and cert to save in your Traefik volume.

![traefik][4]


## Step 2: Set Up Traefik Reverse Proxy

### 2.1. Traefik Configuration
Traefik will be used to handle incoming traffic and route it to appropriate containers based on labels. The first step is configuring Traefik.

Create a traefik.yaml configuration file for Traefik:

```yml
api:
  insecure: true

entryPoints:
  web:
    address: ":80"
  websecure:
    address: ":443"

providers:
  docker:
    exposedByDefault: false
```

### 2.2. Docker Compose for Traefik
Create a docker-compose.yml file for Traefik:

```yml
version: "3.9"
services:
  traefik:
    image: traefik:2.9
    container_name: traefik
    restart: always
    networks:
      - cftunnel-transport
      - cloudflaretunnel
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./traefik.yaml:/traefik.yaml:ro
      - ./certificates.yaml:/certificates.yaml:ro
      - ./origin-certificates/:/origin-certificates:ro
networks:
  cftunnel-transport:
  cloudflaretunnel:
    external: true
```

### 2.3. Start Traefik
Run the following command to start Traefik:

```bash
docker-compose up -d traefik
```

## Step 3: CF Tunnel and Traefik Dokcer Compose (Optinal)

You can use CF tunnel and reverse proxy in same docker compose yml file.


```yml
version: "3.9"
services:
  tunnel:
    container_name: cloudflared-tunnel
    image: cloudflare/cloudflared
    restart: unless-stopped
    command: tunnel run
    environment:
      - TUNNEL_TOKEN=<YOUR-TOKEN>
    networks:
      - cftunnel-transport

  traefik:
    image: traefik:2.9
    container_name: traefik
    restart: always
    networks:
      - cftunnel-transport
      - cloudflaretunnel
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./traefik.yaml:/traefik.yaml:ro
      - ./certificates.yaml:/certificates.yaml:ro
      - ./origin-certificates/:/origin-certificates:ro

networks:
  cftunnel-transport:
  cloudflaretunnel:
    external: true
```

![traefik][2]


certificates.yaml :

```yaml 
tls:
  stores:
    default:
      defaultCertificate:
        certFile: /origin-certificates/guneycansanli.com.pem
        keyFile: /origin-certificates/guneycansanli.com.key

  certificates:
    - certFile: /origin-certificates/guneycansanli.com.pem
      keyFile: /origin-certificates/guneycansanli.com.key
```


Note: origin-certificates is your TLS certicates created in CloudFlare


![traefik][11]

## Step 3: Set Up Your Service with Traefik Labels

### 3.1. Traefik Routing Labels

Next, we’ll configure the whoami service as a simple service to demonstrate routing.
Create a whoami-compose.yaml file for the whoami service:

```yml
version: '3'

services:
  vaultwarden:
    image: traefik/whoami
    container_name: whoami
    restart: always
    labels:
      - traefik.enable=true
      - traefik.http.routers.whoami.rule=Host(`whoami.guneycansanli.com`)
      - traefik.http.routers.whoami.entrypoints=websecure
      - traefik.http.routers.whoami.tls=true
      - traefik.http.services.whoami.loadbalancer.server.port=80
    networks:
      - cloudflaretunnel

networks:
  cloudflaretunnel:
    external: true
```

This configuration tells Traefik that:

- The router whoami should listen for requests on whoami.guneycansanli.com.
- It should use the websecure entry point (for HTTPS).
- It should enable TLS for secure communication.

### 3.2. Start the whoami Service

Run the following command to start the whoami service:

```bash
docker-compose -f whoami-compose.yaml up -d
```

![traefik][6]

## 3.3 Deploy a Sample Static Website (Another Service)

For a simple static website, you can use an Nginx container to serve HTML files. First, create your static site content:

Create Static Content
Create a directory named html/ and add an index.html file:

```html
<!-- ./html/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Static Site</title>
</head>
<body>
  <h1>Welcome to Your Static Site via Traefik!</h1>
  <p>This page is served by Nginx in a Docker container, routed through Traefik and secured by Cloudflare Tunnel.</p>
</body>
</html>
```

Create the Docker Compose File for the Static Website
Create a file named static-site-compose.yaml with the following content:


```yml
version: '3.9'
services:
  static-site:
    image: nginx:alpine
    container_name: static-site
    restart: always
    volumes:
      - ./html:/usr/share/nginx/html:ro
    labels:
      - traefik.enable=true
      - traefik.http.routers.static-site.rule=Host(`static.guneycansanli.com`)
      - traefik.http.routers.static-site.entrypoints=websecure
      - traefik.http.routers.static-site.tls=true
      - traefik.http.services.static-site.loadbalancer.server.port=80
    networks:
      - cloudflaretunnel

networks:
  cloudflaretunnel:
    external: true
```

![traefik][7]

Deploy the static website by running:

```bash
docker-compose -f static-site-compose.yaml up -d
```

---

## Step 4: Configure DNS in Cloudflare

Log into your Cloudflare account.

Navigate to DNS settings for your domain.

- Add a Public hostname record for whoami.guneycansanli.com pointing to your Cloudflare Tunnel.

For example:
  - Subdomain: whoami
  - Domain: guneycansanli.com
  - Type: HTTPS
  - URL: traefik
  - TLS: <Origin Server Name>

- Add a Public hostname record for static.guneycansanli.com pointing to your Cloudflare Tunnel.

  - Subdomain: static
  - Domain: guneycansanli.com
  - Type: HTTPS
  - URL: traefik
  - TLS: <Origin Server Name>

![traefik][9]


## Step 5: Test the Setup

Once everything is set up, you can test the service by navigating to https://whoami.guneycansanli.com in your browser.

Alternatively, use curl:

```bash
curl -I https://whoami.guneycansanli.com
curl -I https://static.guneycansanli.com
```

A successful response should return:

```bash
HTTP/2 200 OK
```

![traefik][8]

![traefik][10]


---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/traefik/traefik-1.png
[2]: ../assets/images/traefik/traefik-2.png
[3]: ../assets/images/traefik/traefik-3.png
[4]: ../assets/images/traefik/traefik-4.png
[5]: ../assets/images/traefik/traefik-5.png
[6]: ../assets/images/traefik/traefik-6.png
[7]: ../assets/images/traefik/traefik-7.png
[8]: ../assets/images/traefik/traefik-8.png
[9]: ../assets/images/traefik/traefik-9.png
[10]: ../assets/images/traefik/traefik-10.png
[11]: ../assets/images/traefik/traefik-11.png


