---
title: "Running GitLab CI Jobs Locally"
layout: post
date: 2025-04-27 11:00
image: ../assets/images/gitlab-ci-local/main.jpg
headerImage: true
tag:
    - gitlab
    - ci
category: blog
author: guneycansanli
description: Running GitLab CI Jobs Locally
---

# Guide: Running GitLab CI Jobs Locally

---

## 1. Introduction

Testing code before deploying is critical. GitLab CI/CD usually runs jobs on remote servers, but it’s often useful to execute them locally during development. As I Gitlab User/Learner I was looking for a simple way to etst my CI (gitlab-ci.yaml) without pushing any code to to repository.
This guide shows how to run GitLab CI pipelines on your own machine.
---

## 2. Method 1: Using `gitlab-ci-local`

### 2.1 Install

Check here if You want to install to other OS : [gitlab-ci-local](https://github.com/firecow/gitlab-ci-local?tab=readme-ov-file#installation)

Example `.gitlab-ci.yml`:

```yaml
unit_test:
  image: python:latest
  script:
    - echo "running tests locally..."
```

Install `gitlab-ci-local` (Linux example):

```bash
sudo wget -O /etc/apt/sources.list.d/gitlab-ci-local.sources https://gitlab-ci-local-ppa.firecow.dk/gitlab-ci-local.sources
sudo apt-get update
sudo apt-get install gitlab-ci-local
```

Verify installation:

```bash
gitlab-ci-local --version
```

---

### 2.2 List Available Jobs

Navigate to your project directory:

```bash
cd demo
gitlab-ci-local --list
```

You should see:

```
unit_test
```

![ci][2]

---

### 2.3 Run a Job

Execute the job:

```bash
gitlab-ci-local unit_test
```

Successful output confirms the job ran properly.


![ci][3]


---

## 3. Method 2: Using GitLab Emulator (`gle`)

### 3.1 Install

Clone and install:

```bash
git clone https://gitlab.com/cunity/gitlab-emulator.git
cd gitlab-emulator
./install-venv.sh
source ~/.gle/venv/bin/activate
```

Check version:

```bash
gle --version
```

![ci][4]

---

### 3.2 List and Run Jobs

Switch to project folder:

```bash
cd demo
gle --list
```

Run a specific job:

```bash
gle unit_test
```

You will see logs indicating successful execution inside a Docker container.

![ci][5]

---

## 4. Method 3: Using GitLab Runner (Deprecated)

### 4.1 Install (Specific Version)

This feature deprecaetd with newer gitlab verisons : [https://gitlab.com/gitlab-org/gitlab/-/issues/385235](https://gitlab.com/gitlab-org/gitlab/-/issues/385235)

Download GitLab Runner 13.3.0:

```bash
sudo curl --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/v13.3.0/binaries/gitlab-runner-darwin-amd64
chmod +x /usr/local/bin/gitlab-runner
```

Confirm version:

```bash
gitlab-runner --version | grep Version
```

---

### 4.2 Run the Job

Execute using Docker:

```bash
gitlab-runner exec docker unit_test
```

The runner will pull the image, prepare the environment, and execute your test script.

---

## 5. Conclusion

In this guide, we demonstrated three different ways to run GitLab CI pipelines locally:

- **gitlab-ci-local** for fast lightweight runs
- **GitLab Emulator (gle)** for realistic simulations
- **GitLab Runner** (older version) for authentic environment tests

Running CI jobs locally can greatly speed up development and improve the quality of your software.


---

Thanks for reading!

—

**Guneycan Sanli**



[1]: ../assets/images/gitlab-ci-local/ci-1.jpg
[2]: ../assets/images/gitlab-ci-local/ci-2.jpg
[3]: ../assets/images/gitlab-ci-local/ci-3.jpg
[4]: ../assets/images/gitlab-ci-local/ci-4.jpg
[5]: ../assets/images/gitlab-ci-local/ci-5.jpg

