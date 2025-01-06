---
title: "Optimize Docker Images: Practical Guide How to Reduce Docker Image Size?"
layout: post
date: 2025-01-04 11:20
image: ../assets/images/docker-image-size/main.jpg
headerImage: true
tag:
    - docker
category: blog
author: guneycansanli
description: Optimize Docker Images: Practical Guide How to Reduce Docker Image Size?
---

# Optimize Docker Images: Practical Guide

### Introduction

Optimizing Docker images is crucial for reducing size, improving build times, and enhancing security. A typical Docker image consists of a base image, dependencies/files/configs, and unwanted software (cruft). This guide explores effective methods to optimize Docker images with practical examples.

---

## Methods to Optimize Docker Images

### 1. Use Minimal Base Images

**Minimal base images** reduce the size and security risks of Docker images. Examples include:

- **Alpine**: Lightweight (5.59MB) and secure.
- **Distroless**: Stripped-down images without a shell, offering minimal utilities for specific languages like Java, Node.js, and Python.

- Note: Distroless images are so minimal that they don’t even have a shell in them. So, you might ask, then how do we debug applications? They have the debug version of the same image that comes with the busybox for debugging.

**Example:**
```dockerfile
FROM alpine:latest
```

> **Note:** Use enterprise-approved base images for production to ensure security compliance.

---

### 2. Use Multistage Builds

**Multistage builds** separate build and runtime environments, reducing the final image size.

**Example: Multistage Dockerfile**
```dockerfile
# Stage 1: Build
FROM node:16 as build
WORKDIR /app
COPY package.json index.js env ./
RUN npm install

# Stage 2: Runtime
FROM node:alpine
COPY --from=build /app /
EXPOSE 8080
CMD ["node", "index.js"]
```

**Results:**
- Single-stage image: 916MB
- Multistage image: 163MB (~80% size reduction)


- Let's Check 2 different dokcer images for that case. We can build both ways and check out real differences.


![docker-ima][1]


- Let's Build a node js docker image with **node:16** base image itself

```dockerfile
FROM node:16

COPY . .

RUN npm installEXPOSE 3000

CMD [ "node", "index.js" ]
```

```
$ docker build -t test/node-app:1.0 --no-cache -f Dockerfile1 .
```

- I have a **916MB** image 

![docker-ima][2]


- Let's Build a multi stages docker image.
- We will start with node:16 as our base image to handle all the dependency and module installations. Once everything is set up, we'll transfer the application contents into a lightweight alpine image. This approach ensures that the alpine image, known for its minimal utilities and small size, serves as our final runtime environment, keeping the overall image efficient and slim.

```dockerfile
FROM node:16 as build

WORKDIR /app
COPY package.json index.js env ./
RUN npm install

FROM node:alpine as main

COPY --from=build /app /
EXPOSE 8080
CMD ["index.js"]
```

```
$ docker build -t test/node-app:2.0 --no-cache -f Dockerfile1 .
```

![docker-ima][3]


---

### 3. Minimize Layers

Each `RUN`, `COPY`, and `FROM` instruction creates a new layer. Combining instructions minimizes layers and reduces size.

**Example: Unoptimized Dockerfile**
- Dockerfile3 
```dockerfile
RUN apt-get update -y
RUN apt-get upgrade -y
RUN apt-get install vim -y
RUN apt-get install net-tools -y
```

**Optimized Dockerfile:**
- Dockerfile4
```dockerfile
RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install --no-install-recommends vim net-tools -y
```

**Results:**
- Reduced build time
- Smaller image size


- The Docker daemon has an in-built capability to display the total execution time that a Dockerfile is taking. We cna enable this feature with:

1. We need to create **daemon.json** file under /etc/docker/ and it should contains
```
{
  "experimental": true
}
```

2. We need to export **DOCKER_BUILDKIT=1** to env
```
export DOCKER_BUILDKIT=1
```

3. We can now build an image and analysis.

```
time docker build -t test/optimize:3.0 --no-cache -f Dockerfile3 .
```

Result:
![docker-ima][4]


- Let’s combine the RUN commands into a single layer & save it as Dockerfile4 as I shared above.
```
time docker build -t test/optimize:3.0 --no-cache -f Dockerfile4 .
```

- In Dokcerfile4 RUN command, we have used --no-install-recommends flag to disable recommended packages. It is recommended whenever you use install in your Dockerfiles

![docker-ima][5]

- Dockerfile3 build time was 18s and Dockerfile4 build time was 9s and also Image size a little bit less than Dockerfile3.


---

### 4. Leverage Caching

- Docker operates using a layered filesystem, where each instruction in the Dockerfile creates a new layer. These layers are cached by Docker and reused if they remain unchanged.

- Because of this, it’s best practice to place instructions for installing dependencies and packages early in the Dockerfile, before the COPY commands. This allows Docker to cache the layers with installed dependencies, making subsequent builds faster when only the application code changes.

- It's important to note that COPY and ADD instructions invalidate the cache for all layers that follow them. As a result, Docker rebuilds all layers after these instructions are executed.

- To optimize the build process, place instructions that are less likely to change, such as installing dependencies, at the top of the Dockerfile. This ensures Docker can leverage caching efficiently.

- For instance, consider these two Dockerfiles: (example comparison would follow in context).

**Good Example:**
```dockerfile
FROM ubuntu:latest
RUN apt-get update -y && apt-get install -y vim
COPY . .
```

**Less Optimal Example:**
```dockerfile
FROM ubuntu:latest
COPY . .
RUN apt-get update -y && apt-get install -y vim
```

**Why?** The second example invalidates the cache more frequently.

- Order of Instructions: The COPY command is placed before the RUN command for installing dependencies.
- Why is this suboptimal?
- Every time the source code changes, the COPY . . instruction invalidates the cache for all subsequent layers.
- Docker rebuilds the entire image, including the RUN layer (installing dependencies), even if the dependencies haven’t changed.
- This results in slower builds, as the dependency installation step is unnecessarily repeated.

---

### 5. Use Dockerignore

Use a `.dockerignore` file to exclude unnecessary files from the build context, reducing the image size and preventing cache invalidation.

**Example: .dockerignore File**
```
node_modules
*.log
.env
```

---

### 6. Keep Application Data Elsewhere

Avoid storing application data in images. Use Docker volumes or Kubernetes persistent volumes to manage data externally, keeping images lightweight.

---

## Docker Image Optimization Tools

Here are some tools to help optimize Docker images:

- **Dive**: Analyze layers in Docker images and identify optimization opportunities.
  - GitHub: [Dive](https://github.com/wagoodman/dive)

- **SlimToolkit**: Reduce image size and improve security.
  - GitHub: [SlimToolkit](https://github.com/slimtoolkit/slim)

- **Docker Squash**: Merge layers to reduce image size.

---

## Summary

By using minimal base images, multistage builds, minimizing layers, leveraging caching, and other best practices, you can create optimized Docker images. Integrating tools like Dive and SlimToolkit into your CI/CD pipeline ensures consistent optimization. For security, scan images with tools like Trivy before deployment.


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


