---
title: "Building Multi-Architecture Docker Images"
layout: post
date: 2025-04-29 11:00
image: ../assets/images/docker-buildx/main.jpg
headerImage: true
tag:
    - docker
    - image
category: blog
author: guneycansanli
description: Building Multi-Architecture Docker Images
---

# Building Multi-Architecture Docker Images (ARM, x86, ARM64)

When you try to pull a Docker image that isn't built for your system's architecture, you might encounter an error like:

```bash
➜ docker pull arm32v7/hello-world:latest
latest: Pulling from devhub/sample-app
no matching manifest for linux/arm64/v8 in the manifest list entries
```

![buildx][1]

This error occurs because the image was built only for the machine's architecture where it was created. This can result in a pod/container failures.

To prevent these issues, we can create Docker images that support multiple architectures.

---

## How to Build a Multi-Architecture Docker Image

Docker provides a tool called `buildx` to build images for different platforms.

> **Note:**  
> `buildx` comes bundled with the latest versions of Docker on Mac, Windows, and Linux.


## How to Install Docker Buildx Plugin (If you have not)

Note: If you are using old version or You have not **buildx** plugin.

You can install plugin package (see official install instructions) or manually I will install manually : 

Replace <user> with your actual username (e.g., Guney).

If you're root:

```bash
mkdir -p ~/.docker/cli-plugins
```

If you're installing for a non-root user:

```bash
mkdir -p /home/<user>/.docker/cli-plugins
```

3. Download the correct Buildx binary

For Docker 20.10.x, Buildx v0.8.2 or later is compatible, but we'll use the latest stable: v0.23.0

Install buildx, You can check latest release : [https://github.com/docker/buildx/releases/](https://github.com/docker/buildx/releases/)

```bash
wget -O ~/.docker/cli-plugins/docker-buildx https://github.com/docker/buildx/releases/download/v0.23.0/buildx-v0.23.0.linux-amd64
```

4. Make the binary executable

```bash
chmod +x ~/.docker/cli-plugins/docker-buildx
```


5. Verify Docker recognizes Buildx
Run:

```bash
docker info | grep -A5 Plugins
```
You should see:
 Plugins:
  buildx: Docker Buildx (Docker Inc., v0.23.0)

Also test:

```bash
docker buildx version
```

![buildx][2]

6. (Optional) Make Buildx the default builder

```bash
docker buildx create --use
docker buildx inspect --bootstrap   
```

### Using BuildX:

1. Write your `Dockerfile` for the application.
2. Use the following command to build and push the image for multiple platforms:

```bash
docker buildx build \
  --platform linux/amd64,linux/arm64,linux/arm/v7 \
  -t devhub/sample-app:latest \
  --push .
```



### Command Details:

- `--platform`: Specifies the architectures to build for:
  - `linux/amd64` → 64-bit Intel/AMD processors (x86_64)
  - `linux/arm64` → 64-bit ARM processors (Apple M1/M2, AWS Graviton)
  - `linux/arm/v7` → 32-bit ARM processors (e.g., older Raspberry Pi)
- `-t`: Sets the image name and tag.
- `--push`: Pushes the built multi-architecture image to the container registry.

After the build completes, the image will be accessible for all specified architectures, allowing seamless deployment across various platforms.

---

## Final Thoughts

Building multi-architecture Docker images ensures that your applications are flexible and portable across different hardware environments. This is crucial for cloud deployments and Kubernetes clusters that may have mixed architecture nodes.

Docker `buildx` makes the process simple and efficient.

---



---

Thanks for reading!

—

**Guneycan Sanli**



[1]: ../assets/images/docker-buildx/buildx-1.jpg
[2]: ../assets/images/docker-buildx/buildx-2.jpg

