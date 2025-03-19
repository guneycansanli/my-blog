---
title: "Hadolint: Step-by-Step Guide to Linting Dockerfiles"
layout: post
date: 2025-03-21 12:20
image: ../assets/images/hantolint/main.jpg
headerImage: true
tag:
    - docker
    - hadolint
    - best-practise
category: blog
author: guneycansanli
description: Hadolint: Step-by-Step Guide to Linting Dockerfiles
---

## What is Hadolint?

Hadolint is an open-source command-line tool for linting Dockerfiles. It helps identify syntax errors, security vulnerabilities, and inefficiencies, ensuring Dockerfiles follow best practices.


### How Hadolint Works

1.  Reads and parses the Dockerfile.
2.  Converts it into an Abstract Syntax Tree (AST).
3.  Checks each instruction against predefined rules.
4.  Reports issues categorized as **Info, Style, Warning, or Error**.



## Installing Hadolint

### Install on Linux

```bash
wget -O hadolint https://github.com/hadolint/hadolint/releases/download/v2.12.0/hadolint-Linux-x86_64
sudo mv hadolint /usr/local/bin/hadolint
sudo chmod +x /usr/local/bin/hadolint

```

### Install on Mac

```bash
brew install hadolint

```

### Install on Windows

```powershell
scoop install hadolint

```

### Verify Installation

```bash
hadolint --version

```

## Linting Dockerfiles Using Hadolint

Run Hadolint against your Dockerfile:

```bash
hadolint Dockerfile

```

### Example Dockerfile (Unoptimized)

```dockerfile
FROM ubuntu:latest
RUN apt-get update
RUN apt-get install -y curl

```

### Running Hadolint on the Unoptimized Dockerfile

```bash
hadolint Dockerfile

```

Hadolint will output warnings and errors if any exist.

### Optimized Dockerfile

```dockerfile
FROM ubuntu:20.04
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
RUN apt-get update && \
    apt-get install -y curl=8.4.0 --no-install-recommends && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
CMD ["echo", "Hello, world!"]

```

## Ignoring Rules in Hadolint

If you want to ignore a specific rule (e.g., DL3008):

```bash
hadolint --ignore DL3008 Dockerfile

```

## Using hadolint.yaml Configuration

Create a `.hadolint.yaml` file to customize linting rules.

```yaml
failure-threshold: warning
ignored:
  - DL3007
override:
  warning:
    - DL3015
trustedRegistries:
  - docker.io
  - "*.gcr.io"

```

Run Hadolint with the configuration file:

```bash
hadolint --config .hadolint.yaml Dockerfile

```


## Integrating Hadolint in CI/CD Pipelines

Add Hadolint as a linting step in your CI/CD pipeline:

```bash
hadolint Dockerfile || exit 1

```

This will fail the pipeline if the Dockerfile contains critical issues.

- You may need to create new credential.

## Running Hadolint with Docker

If you don't want to install Hadolint, you can run it using Docker:

```bash
docker run --rm -i hadolint/hadolint < Dockerfile

```


## Hadolint Online Version

You can also use Hadolint directly from your browser:

[Hadolint Online](https://hadolint.github.io/hadolint/)


## Benefits of Using Hadolint

-   **Improves Dockerfile quality** by identifying errors.
-   **Enhances security** by detecting vulnerabilities.
-   **Optimizes performance** by reducing unnecessary steps.
-   **Ensures consistency** across projects.


## Tips for Using Hadolint

1.  **Fix critical errors first** to improve security and performance.
2.  **Enable all rules** for maximum linting coverage.
3.  **Customize Hadolint** using `.hadolint.yaml` as needed.
4.  **Integrate Hadolint in CI/CD** to enforce best practices automatically.


## Conclusion

Linting Dockerfiles is crucial for security, efficiency, and consistency. Hadolint is a powerful tool that helps enforce best practices. Use it in your local development and CI/CD pipelines to ensure high-quality container images.


---

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

[1]: ../assets/images/
[2]: ../assets/images/
[3]: ../assets/images/
[4]: ../assets/images/
[5]: ../assets/images/
[6]: ../assets/images/
[7]: ../assets/images/
[8]: ../assets/images/
[9]: ../assets/images/
[10]: ../assets/images/



