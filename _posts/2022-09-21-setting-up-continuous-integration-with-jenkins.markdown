---
title: "Setting Up Continuous Integration with Jenkins"
layout: post
date: 2022-09-21 20:20
image: ../assets/images/jenkins/jenkins.png
headerImage: true
tag:
- jenkins
- devOps
- CI/CD
- job
- pipeline
category: blog
author: guneycansanli
description: Setting Up Continuous Integration with Jenkins
---

# What is Jenkins?

[Jenkins][1] is an open-source automation tool written in Java with plugins built for Continuous Integration purposes. Jenkins is used to build and test your software projects continuously making it easier for developers to integrate changes to the project, and making it easier for users to obtain a fresh build. It also allows you to continuously deliver your software by integrating with a large number of testing and deployment technologies.

---

## Setting Up Continuous Integration with Jenkins

With this exercise We will try to do;

* Set up Jenkins using Docker.
* Take a walkthrough of the Jenkins web interface and essential configurations and start creating freestyle and maven jobs.
* Creating a Job with Jenkins.

### Set Up Jenkins with Docker

You need to install or set up Docker before this exercise so We will continue Jenkins image pulling and setting up with a container also with the exoesing with the port from Docker host. You can use any Jenkins version for setting up and I will share a git repository for source code. You will be running a Jenkins container on your Docker host by using the Jenkins image with version 2.263.1-slim. Use the  following commands to clone the devops-repo directory and launch the Jenkins container.

After the git clone the repo with your VM you can reach the source files for using Docker Compose and you can follow the commands for running **Jenkins** container.


You can reach with that link: [DevOps Repository][2]


**cd devops-repo/setup** 

Inside the devops-repo/setup directory is the docker-compose.yml and the Dockerfile
that we will be working with. The Jenkins container image is the 2.263.1-slim version.

**docker-compose build**
**docker-compose up -d**


![Jenkins1][3]


Yes! you can reach the Jenkins container from your local host (localhost:8080) now. You need to pass Password cridential page so for that you should fetch the initialAdminPassword use the following command:

![Jenkins6][6]


* docker compose logs jenkins

and paste it into the Jenkins UI to unlock. In the next step, choose ‘Install suggested plugins’ to configure the default plugins automatically. You can select Install suggested plugins then You will wait for setting up.


![Jenkins2][4]

Jenkins need to seeting up some configurations just follow the the steps and wait for being ready. You will the page of admin user creation.

![Jenkins3][5]

You can create an admin user for managing your **Jenkins**. When you want to reach to Jenkins You will use your admin user cridentials.

![Jenkins7][7]


### Creating Your First Jenkins Job

After setting up **Jenkins**, it's now time to create your first Jenkins job and run it.You will create a Jenkins job to build the voting app that you are about to fork. Visit [example-voting-app][8] on Github and fork the repository onto your git account.

Once you fork the repository, go to Jenkins, top of the page, and click on Create a Job or New Item. Choose the Freestyle project with the name of job-01 and click OK to configure your job. You will get the job configuration page where you can choose the following options. 

Steps:
* Go to job-01 configuration page, add the description of your job.
* Under Source Code Management choose git and provide your project repository url copied from your GitHub repo.


![Jenkins11][9]


![Jenkins12][10]


![Jenkins13][11]


![Jenkins14][12]


* Next, select the Build tab and choose the Execute shell from the Add build step dropdown menu. Paste the following commands into the Command box: **ls -ltr**

* Once you fill in the build step, save the job configuration page.

* Go to your project page, choose the Build Now option to build your job. Once your build
is finished, click on your build and check build execute status


![Jenkins15][13]


* Every build will have logs stored for it.

* Observe the color coding in the build status. If your job is successful, it will show as blue,
if it fails it will turn ***red***.

**TIP**: **If it all goes well, try to break the job to understand what would happen if the job fails. Fix it**
**to continue. Hint -> open the configuration file and replace the sleep 10 command with exit**


![Jenkins17][15]


Yes You created your first Jenkins Job...



For more informations you can follow ----> [info][16]



---

[1]: https://www.jenkins.io/
[2]: https://github.com/guneycansanli/devops-repo.git
[3]: ../assets/images/jenkins/jenkins9.PNG
[4]: ../assets/images/jenkins/jenkins1.PNG
[5]: ../assets/images/jenkins/jenkins2.PNG
[6]: ../assets/images/jenkins/jenkins10.PNG
[7]: ../assets/images/jenkins/jenkins3.PNG
[8]: https://github.com/generalwork/example-voting-app.git
[9]: ../assets/images/jenkins/jenkins11.PNG
[10]: ../assets/images/jenkins/jenkins12.PNG
[11]: ../assets/images/jenkins/jenkins13.PNG
[12]: ../assets/images/jenkins/jenkins14.PNG
[13]: ../assets/images/jenkins/jenkins15.PNG
[14]: ../assets/images/jenkins/jenkins16.PNG
[15]: ../assets/images/jenkins/jenkins17.PNG
[16]: https://www.guru99.com/create-builds-jenkins-freestyle-project.html