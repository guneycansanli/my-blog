---
title: "Using Docker Hardened Images (DHI)"
layout: post
date: 2026-01-22 11:00
image: ../assets/images/docker-hdi/main.jpg
headerImage: true
tag:
    - docker
    - dhi
    - debian
    - containers
category: blog
author: guneycansanli
description: Using Docker Hardened Images (DHI)
---

## Introduction

Docker Hardened Images (DHI) provide a minimal, secure, and production-ready base for your containers.  
With Docker making DHI free and open source, developers can now use enterprise-grade security without changing their Docker workflow.

## What Are Docker Hardened Images (DHI)?

Docker Hardened Images are security-focused base container images built to minimize risk before your application code even runs.

They are:

- Minimal by design (only essential packages included)
- Built from trusted Linux bases (Alpine and Debian)
- Continuously rebuilt using Docker’s secure build pipeline
- Cryptographically verifiable, with signed SBOMs and provenance

Think of DHI as “clean-room” base images that remove unnecessary software—the biggest source of container vulnerabilities.

## What You Get with Free DHI

The free tier is more than enough for most teams.

Included

- 1,000+ hardened images
- Near-zero known CVEs
- Signed SBOMs
- SLSA Level 3 build provenance
- Full vulnerability transparency

This article focuses on **Debian 12 hardened base images (`dhi.io/debian-base:12`)**, showing how to migrate existing Dockerfiles and create secure applications.

---

## Why Use `dhi.io/debian-base:12`?

Standard Debian images often include:

- Extra utilities and libraries you don’t need
- Hundreds of low-risk CVEs
- No SBOM or build provenance

By contrast, `dhi.io/debian-base:12` offers:

- Minimal package footprint  
- Near-zero known CVEs  
- Signed SBOMs and SLSA Build Level 3 provenance  
- Drop-in replacement for standard Debian images  

Using DHI reduces attack surface, improves build speed, and strengthens your supply chain.

---

## Part 1: Migrating a Standard Debian Dockerfile

### Before (Standard Debian 12 Image)

<pre><code>FROM debian:12

RUN apt-get update && \
    apt-get install -y curl ca-certificates && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY . .
CMD ["bash"]
</code></pre>

**Problems:**

- Pulls extra packages  
- Inherits CVEs you didn’t introduce  
- No signed SBOM or provenance  

### After (Docker Hardened Image)

<pre><code>FROM dhi.io/debian-base:12

RUN apt-get update && \
    apt-get install -y curl ca-certificates && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY . .
CMD ["bash"]
</code></pre>


No other changes are required. The image is a drop-in replacement.

---

## Part 2: Python Application Example

<pre><code>FROM dhi.io/debian-base:12

ENV PYTHONUNBUFFERED=1

RUN apt-get update && \
    apt-get install -y python3 python3-pip && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY requirements.txt .
RUN pip3 install --no-cache-dir -r requirements.txt

COPY . .
CMD ["python3", "app.py"]
</code></pre>

This setup keeps full Debian compatibility while minimizing vulnerabilities in the base image.

---

## Part 3: Multi-Stage Build Example

### Build Stage

<pre><code>FROM dhi.io/debian-base:12 AS builder

RUN apt-get update && \
    apt-get install -y build-essential && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /src
COPY . .
RUN make build
</code></pre>

### Runtime Stage

<pre><code>FROM dhi.io/debian-base:12

WORKDIR /app
COPY --from=builder /src/bin/app /app/app

CMD ["./app"]
</code></pre>

This ensures **both build and runtime images are hardened** and traceable.

---

## Conclusion

Security starts with the base your application runs on.  
By switching to **`dhi.io/debian-base:12`**, you immediately:

- Reduce vulnerabilities  
- Improve build speed and reliability  
- Gain a fully traceable supply chain  

Docker Hardened Images make **secure-by-default containers** accessible to everyone, and Debian 12 DHI is the safest way to run Debian in production today.

---

Thanks for reading!

**Guneycan Sanli**