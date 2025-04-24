---
title: "How to Push a Local Project to a New GitLab Repository"
layout: post
date: 2025-04-22 11:00
image: ../assets/images/gitlab-hadolint/main.jpg
headerImage: true
tag:
    - gitlab
    - linting
    - ci
    - docker
category: blog
author: guneycansanli
description: How to Push a Local Project to a New GitLab Repository
---

# Step-by-Step Guide: Integrating Hadolint into GitLab CI/CD for Merge Request Linting

This guide helps you integrate Hadolint into GitLab CI/CD to lint Dockerfiles and show results directly in Merge Requests. I have a basic Pythin Fast API project which has Dockerfile so I can scan my Dockerfile with **Hadolint**. 

Note: I was already gitlab runner which can use DinD (Docker inside Docker)

---

## Step 1: Add `.gitlab-ci.yml`

Create this file at the root of your repository:

```yaml
stages:
  - lint

docker-lint:
  stage: lint
  image: hadolint/hadolint:latest-debian
  rules:
    - if: '$CI_PIPELINE_SOURCE == "merge_request_event" && $CI_MERGE_REQUEST_TARGET_BRANCH_NAME == "main"'
      when: always
  before_script:
    - echo -e "trustedRegistries:\n  - custom-registry.cloud.com" > .hadolint.yaml
  script:
    - |
      if hadolint -f gitlab_codeclimate -c .hadolint.yaml Dockerfile | tee docker-lint-$CI_COMMIT_SHORT_SHA.json ;then
        echo -e "\nChecking Dockerfile hardening is successfull."
      else
        echo -e "\nChecking Dockerfile hardening has issues. Please check and fix it."
        exit 1
      fi
  artifacts:
    name: "$CI_JOB_NAME artifacts from $CI_PROJECT_NAME on $CI_COMMIT_TAG"
    expire_in: 1 day
    when: always
    reports:
      codequality:
        - "docker-lint-$CI_COMMIT_SHORT_SHA.json"
    paths:
      - "docker-lint-$CI_COMMIT_SHORT_SHA.json"
  interruptible: true
```

![hado][1]

### What this does:

- Uses the official Hadolint image to scan all Dockerfiles.
- Outputs results in GitLab's Code Quality format (`docker-lint.json`).
- Job runs **only for Merge Requests** (`merge_request_event`).
- Results are shown in the **Merge Request UI** as code quality feedback.
- Additionally, you can pass a custom configuration file in the command line with the --config option. In our example, Hadolint can warn you when images from untrusted repositories are being used in Dockerfiles, you can append the trustedRegistries keys to the configuration file, as shown below

# cat hadolint.yaml (Example) 
trustedRegistries:
  - custom-registry.cloud.com

I have used above hadolint.yaml in CI file but You can use as CI/CD variable.

---

## Step 2: Push Main Branch

```bash
git checkout -b main
git add .gitlab-ci.yml
git commit -m "Add Hadolint linting pipeline"
git push -u origin main
```

Let the pipeline run — nothing will happen yet since it only triggers on Merge Requests.

---

## Step 3: Create a Feature Branch

Make a change that violates a Dockerfile rule:

```bash
git checkout -b feature/lint-test
echo 'MAINTAINER someone' >> Dockerfile
git commit -am "Trigger Hadolint warning"
git push -u origin feature/lint-test
```


I have pushed my the code **feature/add-lint-issue** branch and it did not trigger any Pipeline.

---

## Step 4: Open a Merge Request

Create a Merge Request from `feature/lint-test` into `main`.

GitLab will:

- Trigger the pipeline (because of `merge_request_event`)
- Run `docker-lint` job
- Generate `docker-lint.json` as an artifact
- Show Hadolint feedback in the **Code Quality section** of the MR UI

Since my Hadolint yaml expect **trustedRegistries: - custom-registry.cloud.com** , Pipeline will fail until I fix it

![hado][2]

![hado][3]

---

## Step 5: Fix Lint Issues and Re-push

After seeing the warnings:

```bash
# Remove deprecated MAINTAINER line
git commit -am "Fix Hadolint issues"
git push
```


Since my Hadolint yaml expect **trustedRegistries: - custom-registry.cloud.com** , Pipeline will fail until I fix it
Also We can check artifacts and see Hadolint analysis even pipeline passes.

The MR updates and the new pipeline shows a clean code quality report.

I have fixed my Dockerfile and pushed 

![hado][4]


![hado][5]

![hado][6]

MR pipeline now succeed and my artifact did not returm any warnings or errors. We can accept MR now.

---


## Optional: Customize Hadolint Rules

Add a `.hadolint.yaml` file at the root:

```yaml
ignored:
  - DL3008  # Ignore apt-get pinning
```

For full rule reference: https://github.com/hadolint/hadolint#rules

---

## Notes on MR-only Trigger

This setup uses:

```yaml
rules:
  - if: '$CI_PIPELINE_SOURCE == "merge_request_event" && $CI_MERGE_REQUEST_TARGET_BRANCH_NAME == "main"'
    when: always
```

This ensures:

- Runs only on MRs to main branch
- Skips push/tag/schedule pipelines
- Re-triggers on every commit in the MR (ideal for iterative linting)

---

## Done!

You now have:

- Dockerfile linting via Hadolint  
- GitLab CI/CD integration  
- MR-only pipeline triggering  
- Merge Request feedback with actionable Code Quality reports

Improve code quality one Dockerfile at a time!

---


---

Thanks for reading!

—

**Guneycan Sanli**



[1]: ../assets/images/zombie/zombie1.jpg
[2]: ../assets/images/zombie/zombie-2.jpg
