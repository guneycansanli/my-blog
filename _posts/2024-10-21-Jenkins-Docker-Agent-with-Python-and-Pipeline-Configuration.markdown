---
title: "Jenkins Docker Agent with Python and Pipeline Configuration"
layout: post
date: 2024-10-21 14:20
image: ../assets/images/jenkins-docker-agent/
headerImage: true
tag:
    - jenkins
    - ubuntu
    - docker
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

# Step 1: Build the Docker Agent Image

Create a Dockerfile that installs **Python** and the **Docker CLI** to be used in the Jenkins agent. Below is the Dockerfile you can use:

dockerfile

    pipeline {
    agent { 
        node {
            label 'docker-agent-python-alpine'
        }
    }
    triggers {
        pollSCM('* * * * *') // This will poll SCM every minute
    }
    stages {
        stage('Build') {
            steps {
                echo "Building.."
                sh '''
                cd myapp
                pip install -r requirements.txt --break-system-packages
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing.."
                sh '''
                cd myapp
                python3 hello.py
                python3 hello.py --name=Guney
                '''
            }
        }
        stage('Deliver') {
            steps {
                echo 'Deliver....'
                sh '''
                echo "doing delivery stuff.."
                '''
            }
        }
      }
    }


### Build and Push the Image to Docker Hub

1.  **Build the Docker image** using the following command:
    

    
    `docker build -t jenkinsagent:python .`
    
2.  **Tag the image** for Docker Hub:
    
    bash
    
    Copy code
    
    `docker tag jenkinsagent:python gnyscnsnli/jenkinsagent:python`
    
3.  **Push the image** to Docker Hub:
 
    
    `docker push gnyscnsnli/jenkinsagent:python`
    

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

* * *

# Step 3: Create Jenkins Pipeline Using SSCM

Here’s an example of a Jenkins pipeline that will run on the Docker agent configured above.

### Jenkinsfile (Pipeline)

    pipeline {
    agent { 
        node {
            label 'docker-agent-python-alpine'
        }
    }
    triggers {
        pollSCM('* * * * *') // This will poll SCM every minute
    }
    stages {
        stage('Build') {
            steps {
                echo "Building.."
                sh '''
                cd myapp
                pip install -r requirements.txt --break-system-packages
                '''
            }
        }
        stage('Test') {
            steps {
                echo "Testing.."
                sh '''
                cd myapp
                python3 hello.py
                python3 hello.py --name=Guney
                '''
            }
        }
        stage('Deliver') {
            steps {
                echo 'Deliver....'
                sh '''
                echo "doing delivery stuff.."
                '''
            }
        }
      }
    }


### Explanation:

*   **Agent**: Specifies the Docker agent with the label `docker-agent-python-alpine`.
*   **Stages**:
    *   **Build**: Installs dependencies using `pip`.
    *   **Test**: Runs Python scripts for testing.
    *   **Deliver**: Placeholder for any delivery steps.

* * *

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

[1]: ../assets/images/jenkins-docker-agent/
