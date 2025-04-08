---
title: "Enabling GitLab Container Registry (Omnibus Edition) – A Complete Guide"
layout: post
date: 2025-04-06 12:20
image: ../assets/images/gitlab-registry/main.jpg
headerImage: true
tag:
    - gitlab
    - container
    - registry
category: blog
author: guneycansanli
description: Enabling GitLab Container Registry (Omnibus Edition) – A Complete Guide
---

## 📦 Introduction

This guide helps you enable and configure the **GitLab Container Registry** on a **self-hosted GitLab Omnibus instance**. It includes two deployment paths:

1. ✅ **Cloudflare Tunnel Setup** – Secure tunneling with no SSL certs  
2. 🔐 **NGINX with SSL Certificates** – Classic public-facing deployment

---

## ⚙️ Prerequisites

Before you begin:

- ✅ Self-hosted **GitLab CE (Omnibus)** instance installed and running  
- 🐳 Docker installed (for testing registry push/pull)  
- 🌐 Subdomain/Domain for registry (e.g. `gitlab.registry.guneycansanli.com`)  
- ☁️ **Cloudflare Tunnel** OR valid SSL certificates (depending on setup)

> 📘 **Reference**: Official GitLab Docs  
> https://docs.gitlab.com/administration/packages/container_registry/

---

## Method 1: GitLab Registry via Cloudflare Tunnel (No SSL Certs Required)

> ✅ Best for home/self-hosted environments with dynamic IPs or blocked ports.

### Step 1: Set Up Cloudflare Tunnel

1. Create a Cloudflare Tunnel using `cloudflared`
2. Define your public hostname like this:

- **Subdomain:** `gitlab.registry`  
- **Domain:** `guneycansanli.com`  
- **Type:** `HTTP`  
- **URL:** `http://192.168.1.171:5050` (your internal GitLab IP and port)

![gitlab-regis][0]

This tells Cloudflare to expose the internal plain HTTP registry endpoint securely via HTTPS.

> ❗️Make sure **Type** is set to `HTTP`, not `HTTPS`, because the GitLab registry does **not** serve HTTPS directly in this setup.

---

### Step 2: Configure GitLab

Edit `/etc/gitlab/gitlab.rb`:

```ruby
gitlab_rails['gitlab_default_projects_features_container_registry'] = true
registry_external_url 'http://localhost:5050'  (or private ip of Gitlab server)
gitlab_rails['registry_enabled'] = true
```

![gitlab-regis][1]

Apply changes:

```bash
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

---

### Step 3: Allow HTTP Login from Docker

Docker by default requires HTTPS for registries. Since we’re using HTTP (via Cloudflare tunnel), you must explicitly mark it as insecure.

Edit `/etc/docker/daemon.json` on **the client machine**:

```json
{
  "insecure-registries": ["gitlab.registry.guneycansanli.com"]
}
```

![gitlab-regis][2]

Then restart Docker:

```bash
sudo systemctl restart docker
```

---

### Step 4: Test Login

```bash
docker login gitlab.registry.guneycansanli.com
```

If successful, you should see:

```text
Login Succeeded
```

![gitlab-regis][3]

Test from another VM : 

![gitlab-regis][4]

---

## Method 2: Traditional Registry with NGINX + SSL

> 🔐 Best for production/public GitLab deployments

### Step 1: Set Up SSL Certificates

Ensure certs are placed here:

```bash
/etc/gitlab/ssl/gitlab.registry.guneycansanli.com.crt
/etc/gitlab/ssl/gitlab.registry.guneycansanli.com.key
```

Use Let's Encrypt, ZeroSSL, or custom certs.

---

### Step 2: Configure GitLab

Update `/etc/gitlab/gitlab.rb`:

```ruby
registry_external_url 'https://gitlab.registry.guneycansanli.com'

gitlab_rails['registry_enabled'] = true
gitlab_rails['registry_host'] = 'gitlab.registry.guneycansanli.com'
gitlab_rails['registry_port'] = 5050

registry_nginx['enable'] = true
registry_nginx['listen_port'] = 5050
registry_nginx['ssl_certificate'] = "/etc/gitlab/ssl/gitlab.registry.guneycansanli.com.crt"
registry_nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/gitlab.registry.guneycansanli.com.key"
```

Reconfigure:

```bash
sudo gitlab-ctl reconfigure
sudo gitlab-ctl restart
```

---

### Step 3: Open Port 5050

```bash
sudo ufw allow 5050/tcp
```

---

### Step 4: Test

```bash
curl -v https://gitlab.registry.guneycansanli.com/v2/
docker login gitlab.registry.guneycansanli.com
```

---

## ✅ Using the Registry

You can now push images like this:

```bash
docker tag myapp gitlab.registry.guneycansanli.com/group/project/myapp:latest
docker push gitlab.registry.guneycansanli.com/group/project/myapp:latest
```

You’ll see registry URLs in GitLab at:

```
Project → Packages & Registries → Container Registry
```

---

## 🐛 Debug Logs

To check the registry service:

```bash
sudo gitlab-ctl tail registry
```

---

## 🧠 Final Notes

- If using Cloudflare Tunnel, Docker connects to `gitlab.registry.guneycansanli.com` securely via HTTPS, but GitLab itself serves plain HTTP internally.
- Marking the domain as `insecure-registries` in Docker is safe **only in trusted/private environments**.
- You can create your Pipeline and automate Docker image push/pull in you home-lab

![gitlab-regis][5]

![gitlab-regis][6]

---

## 🔗 Resources

- 📘 [GitLab Container Registry Docs](https://docs.gitlab.com/ee/administration/packages/container_registry/)
- 🔧 [SSL Settings in GitLab Omnibus](https://docs.gitlab.com/omnibus/settings/ssl.html)
- ☁️ [Cloudflare Tunnel Guide](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/)

---

Thanks for reading!

:+1: :+1: :+1: :+1: :+1: :+1:

— Guneycan Sanli

[0]: ../assets/images/gitlab-registry/gitlab-regis-0.jpg
[1]: ../assets/images/gitlab-registry/gitlab-regis-1.jpg
[2]: ../assets/images/gitlab-registry/gitlab-regis-2.jpg
[3]: ../assets/images/gitlab-registry/gitlab-regis-3.jpg
[4]: ../assets/images/gitlab-registry/gitlab-regis-4.jpg
[5]: ../assets/images/gitlab-registry/gitlab-regis-5.jpg
[6]: ../assets/images/gitlab-registry/gitlab-regis-6.jpg
