---
title: "Distroless Containers"
layout: post
date: 2025-09-30 11:00
image: ../assets/images/distroless/main.jpg
headerImage: true
tag:
    - docker
    - distroless
    - container
category: blog
author: guneycansanli
description: Distroless Containers
---

# Distroless Containers Explained: A Practical Guide to Minimal, Secure Images

## 1. Introduction
Container images based on full Linux distributions like Ubuntu or Debian come packed with utilities, libraries, shells, and other tools that most applications don’t actually need at runtime. While that makes them convenient during development, it also leads to:

- Larger image sizes  
- Longer pull/push times  
- A larger attack surface  
- More CVEs to track

To solve this, many teams look to trim the fat by using minimal base images. Two common approaches emerge: starting **FROM scratch** or using **distroless** images. Both aim for minimalism, but they take different paths.

---

## 2. Why a Pure `FROM scratch` Image Can Be Painful
Starting with an empty image sounds ideal — no unnecessary packages, tiny size, and complete control. However, a scratch-based image is missing critical runtime components by default:

- No system directories like `/tmp`, `/var`, or `/home`  
- No CA certificates for HTTPS  
- No `/etc/passwd` or `/etc/group`  
- No timezone data  
- No shared libraries for dynamically linked programs

You end up manually recreating the environment your app expects. This is where distroless images step in — still minimal, but more complete.

---

## 3. What Distroless Images Actually Are
Distroless images (maintained under `gcr.io/distroless`) are designed to be “as close to scratch as possible” but still functional for production workloads. They:

- Provide an OS directory layout  
- Include basic system files (`passwd`, `group`, `nsswitch`, etc.)  
- Contain certificates and timezone data  
- Exclude shells, package managers, and unnecessary binaries  
- Have significantly fewer CVEs than full distro-based images

Think of them as curated minimal roots that remove operational junk while preserving essential structure.

---

## 4. Level 1: `gcr.io/distroless/static`
This is the ideal choice for statically compiled apps (Go is the classic example).

**Key traits:**
- ~2MB in size  
- Debian-rooted filesystem layout  
- Includes `/etc/passwd`, `/etc/group`, `tzdata`, certificates  
- No libc or other dynamic libraries  
- Zero CVEs (as of recent scans)

This makes it a more realistic and secure drop-in replacement for `FROM scratch` when your binary has no dynamic dependencies.

---

## 5. Level 2: `gcr.io/distroless/base` and `base-nossl`
If your application is dynamically linked (e.g., CGO-enabled Go, some Rust builds, C/C++ apps), you’ll need shared libraries.

Two options exist:

###  `gcr.io/distroless/base-nossl` (~15MB)
Includes:
- libc  
- libnss  
- libresolv  
- libpthread  
- Certificates and tzdata inherited from `static`

### `gcr.io/distroless/base` (~20MB)
Same as above, but adds:
- libssl + dependencies

These images are lightweight compared to full distros, and any remaining CVEs are usually limited to common libs like glibc and OpenSSL.

---

## 6. Level 3: `gcr.io/distroless/cc`
Some applications (like Rust programs) require `libgcc_s.so.1` at runtime. If you try running these on `base` and get missing lib errors, `distroless/cc` is your fix.

**Highlights:**
- Based on `distroless/base`  
- Adds libgcc and a few related libs  
- Total size ~23MB

It solves the shared-library problem without bloating your image.

---

## 7. Distroless Images for Runtime-Based Languages
For interpreted or VM-based languages, distroless provides prebuilt images with embedded runtimes:

- `gcr.io/distroless/java` — JVM (Java 17 & 21)  
- `gcr.io/distroless/nodejs` — Node.js 18/20/22  
- `gcr.io/distroless/python3` — Python 3.x

Under the hood:
- Java images build on `base-nossl`  
- Node.js and Python build on `cc`  

These images remove shells and package managers but supply the necessary runtime engines.

---

## 8. Building on Distroless: Multi-Stage Is Mandatory
Since distroless images have no shell and no package manager, you can’t install or build anything inside them directly. Instead, use a multi-stage build:

```dockerfile
FROM node:22 AS build
COPY . /app
WORKDIR /app
RUN npm ci --omit=dev

FROM gcr.io/distroless/nodejs22:nonroot
COPY --from=build /app /app
WORKDIR /app
CMD ["hello.js"]
```

![distro][1]

```bash
# 1. Build the Docker image
docker build -t my-node-app .

# 2. Run the container
docker run -d -p 3000:3000 my-node-app

# 3. Try access sheel of container
docker exec -it ab83751f34a3 /bin/bash

docker exec -it ab83751f34a3 sh
```

![distro][2]


![distro][3]

Tag Variants You Should Know:

- `:latest` — root user, no shell
- `:nonroot` — non-root user, no shell
- `:debug` — root user, with shell
- `:debug-nonroot` — non-root, with shell

For production, nonroot is generally recommended.


## 9. DISTROLESS - I GOT NO SHELL, WHAT CAN I DO?

Because distroless containers don’t ship with a shell, commands like this won’t work:

```bash
nsenter -t $(docker inspect -f '\{\{.State.Pid\}\}' <container>) -u hostname

nsenter -t $(docker inspect -f '\{\{.State.Pid\}\}' <container>) -n ip a

nsenter -t $(docker inspect -f '\{\{.State.Pid\}\}' <container>) -n netstat -tulpn

nsenter -t $(docker inspect -f '\{\{.State.Pid\}\}' 22cd91504233) -n ss -tulpn
```

![distro][4]

Instead, you can debug using nsenter, which runs tools from the host inside the container’s namespaces.


Here’s what it does:

- docker inspect -f '{{.State.Pid}}' <container> → Gets the process ID
- nsenter -t <PID> → Targets that process
- -n → Joins the network namespace
- ss -tulpn → Runs the host's ss inside the container context
- The ss command runs on the host, but it sees the container’s network interfaces and sockets.

You are not running the command inside the container directly. You are entering its namespaces from the host, so you don’t need a shell inside the container.

This works for monitoring network, inspecting processes, or doing other namespace-level operations without requiring /bin/sh.

This approach allows you to:

- Inspect running processes
- Debug networking
- Use host binaries without baking tools into images
   You’ll need appropriate privileges. For rootless runtimes, configure permissions for your user.


REF NSENTER: [https://www.redhat.com/en/blog/container-namespaces-nsenter](https://www.redhat.com/en/blog/container-namespaces-nsenter)

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/distroless/distro-1.jpg
[2]: ../assets/images/distroless/distro-2.jpg
[3]: ../assets/images/distroless/distro-3.jpg
[4]: ../assets/images/distroless/distro-4.jpg





