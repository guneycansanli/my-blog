---
title: "Docker SBOM"
layout: post
date: 2025-09-22 11:00
image: ../assets/images/sbom/main.jpg
headerImage: true
tag:
    - docker
    - sbom
    - security
category: blog
author: guneycansanli
description: Docker SBOM
---

# The Complete Guide to Generating Docker SBOMs for Container Security

Software supply chain security has become a critical focus for DevOps teams. One of the most powerful tools for transparency and security is the **SBOM** (Software Bill of Materials). This guide will walk you through what SBOMs are, why they matter, and how to generate and use them with Docker.

---

## What Is an SBOM?

An **SBOM (Software Bill of Materials)** is a detailed inventory of all the components, packages, and libraries inside a piece of software (e.g., a container image).  

Think of it as a “recipe list” for your container image.

**Why it matters:**
- Visibility into dependencies
- Faster vulnerability detection
- Compliance with standards (NIST, FedRAMP, etc.)
- Improved incident response

---

## Why Use SBOMs in Containers?

1. **Security** – Detect vulnerable packages before shipping images.  
2. **Compliance** – Regulations increasingly require SBOMs.  
3. **Transparency** – Clear understanding of your software supply chain.  
4. **Auditability** – Simplify investigations during incidents.  

---

## Docker’s Native SBOM Support

Starting with **Docker 20.10.24+**, Docker CLI includes an `sbom` subcommand.  
It’s powered by [Syft](https://github.com/anchore/syft) under the hood.

Check your version:

```bash
docker --version
```

If ≥20.10.24, you’re good to go.

If You do not have Docker SBOM Plug-in you can install manually

```bash
#Install Docker SBOM
curl -sSfL https://raw.githubusercontent.com/docker/sbom-cli-plugin/main/install.sh | sh -s --

#Verify Docker SBOM
docker sbom --version
```

![sbom][1]

![sbom][2]

## Generating an SBOM

Example 1: NGINX Official Image

```bash
docker sbom nginx:latest
```

This outputs all packages inside the nginx:latest container.

![sbom][3]


## Example 2: Node.js Application

### Step 1 – Dockerfile

```dockerfile
# simple Node.js app
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
CMD ["node", "index.js"]
```

### Step 2 – Build Image

```bash
docker build -t my-node-app .
```

### Step 3 – Generate SBOM

```bash
docker sbom my-node-app
```

## Exporting SBOMs

By default, Docker outputs SBOMs in SPDX JSON format. You can save it:

```bash
docker sbom nginx:latest --format spdx-json > sbom.json
```

![sbom][4]

Alternative format (CycloneDX):

```bash
docker sbom nginx:latest --format cyclonedx-json > sbom.cdx.json
```

![sbom][5]

## Integrating SBOMs into CI/CD


```yaml
name: Build and SBOM
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Build Docker image
        run: docker build -t my-node-app .
      - name: Generate SBOM
        run: docker sbom my-node-app --format spdx-json > sbom.json
      - name: Upload SBOM
        uses: actions/upload-artifact@v3
        with:
          name: sbom
          path: sbom.json
```

## Scanning SBOMs for Vulnerabilities

SBOMs alone aren’t enough — you must scan them.

Using Grype [Grype](https://github.com/anchore/grype)

```bash
# Scan directly from image
grype my-node-app

# Or scan from SBOM file
grype sbom:sbom.json
```

![sbom][6]

This identifies CVEs linked to your packages.


## Storing SBOMs

Best practices:
Store SBOMs in artifact registries (ECR, Harbor, JFrog).
Tag SBOMs with image hashes (sha256) for immutability.
Keep them versioned alongside container images.

## Best Practices

**Start small: generate SBOMs early in your pipeline.**
**Automate SBOM creation in CI/CD.**
**Always scan SBOMs with tools like Grype.**
**Store SBOMs securely (artifact repos, Git).**
**Consider signing SBOMs for integrity validation.**


## Conclusion

SBOMs bring visibility, compliance, and security to your containers. With Docker’s built-in SBOM support, it’s easier than ever to adopt.

---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/sbom/sbom-1.jpg
[2]: ../assets/images/sbom/sbom-2.jpg
[3]: ../assets/images/sbom/sbom-3.jpg
[4]: ../assets/images/sbom/sbom-4.jpg
[5]: ../assets/images/sbom/sbom-5.jpg
[6]: ../assets/images/sbom/sbom-6.jpg




