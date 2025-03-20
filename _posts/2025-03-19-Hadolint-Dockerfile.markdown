---
title: "Hadolint: Step-by-Step Guide to Linting Dockerfiles"
layout: post
date: 2025-03-19 12:20
image: ../assets/images/hadolint/main.jpg
headerImage: true
tag:
    - docker
    - hadolint
    - best-practise
category: blog
author: guneycansanli
description: Hadolint Step-by-Step Guide to Linting Dockerfiles
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

```bash
scoop install hadolint
```

### Verify Installation

```bash
hadolint --version
```

![hadolint][1]

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

![hadolint][2]

- You can see output and Hadolint recommends couple things We can make Dockerfile better/best practises. 
- Hadolint reads Dockerfile and analysis Dockefile. There are rules that Hadolint follow You can find rules [Hadolint Rules](https://github.com/hadolint/hadolint/tree/master/src/Hadolint/Rule?ref=devopscube.com) 

Hadolint ouput includes:  
- **🔹 Info**: Provides general suggestions for improvement. These are minor recommendations that can enhance the quality but are not critical.  
- **🎨 Style**: Focuses on formatting and structure, such as proper indentation, line length, and readability improvements.  
- **⚠️ Warning**: Highlights less critical issues, including minor security concerns and areas needing improvement.  
- **❌ Error**: Represents severe issues, potentially indicating security vulnerabilities or major violations of best practices. These must be addressed immediately.  

![hadolint][3]

- If you check the exit code, you will get a non-zero exit code after your Dockerfile checks.


### Optimized Dockerfile

```dockerfile
FROM ubuntu:20.04
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
RUN apt-get update && \
    apt-get install -y curl=8.4.0 --no-install-recommends && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

![hadolint][4]

- You can see above exmaple after I implement handolint recommendations new scan's exit code shows **0**


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
  - "*.ecr.amazonaws.com"
```

Run Hadolint with the configuration file:

```bash
hadolint --config .hadolint.yaml Dockerfile
```

![hadolint][5]

![hadolint][6]

## Integrating Hadolint in CI/CD Pipelines

You can Add Hadolint as a linting step in your CI/CD pipeline:

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

![hadolint][7]

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

[1]: ../assets/images/hadolint/hadolint-1.jpg
[2]: ../assets/images/hadolint/hadolint-2.jpg
[3]: ../assets/images/hadolint/hadolint-3.jpg
[4]: ../assets/images/hadolint/hadolint-4.jpg
[5]: ../assets/images/hadolint/hadolint-5.jpg
[6]: ../assets/images/hadolint/hadolint-6.jpg
[7]: ../assets/images/hadolint/hadolint-7.jpg




