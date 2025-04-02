---
title: "How to Upgrade GitLab"
layout: post
date: 2025-04-01 12:20
image: ../assets/images/gitlab-upgrade/main.jpg
headerImage: true
tag:
    - gitlab
    - linux
category: blog
author: guneycansanli
description: How to Upgrade GitLab
---

# Upgrading GitLab

## Introduction

GitLab is a widely used web-based Git repository manager that supports collaboration, version control, and CI/CD pipeline automation. The Omnibus GitLab package simplifies installation and upgrades by bundling all dependencies into a single package.

Upgrading GitLab might seem complex, especially for beginners, but by following a structured approach, you can ensure a smooth and secure transition to the latest version.

## Before You Begin

Before upgrading, determine both your current GitLab version and the target version.

GitLab provides an **Upgrade Path Tool**, which suggests the optimal upgrade path and necessary commands. It also highlights key changes and considerations for the new version. Check this URL : [GitLab Upgrade Helper Tool](https://gitlab-com.gitlab.io/support/toolbox/upgrade-path/) 

![gitlab][0]

I will upgrade from **v17.8.2** -> **17.10.1**

### Checking GitLab Version

To check your GitLab version, visit:
```
<GITLAB_URL>/help
```
Alternatively, run the following command on your GitLab server:
```bash
sudo gitlab-rake gitlab:env:info
```

![gitlab][1]

![gitlab][2]


## The Upgrade Process

### Step 1: Update the GitLab Repository

Run the following script to update the package repository:
```bash
curl -s https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
```

![gitlab][3]

### Step 2: Create a Backup

While GitLab automatically takes a backup during the upgrade, it is recommended to manually back up your data:
```bash
sudo gitlab-backup create
```

![gitlab][4]

Additionally, back up critical configuration files manually:
```bash
cp /etc/gitlab/gitlab-secrets.json /backup/location/
cp /etc/gitlab/gitlab.rb /backup/location/
```

![gitlab][5]

### Step 3: Install the New Version

Using the GitLab Upgrade Path Tool, I will install the latest version **gitlab-ce=17.10.1-ce.0**
```bash
sudo apt-get install -y gitlab-ce=17.10.1-ce.0
```

![gitlab][6]

After installation, restart GitLab:
```bash
sudo gitlab-ctl restart
```

## Verifying the Upgrade

### Step 1: Confirm the New Version

Check the installed version by visiting:
```
<GITLAB_URL>/help
```
or by running:
```bash
sudo gitlab-rake gitlab:check
```

![gitlab][7]

### Step 2: Validate Secrets

Ensure encrypted values in the database are decryptable:
```bash
sudo gitlab-rake gitlab:doctor:secrets
```

### Step 3: Verify Background Migrations

Check background migrations status:
```bash
sudo gitlab-rake db:migrate:status
```

To check batched background migrations:
1. Navigate to **Main Menu > Admin**.
2. Go to **Monitoring > Background Migrations**.
3. Ensure all migrations have a **Finished** status before upgrading further.

![gitlab][8]

### Step 4: Check Running Processes

Verify the state and uptime of GitLab components:
```bash
sudo gitlab-ctl status

root@gitlab-debian12:~# gitlab-ctl status
run: alertmanager: (pid 16696) 157s; run: log: (pid 16416) 196s
run: gitaly: (pid 16712) 157s; run: log: (pid 16425) 196s
run: gitlab-exporter: (pid 16733) 156s; run: log: (pid 16414) 196s
run: gitlab-kas: (pid 16766) 145s; run: log: (pid 16420) 196s
run: gitlab-workhorse: (pid 16777) 145s; run: log: (pid 16418) 196s
run: logrotate: (pid 16791) 144s; run: log: (pid 16419) 196s
run: nginx: (pid 16797) 144s; run: log: (pid 16413) 196s
run: node-exporter: (pid 16808) 143s; run: log: (pid 16397) 196s
run: postgres-exporter: (pid 16814) 143s; run: log: (pid 16423) 196s
run: postgresql: (pid 16826) 143s; run: log: (pid 16405) 196s
run: prometheus: (pid 16835) 142s; run: log: (pid 16396) 196s
run: puma: (pid 16847) 141s; run: log: (pid 16415) 196s
run: redis: (pid 16852) 141s; run: log: (pid 16422) 196s
run: redis-exporter: (pid 16859) 141s; run: log: (pid 16406) 196s
run: sidekiq: (pid 16869) 139s; run: log: (pid 16417) 196s
```

## Conclusion

Congratulations! You have successfully upgraded GitLab Omnibus to the latest version. Your instance should now be running smoothly with enhanced features and security improvements.


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

[1]: ../assets/images/file-server/file-1.jpg
[2]: ../assets/images/file-server/file-2.jpg
[3]: ../assets/images/file-server/file-3.jpg
[4]: ../assets/images/file-server/file-4.jpg
[5]: ../assets/images/file-server/file-5.jpg
[6]: ../assets/images/file-server/file-6.jpg





