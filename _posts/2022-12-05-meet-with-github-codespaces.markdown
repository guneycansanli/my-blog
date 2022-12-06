---
title: "Meet With GitHub Codespaces"
layout: post
date: 2022-12-05 20:20
image: ../assets/images/gitCodespace/gitCodespace.png
headerImage: true
tag:
    - git
    - new
    - useful
    - development
category: blog
author: guneycansanli
description: New Feature GitHub Codespaces
---

# What is GitHub Codespaces?

**GitHub** **Codespaces** is a service that provides developers with on-demand access to a secure development environment running a given codebase (Git repository) on a remote server. It also allows the developer to debug, maintain and make changes via a full-featured (with syntax highlighting, themes, extensions, version control, etc.) browser-based or locally installed Visual Studio Code IDE from their local machine.

**GitHub** Codespaces uses virtualization tools under the hood, such as containers and virtual machines. Codespaces can be thought of as having a backend part and a frontend part—the usual client-server architecture, nothing fancy. The backend part is where all the application code is usually run in a Docker container (you don’t need to install Docker to use Codespaces) with the required runtime and tools, and the frontend part is the IDE, which interfaces with the running application.

---

## How to Use ?

You should follow that steps for create a new codespace.

-   Open your target repository.
-   Click the Code section.
-   Create Codespace on your branch.

![Codespace][2]

-   After click button you will see that your browser open a new tab.
-   You will see an IDE and It will be ready for using with your source code.
-   You can inspect the IDE.
-   Environment try to install the all dependencies for your project.

![Codespace][3]

-   After a while You can ready to build, run or any code development.

![Codespace][4]

-   I had a sample Jekyll blog source code and after the ready to build I serve it with the Jekyll serve command.

![Codespace][5]

-   Finally I can access to my running web project.

![Codespace][6]

With the new GitHub feature. We can develop or test our code without our work PC. Codespaces is really usefull I am sure We will use it We will use it more in the near future.

Thanks for reading :)

---

[2]: ../assets/images/gitCodespace/gitCodespace1.jpg
[3]: ../assets/images/gitCodespace/gitCodespace2.jpg
[4]: ../assets/images/gitCodespace/gitCodespace3.jpg
[5]: ../assets/images/gitCodespace/gitCodespace4.jpg
[6]: ../assets/images/gitCodespace/gitCodespace5.jpg
