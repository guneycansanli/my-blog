---
title: "Setup dynamic Docker Slave(Agent) and Integrate with Jenkins Master"
layout: post
date: 2024-10-06 14:20
image: ../assets/images/jenkins-docker-agent/main.png
headerImage: true
tag:
    - jenkins
    - ubuntu
    - docker
category: blog
author: guneycansanli
description: Setup dynamic Docker Slave(Agent) and Integrate with Jenkins Master
---

# Introduction

Jenkins offers a robust master-slave architecture that allows for distributed builds, making it highly flexible. In this guide, we'll explore how to configure slave nodes using Docker and connect them to the Jenkins Master.

### Benefits of Using Docker Containers as Jenkins Build Agents:
* Temporary (Ephemeral): Agents can be easily spun up and destroyed as needed.
* Optimized Resource Use: Docker containers help in utilizing system resources more efficiently.
* Customizable Agents: Different builds, such as Java 8 or Java 11, can run on tailored agents.
* Scalability: It allows for easy scaling of the build infrastructure.

---

# Prerequisites

1- Two Ubuntu virtual machines (VMs) are required: one for Jenkins Master and another for the Docker Host.
2- Jenkins Master is already installed and operational.
 * Port 8080 is open in the firewall If You have Firewall enabled.
3- Set up the Docker Host. 
 * Port 4243 is open on the Docker Host machine, If You have Firewall enabled.
 * Ports 32768 to 60999 are also open on the Docker Host machine, If You have Firewall enabled.

---

# Step 1 - Set Up Docker Host with Remote API

Access the Docker host machine and edit the Docker service configuration file. Look for the ExecStart line and replace it with the updated version.

```
sudo vi /lib/systemd/system/docker.service
```


Update the ExecStart line to the following:

```
ExecStart=/usr/bin/dockerd -H tcp://0.0.0.0:4243 -H unix:///var/run/docker.sock
```
![docker-jenkins][1]


Restart the Docker service by running the following commands:

```
sudo systemctl daemon-reload
sudo service docker restart
```
![docker-jenkins][2]


Verify that the API is correctly set up by executing this curl command:

```
curl http://localhost:4243/version
```

![docker-jenkins][3]

---

# Step 2 - Create Jenkins Slave Docker Image

1- Clone the public repository for Jekinds Docker Slave image bulding. The repository forked from another project. We need to clone the code in Docker Host VM. We will build image in Docker Host VM and We will no need to pull image from public registery.

Clone the repository containing the Dockerfile using the command below:

```
git clone https://github.com/guneycansanli/jenkins-docker-slave
cd jenkins-docker-slave
```
![docker-jenkins][4]


Build the Docker image for the Jenkins slave:
```
sudo docker build -t guney-jenkins-slave .
```


To view the list of Docker images available on the host, run:

```
sudo docker images
```
![docker-jenkins][5]



# Step 3 - Set Up Jenkins Server with Docker Plugin


Log in to the Jenkins Master and ensure the Docker plugin is installed.

* Navigate to:
 * Manage Jenkins -> Plugins -> Available Plugins -> Seatch Docker and install.
 * Check installed plugins and make sure Docker Plugin is enabled.

![docker-jenkins][6]


Configuraiton of Docker Slave , Cloud Node

* Navigate to:
 * Manage Jenkins -> Configure Nodes and Clouds
 * Add new cloud.
 * Select Docker and save.

![docker-jenkins][7]

![docker-jenkins][8]


* Enter the Docker host's DNS name or IP address, I will use IP since I have no DNS set-up:

```
tcp://docker_host_dns-or-IP:4243
```

* Ensure that Enabled is selected.
* You can test connection to click test connection.


![docker-jenkins][9]


---

# Step 4 - Set Up Docker Agent Templates

* Navigate to Docker Agent Templates.
* Enter a label, such as "docker-slave", and provide a name for the template.
* Ensure Enabled is selected.
* Specify the name of the Docker image you built earlier on the Docker host.
* Set the Remote file system root to:

```
/home/jenkins
```

![docker-jenkins][10]


* Choose Connect with SSH as the connection method.
* Enter the SSH credentials according to your Dockerfile:
```
Username: jenkins
Password: password
```

![docker-jenkins][11]

Adding New SSH Creds:
![docker-jenkins][12]


* Select Never Pull as the pull strategy since the image is already present on the Docker Host.
* Click Save to apply the changes.

![docker-jenkins][13]

---

# Step 5 - Create a Build Job in Jenkins

* Create a Pipeline Job

![docker-jenkins][14]


* In Jenkins, create a new pipeline job and use the following pipeline script:

```
pipeline {
    agent { 
        label "docker-slave"
    }
    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```

![docker-jenkins][15]

* Click Apply and Save the job.

* Run the Job:
 * Once you build the job, the output will display a message similar to:

![docker-jenkins][16]


* Once your build completed , You can check Docker containers in the dokcer host machine and You will see there is no container running because It was a container for only build purposes and It is terminated by Jenkins after build/pipeline completed.

![docker-jenkins][17]

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

[1]: ../assets/images/jenkins-docker-agent/docker-jenkins-1.jpg
[2]: ../assets/images/jenkins-docker-agent/docker-jenkins-2.jpg
[3]: ../assets/images/jenkins-docker-agent/docker-jenkins-3.jpg
[4]: ../assets/images/jenkins-docker-agent/docker-jenkins-4.jpg
[5]: ../assets/images/jenkins-docker-agent/docker-jenkins-5.jpg
[6]: ../assets/images/jenkins-docker-agent/docker-jenkins-6.jpg
[7]: ../assets/images/jenkins-docker-agent/docker-jenkins-7.jpg
[8]: ../assets/images/jenkins-docker-agent/docker-jenkins-8.jpg
[9]: ../assets/images/jenkins-docker-agent/docker-jenkins-9.jpg
[10]: ../assets/images/jenkins-docker-agent/docker-jenkins-10.jpg
[11]: ../assets/images/jenkins-docker-agent/docker-jenkins-11.jpg
[12]: ../assets/images/jenkins-docker-agent/docker-jenkins-12.jpg
[13]: ../assets/images/jenkins-docker-agent/docker-jenkins-13.jpg
[14]: ../assets/images/jenkins-docker-agent/docker-jenkins-14.jpg
[15]: ../assets/images/jenkins-docker-agent/docker-jenkins-15.jpg
[16]: ../assets/images/jenkins-docker-agent/docker-jenkins-16.jpg
[17]: ../assets/images/jenkins-docker-agent/docker-jenkins-17.jpg

