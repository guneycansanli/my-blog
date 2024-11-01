---
title: "Jenkins Docker Agent with Python and Pipeline Configuration"
layout: post
date: 2024-10-21 14:20
image: ../assets/images/jenkins-python-pipeline/main.jpg
headerImage: true
tag:
    - jenkins
    - ubuntu
    - docker
    - python
category: blog
author: guneycansanli
description: Jenkins Docker Agent with Python and Pipeline Configuration
---

# Jenkins Docker Agent with Python and Pipeline Configuration

### Introduction

In this guide, we will set up a Jenkins Docker agent, push the custom Docker image to Docker Hub, configure Jenkins to use this agent, and implement a Jenkins pipeline. This approach is beneficial for dynamic scaling and using isolated environments for builds.

### Benefits of Using Docker Containers as Jenkins Agents:

*   **Ephemeral Agents**: Easily spun up and destroyed when needed.
*   **Efficient Resource Utilization**: Better resource management using lightweight containers.
*   **Customizable Agents**: Use specific configurations (e.g., Python environment) for each build.
*   **Scalable Infrastructure**: Quickly scale your CI/CD pipeline.

* * *

# Prerequisites

1.  **Jenkins Master**: A Jenkins Master server already installed and running.
2.  **Docker**: Docker installed on the machine where the Jenkins agents will run.
3.  **Docker Hub**: A Docker Hub account for pushing and pulling Docker images.
4.  **GitHub Repository**: A repository to store your Jenkinsfile (SSCM pipeline).

* * *

# Step 1: Build the Docker Agent Image / Creating the Docker image for our Jenkins Agent/Worker Node

Create a Dockerfile that installs **Python3** and the **Docker CLI** to be used in the Jenkins agent. Below is the Dockerfile you can use:

Dockerfile

    FROM jenkins/agent:alpine-jdk17

    USER root

    RUN apk add python3

    RUN apk add py3-pip

    USER jenkins

![py-jenkins][1]


### Build and Push the Image to Docker Hub

1.  **Build the Docker image** using the following command:
    
    `docker build -t jenkins-python-image:python .`
    
2.  **Tag the image** for Docker Hub:
    
    `docker tag jenkinsagent:python gnyscnsnli/jenkinsagent:python`

![py-jenkins][2]
    

4.  **Login** to Docker Hub:
 
    `docker login`

3.  **Push the image** to Docker Hub:
 
    `docker push gnyscnsnli/jenkinsagent:python`
    
![py-jenkins][3]


* * *

# Step 2: Configure Jenkins Cloud for Docker Agent

Now, configure Jenkins to use the Docker agent image as part of its cloud setup.

1.  **Go to Jenkins Dashboard** > Manage Jenkins > Configure System.
2.  Scroll to **Cloud** and click **Add a new cloud** > Choose **Docker**.
3.  **Configure Docker Host**:
    *   **Docker Host URI**: `tcp://<your_docker_host_ip>:4243`
    *   **Enabled**: Check this box.
4.  **Add Docker Agent Template**:
    *   **Labels**: `docker-agent-python-alpine`
    *   **Docker Image**: `gnyscnsnli/jenkinsagent:python`
    *   **Remote FS Root**: `/home/jenkins/agent`
5.  Set the agent to **Use this node as much as possible**.

![py-jenkins][4]

![py-jenkins][5]


* * *

# Step 3: Create Jenkins Pipeline Using SSCM

Here’s an example of a Jenkins pipeline that will run on the Docker agent configured above. First example 

### Jenkinsfile (Pipeline) from GitHub / Setting Up a Jenkins Pipeline for My Python Project on GitHub

Alright, so here’s the plan. We’ve got a Python project in GitHub, complete with a Jenkinsfile right in the repository. The Jenkinsfile is crucial because it’s where we define the steps our pipeline will follow—like checking out the code, setting up a virtual environment, running tests, and deploying if everything looks good.


1. Triggers: Automating Builds

    * GitHub Hook Trigger: This is our main trigger. Every time we push code to GitHub, it pings Jenkins to start a build. Super efficient!
    * Poll SCM (Backup Check): If GitHub’s webhook doesn’t work, Jenkins will poll the repository at intervals. Right now, it’s set to * * * * *, which means every minute. But Jenkins recommends H * * * * to check hourly instead—a more reasonable setting.


2. Pipeline Definition: Source and Script

    * Pipeline Script from SCM: Jenkins will pull the build instructions directly from our GitHub repo.
    * Repo URL: We set the URL to [https://github.com/guneycansanli/jenkins-python-pipeline](https://github.com/guneycansanli/jenkins-python-pipeline), and leave credentials blank (assuming the repo is public).
    * Branch and Jenkinsfile: We’re targeting the master branch and setting the path to our Jenkinsfile. This file holds all the steps Jenkins needs to build, test, and deploy the project.

3. Efficient Checkout

    * Lightweight Checkout: Only the Jenkinsfile is fetched instead of the whole repo. This speeds up the process, especially with larger repositories.

4. Here is my pipeline configuration

    ![py-jenkins][6]

    ![py-jenkins][7]

    ![py-jenkins][8]

5. Make sure Your git repo is public. Here is my project [python-jenkins](https://github.com/guneycansanli/jenkins-python-pipeline)

### Explanation:

*   **Agent**: Specifies the Docker agent with the label `docker-agent-python-alpine`.
*   **Stages**:
    *   **Build**: Installs dependencies using `pip`.
    *   **Test**: Runs Python scripts for testing.
    *   **Deliver**: Placeholder for any delivery steps.

* * *

# Run Pipeline

1. Once you completed the steps You can trigger your pipeline...

    ![py-jenkins][9]


# Conclusion

By following these steps, you have successfully created a Jenkins Docker agent with Python, configured it in Jenkins, and set up a Jenkins pipeline. This setup provides a scalable, customizable, and efficient CI/CD environment.

* * *

You can copy this directly into your project, making sure to replace placeholders like `<your_docker_host_ip>` with your actual values!
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

[1]: ../assets/images/jenkins-python-pipeline/py-jenkins-1.jpg
[2]: ../assets/images/jenkins-python-pipeline/py-jenkins-2.jpg
[3]: ../assets/images/jenkins-python-pipeline/py-jenkins-3.jpg
[4]: ../assets/images/jenkins-python-pipeline/py-jenkins-4.jpg
[5]: ../assets/images/jenkins-python-pipeline/py-jenkins-5.jpg
[6]: ../assets/images/jenkins-python-pipeline/py-pipeline-6.jpg
[7]: ../assets/images/jenkins-python-pipeline/py-pipeline-7.jpg
[8]: ../assets/images/jenkins-python-pipeline/py-pipeline-8.jpg
[9]: ../assets/images/jenkins-python-pipeline/py-pipeline-9.jpg

