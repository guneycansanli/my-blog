---
title: "Jujutsu (jj) -- a simple, intuitive version control system"
layout: post
date: 2025-11-02 11:00
image: ../assets/images/jj/main.jpg
headerImage: true
tag:
    - git
    - jj
    - verison-control
category: blog
author: guneycansanli
description: Jujutsu (jj) -- a simple, intuitive version control system
---

# Jujutsu (jj) -- A Simpler Future for Version Control (Git)

## Introduction
Git is powerful — there’s no debate about that.  
It’s the foundation of modern software collaboration and one of the most influential developer tools ever made.  

But let’s be real: **Git can also be frustrating**.  
Branches, rebases, detached HEADs, and merge conflicts often make simple tasks feel unnecessarily complex.  

That’s where **[Jujutsu (jj)]**(https://jj-vcs.github.io/jj/latest/) comes in.  

Jujutsu rethinks version control while staying **100% compatible with Git**. It’s not here to replace it, but to make it *feel human*.  
Think of it as Git’s smarter, calmer sibling — keeping all the power, but removing the friction.  

- Larger image sizes  
- Longer pull/push times  
- A larger attack surface  
- More CVEs to track

---

## Common Git Pain Points

Git’s design is a masterpiece, but its user experience hasn’t aged as gracefully.  
If you’ve ever used Git in a team environment, these problems will sound familiar:

- **Branch Management:**  
  Keeping track of multiple branches, features, and hotfixes can quickly become a mess.

- **Editing Commits:**  
  Need to fix a typo or change a commit message from a few days ago?  
  Time to open a rebase session and cross your fingers.

- **Conflict Resolution:**  
  Git stops everything when a conflict appears — even if you’d rather fix it later.

- **Detached HEAD Fear:**  
  A simple checkout can leave you stranded in “detached HEAD” land, unsure of where you are.

Jujutsu tackles all of this with a cleaner mental model — one that focuses on **what developers actually do every day**.

---

## The Jujutsu - jj Way

At its core, Jujutsu simplifies how we think about version control.  
It still works with your `.git` folder, your branches, and your history — but with a more intuitive interface.

Let’s look at how it changes the experience.

---

## Simplified Mental Model

In Jujutsu, **everything starts with commits**.  
You don’t need to think about branches right away. You simply create commits and evolve them over time.  

Branches are just convenient *labels*, not the core workflow.

For example, instead of juggling branches like `feature/login`, `feature/login-rework`, or `fix/login-bug`,  
you simply focus on refining the *commits* that make up the login feature.  

When you’re ready to share it, jj can automatically create a branch for you, using a **stable change ID** that never changes — even if you move or modify commits later.

```bash
# Create a new commit in jj
jj new -m "Implement user login"
# Make changes to it directly
jj edit @
# Add a description or update message
jj describe
```

Your workflow stays focused on progress, not branch names.

--

## Editing Commits Without Fear

Git makes editing past commits feel dangerous. With Jujutsu, editing history is normal and safe.

Need to fix a typo in a commit from last week? No problem.

```bash
jj edit <commit-id>
# Edit your files as usual
jj describe
```

Once you save, jj automatically reorders and updates the commit tree. You can even modify multiple commits in a chain — and the rest of your history adjusts accordingly.

---

## Real-World Example

Let’s say you committed a function as login_user() but later renamed it to sign_in_user(). With Git, you’d have to rebase, resolve conflicts, and reword commits. With jj:

```bash
jj log
# Find the commit where you added login_user()
jj edit <commit-id>
# Rename function and save
jj describe -m "Refactor: rename login_user() -> sign_in_user()"
jj log
```

Done. The history stays clean and readable.


## Conflict Resolution on Your Terms

One of jj’s most refreshing ideas is how it handles merge conflicts. In Git, conflicts block your progress. In jj, they’re just part of your commit tree.
Conflicts are stored as structured data, not text markers, so you can resolve them later — when you’re ready.

```bash
# Merge another branch into your work
jj rebase -s main
# If there's a conflict, jj records it as data
jj status
# Resolve it anytime later
jj resolve
```

Even better: when you resolve a conflict once, jj remembers and automatically applies that resolution to future commits that hit the same conflict.
No more repetitive fixing of the same merge issue.

## Seeing the Big Picture

Jujutsu’s **jj log** command is one of its hidden gems.
It visualizes your project’s history in a clear, tree-based layout — far more readable than **git log --graph**.

Each commit shows:
- A permanent change ID (never changes)
- A Git hash (for compatibility)
- Your working copy, marked with @

Example:
```bash
jj log
```


Output:
```bash
o  78f2a3a0 (main)  Add API tests
|
o  4a1d9c51         Refactor auth module
|
@  e9b7c112         Implement user login
```

You instantly see where you are, what’s next, and how your commits connect.


## Real-Life Use Case: Collaborative Refactoring

Imagine your team is refactoring a shared module.
Three developers are editing the same files, but at different points in history.
In Git, that’s a recipe for merge conflicts and rebases gone wrong.

In Jujutsu:

1. Everyone keeps their own commit tree.
2. Conflicts are stored safely, not blocking progress.
3. Once one person resolves a conflict, jj remembers the resolution pattern.

That means future merges automatically skip the same problem.

```bash
jj git fetch
jj rebase -s main
# Resolve once
jj resolve
# The fix now applies to all descendants automatically
```

It feels like version control that **learns** as you go.

## No Lock-In: Use jj and Git Together

This is one of jj’s best features — it works with Git, not against it. You can initialize it alongside your Git repo in colocation mode:

```bash
jj git init --colocate
```

That creates a .jj folder next to your existing .git directory. From then on, you can use either tool at any time.
Want to switch back to pure Git? Just delete **.jj** Your Git repo stays exactly as it was. This makes jj risk-free to try.


## Backed by Google, Built with Rust

Jujutsu isn’t a side project — it’s built by **Martin von Zweigbergk**, a Google engineer, and written in Rust. It’s already being used internally at Google, powering some of the largest codebases in the world.

Like Rust itself, jj’s design focuses on safety, performance, and developer happiness. Its approach scales from small solo projects to massive enterprise repositories.

## Getting Started with Jujutsu

Note: First make sure that you have a Rust version >= 1.88 and that the build-essential package is installed by running something like this

```bash
#Debian Based 
apt-get install build-essential

#Install Rust and Cargo
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

```bash
# To install the *prerelease* version from the main branch
cargo install --git https://github.com/jj-vcs/jj.git --locked --bin jj jj-cli

# To install the latest release
cargo install --locked --bin jj jj-cli
```

![jj][1]


Please check other installation methods for other distros. [jj installation](https://jj-vcs.github.io/jj/latest/install-and-setup)


Verify JJ version:

```bash
jj --version
```

![jj][2]


If you want to do some tutorials You can find good examples in official JJ page: [jj tutorial](https://jj-vcs.github.io/jj/latest/tutorial/)


---

Thanks for reading!

—

**Guneycan Sanli**


[1]: ../assets/images/jj/jj-1.jpg
[2]: ../assets/images/jj/jj-2.jpg





