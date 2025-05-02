---
title: "GitLab CI Dashboard Centralized Pipeline & Schedule Monitoring"
layout: post
date: 2025-05-01 11:00
image: ../assets/images/gitlab-ci-dashboard/main.jpg
headerImage: true
tag:
    - gitlab
    - ci
category: blog
author: guneycansanli
description: GitLab CI Dashboard Centralized Pipeline & Schedule Monitoring
---

## Introduction

Managing GitLab pipelines across multiple projects can be a challenge. The native functionality of GitLab limits views to the project level, making it difficult to get a high-level overview of all pipeline activities. Recentyl I was looking for way to have all pipeline in a centrialze location. There are many diffrent opions for it You can monitor pipelines with monitoring tools and have dahsboards etc. I found The **GitLab CI Dashboard** solves this problem by providing a **centralized dashboard** for monitoring pipelines, schedules, and their statuses across entire GitLab groups. 

The Project : [gitlab-ci-dashboard](https://github.com/larscom/gitlab-ci-dashboard)

There is a demo also You can check : [gitlab-ci-dashboard-demo](https://github.com/larscom/gitlab-ci-dashboard)

## Key Features

- View **all pipeline statuses** for your group (success, failure, cancellation)
- Track **all scheduled pipelines** within your group
- **No rate-limiting** from GitLab's API thanks to efficient server-side caching
- Single access token for your entire team, whether read-only or read/write
- Perform advanced actions like **restarting**, **creating**, or **canceling** pipelines (with write token)

## Completed Features

- Display of **latest pipeline statuses** across the group
- Detailed view of **all pipelines** within the group
- Complete list of **scheduled pipelines**
- Direct links to **GitLab pipelines**
- Monitor **individual job statuses**
- Download **artifacts** directly from jobs
- **Search** for projects by name
- **Filter** pipelines by status or tags
- **Mark favorite** projects for quick access
- **Create new pipelines** (requires write token)
- **Retry failed pipelines** (requires write token)
- **Cancel pipelines** (requires write token)

## Planned Features

- Overview of **registries (containers/packages)** within the group
- Feel free to share suggestions for additional features!

## Prerequisites

- A **GitLab server** (API v4)
- **API token** (read-only or read/write scope)
- Docker installed on your local machine

## Installation Guide

1. **Generate your GitLab API token**  
   Create a personal access token [here](https://gitlab.com/-/profile/personal_access_tokens), with either `read_api` (for read-only) or `api` (for write access).

![ci-dash][2]

2. **Run the Docker container:**

```bash
docker run -p PORT:8080 \
  -e GITLAB_BASE_URL=https://gitlab.com \
  -e GITLAB_API_TOKEN=my_token \
  larscom/gitlab-ci-dashboard:latest
```

![ci-dash][1]


You can use also docker-compose : 

```yaml
version: '3.8'

services:
  gitlab-dashboard:
    image: larscom/gitlab-ci-dashboard:latest
    ports:
      - "9980:8080"
    env_file:
      - .env-ci-dashboard
    restart: always
    volumes:
      - ./gitlab-dashboard-data:/data-ci-dashboard 
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:9980"]
      interval: 500s
      timeout: 100s
      retries: 10
```

```.env-ci-dashboard
GITLAB_BASE_URL=https://gitlab.guneycansanli.com
GITLAB_API_TOKEN=XXxxXXXXXxxxXXXXXxxXXXXXxXXXXX
```


3. Visit [http://localhost:PORT](http://localhost:PORT) in your browser

   The dashboard will display all available groups and their associated projects by default.


![ci-dash][3]

---

### Creating / Cancelling / Retrying Pipelines

To enable write actions:

- Set `API_READ_ONLY=false`
- Provide a valid **read/write** GitLab access token

### Disable Write Actions (Hide Ellipsis Button)

To hide the option for write actions:

```env
UI_HIDE_WRITE_ACTIONS=true
```

---

## Prometheus Metrics

Metrics are exposed at:

```
http://localhost:PORT/metrics/prometheus
```

---

## Configurable Environment Variables

| Variable | Type | Description | Required | Default |
|---------|------|-------------|----------|---------|
| `GITLAB_BASE_URL` | string | GitLab instance URL (e.g., `https://gitlab.com`) | ✅ | – |
| `GITLAB_API_TOKEN` | string | GitLab access token | ✅ | – |
| `GITLAB_GROUP_ONLY_IDS` | string | Comma-separated list of group IDs to include (e.g., `123,456`) | ❌ | – |
| `GITLAB_GROUP_SKIP_IDS` | string | Comma-separated list of group IDs to ignore | ❌ | – |
| `GITLAB_GROUP_ONLY_TOP_LEVEL` | bool | Show only top-level groups | ❌ | false |
| `GITLAB_GROUP_CACHE_TTL_SECONDS` | int | Group data cache time-to-live (TTL) in seconds | ❌ | 300 |
| `GITLAB_PROJECT_SKIP_IDS` | string | Comma-separated list of project IDs to ignore | ❌ | – |
| `GITLAB_PROJECT_CACHE_TTL_SECONDS` | int | Project data cache TTL in seconds | ❌ | 300 |
| `GITLAB_PIPELINE_CACHE_TTL_SECONDS` | int | Pipeline data cache TTL in seconds | ❌ | 5 |
| `GITLAB_PIPELINE_HISTORY_DAYS` | int | How many days back to fetch pipeline data | ❌ | 5 |
| `GITLAB_BRANCH_CACHE_TTL_SECONDS` | int | Branch data cache TTL in seconds | ❌ | 60 |
| `GITLAB_SCHEDULE_CACHE_TTL_SECONDS` | int | Schedule data cache TTL in seconds | ❌ | 300 |
| `GITLAB_JOB_CACHE_TTL_SECONDS` | int | Job data cache TTL in seconds | ❌ | 5 |
| `GITLAB_ARTIFACT_CACHE_TTL_SECONDS` | int | Artifact data cache TTL in seconds | ❌ | 1800 |
| `API_READ_ONLY` | bool | Set to `true` to disable write actions | ❌ | true |
| `UI_HIDE_WRITE_ACTIONS` | bool | Hide the ellipsis button for write actions | ❌ | false |
| `SERVER_LISTEN_IP` | string | IP address for the web server | ❌ | `0.0.0.0` |
| `SERVER_LISTEN_PORT` | int | Port for the web server | ❌ | `8080` |
| `SERVER_WORKER_COUNT` | int | Number of worker threads for the server | ❌ | CPU-specific |
| `RUST_LOG` | string | Logging level (`debug`, etc.) | ❌ | – |


---

**GitLab CI Dashboard** is designed to streamline pipeline monitoring across your GitLab groups with minimal configuration required, making it a must-have tool for DevOps teams.


---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/gitlab-ci-dashboard/ci-dashboard-1.jpg
[2]: ../assets/images/gitlab-ci-dashboard/ci-dashboard-2.jpg
[3]: ../assets/images/gitlab-ci-dashboard/ci-dashboard-3.jpg

