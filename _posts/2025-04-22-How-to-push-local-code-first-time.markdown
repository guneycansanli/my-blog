---
title: "How to Push a Local Project to a New GitLab Repository"
layout: post
date: 2025-04-22 11:00
image: ../assets/images/gitlab-code/main.jpg
headerImage: true
tag:
    - gitlab
    - git
category: blog
author: guneycansanli
description: How to Push a Local Project to a New GitLab Repository
---

# How to Push a Local Project to a New GitLab Repository

If you have a local project without a Git repository, and you want to push it to GitLab, follow these simple steps.

---

## Step 1: Create a New GitLab Repository

1. Go to [GitLab](https://gitlab.com) or your self-hosted GitLab instance.
2. Log in to your account.
3. Click the **"+"** icon or navigate to **Projects > New project**.
4. Choose **"Create blank project"**.
5. Fill in the following details:
   - **Project name**: Enter a unique name for your project.
   - **Visibility level**: Choose between **Private**, **Internal**, or **Public** based on your project’s needs.
6. Click **"Create project"**.

---

## Step 2: Initialize Git in Your Project Directory

Open your terminal and navigate to the folder containing your project:

```bash
cd /path/to/your/project
git init
```

This command initializes a new Git repository in your local project folder.

---

## Step 3: Add and Commit Your Files

To prepare your local project for the initial push, add all the files to the Git staging area and make an initial commit:

```bash
git add .
git commit -m "Initial commit"
```

The `git add .` command stages all files in the current directory for the commit. The `git commit -m "Initial commit"` command commits the staged files with a message describing the commit.

---

## Step 4: Add the Remote GitLab Repository

Once your GitLab repository is created, copy the repository URL from GitLab (e.g., `https://gitlab.com/username/projectname.git` or your local GitLab instance URL). Then, add it as a remote in your local repository:

```bash
git remote add origin https://gitlab.com/username/projectname.git
```

This command links your local Git repository with the remote GitLab repository, allowing you to push your changes.

---

## Step 5: Authenticate with HTTPS

### Option 1: Login with Username and Personal Access Token (Recommended)

GitLab no longer allows password authentication for HTTPS. Instead, you must use a **Personal Access Token (PAT)**.

1. Go to **User > Preferences > Access Tokens**.
2. Create a new token with the following scopes:
   - `read_repository`
   - `write_repository`
3. Copy the token (you won't be able to see it again).
4. When prompted for authentication by Git:
   - Enter your **GitLab username**.
   - Use the **Personal Access Token** as your **password**.

To avoid having to input the token each time, you can configure Git to cache your credentials:

```bash
git config --global credential.helper cache
```

Or you can store them permanently (note: this stores your credentials in plaintext, so use it cautiously):

```bash
git config --global credential.helper store
```

---

### Option 2: Login with Username and Password (Self-Hosted GitLab Only)

If you're using a **self-hosted GitLab** instance where basic authentication is still enabled, you can enter your **GitLab username** and **password** when prompted for authentication.

**Note**: Basic password authentication is deprecated and not recommended on **GitLab.com**. Always prefer using **Personal Access Tokens** or SSH for security.

---

## Step 6: Push Your Code to GitLab

### How to Fix "Non-Fast-Forward" Errors

If you encounter the following error when attempting to push your code:

```bash
! [rejected]        main -> main (non-fast-forward)
error: failed to push some refs to 'http://192.168.1.171/guneycansanli/fast-api-ai.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
```

This error occurs when your local branch is behind the remote branch. To resolve it, follow these steps:

1. **Pull the latest changes from the remote** to update your local branch:

   ```bash
   git pull origin main --rebase
   ```

   This command pulls the changes from the remote repository and rebases your local changes on top of the remote branch, avoiding a merge commit.

2. If you encounter a conflict, such as the following message:

   ```bash
   Auto-merging README.md
   CONFLICT (add/add): Merge conflict in README.md
   error: could not apply 074ef52... init commit
   hint: Resolve all conflicts manually, mark them as resolved with
   hint: "git add/rm <conflicted_files>", then run "git rebase --continue".
   hint: You can instead skip this commit: run "git rebase --skip".
   hint: To abort and get back to the state before "git rebase", run "git rebase --abort".
   ```

### How to Resolve Merge Conflicts During Rebase

1. Open the conflicted file(s), for example, `README.md`.
2. Git will mark the conflicted sections like this:

   ```bash
   <<<<<<< HEAD
   Your local changes
   =======
   Remote changes
   >>>>>>> commit-hash
   ```

3. Manually resolve the conflict by deciding which changes to keep (or merge the changes). Remove the conflict markers (`<<<<<<<`, `=======`, and `>>>>>>>`) once you've resolved the conflict.
4. After resolving conflicts, stage the changes:

   ```bash
   git add README.md
   ```

5. Continue the rebase process:

   ```bash
   git rebase --continue
   ```

6. If you encounter more conflicts, repeat the steps. Once the rebase is complete, push your changes to GitLab:

   ```bash
   git push -u origin main
   ```

---

## GitLab Tips

- **Add a README**: Include a `README.md` file in your project to explain what your project does, its dependencies, how to set it up, and any other important information.
- **Use SSH for Authentication**: Using SSH keys instead of HTTPS can be more secure and convenient since you won't need to enter a password or Personal Access Token each time. Here's how to set up SSH:
  1. Generate an SSH key pair:
     ```bash
     ssh-keygen -t ed25519 -C "your_email@example.com"
     ```
  2. Add your public key to GitLab under **Preferences > SSH Keys**.
  3. Update your remote URL:
     ```bash
     git remote set-url origin git@gitlab.com:username/projectname.git
     ```
- **Protect Main Branches**: In project settings, protect your `main` branch to prevent force pushes or direct commits. This helps maintain a stable codebase.
- **Use `.gitignore`**: Avoid committing unnecessary files such as build artifacts, log files, or environment-specific configurations. You can create a `.gitignore` file to specify files and directories to exclude.
- **Enable CI/CD**: Automate your testing, deployment, and other workflows by adding a `.gitlab-ci.yml` file to your project.
- **Use Issues & Merge Requests**: GitLab has powerful tools for tracking bugs, tasks, and feature requests with its **Issues** system. Merge Requests allow you to propose changes to your project and collaborate with others.
- **Add Project Badges**: Show the status of your builds, tests, or coverage in your `README.md` with project badges.

---


---

Thanks for reading!

—

**Guneycan Sanli**
