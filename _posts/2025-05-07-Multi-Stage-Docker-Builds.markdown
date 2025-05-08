---
title: "Docker Images with Multi-Stage Builds"
layout: post
date: 2025-05-04 11:00
image: ../assets/images/docker-multi-stage/main.jpg
headerImage: true
tag:
    - docker
    - image
category: blog
author: guneycansanli
description: Docker Images with Multi-Stage Builds 
---

# Docker Images with Multi-Stage Builds 

If you’ve ever struggled with oversized Docker images that take forever to ship or open up security holes, then you’ll appreciate what we’re covering today: **Multi-Stage Docker Builds** — a smarter, cleaner, and more scalable approach to building container images.

---

##  Overview

In this tutorial, you'll learn:

1. *What is a multi-stage Docker build?*  
2. *Optimizing a Node.js image*  
3. *Trimming down a Golang image*  
4. *Size & performance impact*  
5. *Optimization tips*  
6. *Best practices*

🔗 [Official Docker Multi-Stage Build Docs](https://docs.docker.com/develop/develop-images/multistage-build/)

---

## What Is a Multi-Stage Build?

A **multi-stage build** uses multiple `FROM` layers in one Dockerfile. Each stage does a specific job — like compiling code or installing dependencies — and only the necessary artifacts are passed into the final image.

> *Goal: Keep your production image small, clean, and dependency-free.*

---

## Example 1: Node.js App

### Traditional Dockerfile (Not Ideal)

Create a file: `Dockerfile.bloat`

```dockerfile
FROM node:18

WORKDIR /app
COPY . .
RUN npm install && npm run build

CMD ["npm", "start"]
```

**Problems:**

- Includes dev dependencies and source code.
- Sluggish performance due to a larger image.
- More layers = more security concerns.


![Multi][1]

You can see my image is **1.09GB**

---

### Multi-Stage Version

Create a new file: `Dockerfile`

```dockerfile
# First stage: build
FROM node:18 AS builder

WORKDIR /app

# Copy only package files first for better caching
COPY package*.json ./

# Install all dependencies
RUN npm install

# Copy the rest of the application
COPY . .

# Run the build script
RUN npm run build

# Second stage: production image
FROM node:18-slim

WORKDIR /app

# Copy built app and node_modules from builder
COPY --from=builder /app ./

# Use npm start, same as in the traditional version
CMD ["npm", "start"]
```

Result:
- Production image has only what's required to run.
- Faster container start times.
- Lower attack surface.


![Multi][2]

The image is **192MB**


## Quick Comparison

Run:

```bash
docker images | grep -E 'nodeapp'
```

![Multi][3]


---

## Example 2: Go Application

### Bloated Version

Create `Dockerfile.golang.bloat`:

```dockerfile
FROM golang:1.21

WORKDIR /app
COPY . .
RUN go build -o app

CMD ["./app"]
```

Issues:
- Over 700MB in size.
- Retains Go compiler and source code.

---

### Optimized Multi-Stage Dockerfile

```dockerfile
FROM golang:1.21 AS builder

WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build -o app

FROM alpine:latest

WORKDIR /root/
COPY --from=builder /app/app .

CMD ["./app"]
```

Image Size:
From ~700MB ➜ ~20MB (can drop under 5MB with `scratch`)

---

**Why Multi-Stage Wins:**

- Reduces build time with Docker cache.
- Smaller push/pull size.
- Keeps images tidy and focused.
- Simplifies debugging and maintenance.

---

## Pro Tips for Go Builds

| Tip                     | Description |
|-------------------------|-------------|
| `CGO_ENABLED=0`         | Builds a static binary, no C libraries |
| `GOOS`, `GOARCH`        | Cross-compile for Linux, Windows, ARM, etc. |
| `FROM scratch`          | Minimal image with just the binary |
| `distroless base `      | Secure images with no package manager |

---

## Best Practices

- Use base images like `alpine` or `node:slim`  
- Use `.dockerignore` to skip unnecessary files (`.git`, `docs`, `node_modules`)  
- Separate build, test, and production steps into clear stages  
- Always minimize what you copy into the final stage

Example `.dockerignore`:

```
.git
node_modules
*.md
tests/
```

---

## Final Thoughts

Multi-stage builds are a simple yet powerful way to:

- Reduce Docker image sizes  
- Enhance security  
- Speed up deployment pipelines  

---

🔗 _Explore more in the [official Docker docs](https://docs.docker.com/develop/develop-images/multistage-build/)_

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/docker-multi-stage/multi-1.jpg
[2]: ../assets/images/docker-multi-stage/multi-2.jpg
[3]: ../assets/images/docker-multi-stage/multi-3.jpg

