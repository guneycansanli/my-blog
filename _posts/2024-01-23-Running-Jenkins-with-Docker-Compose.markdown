---
title: "Running jenkis with Docker Compose on Ubuntu 20.04.6 LTS (Focal Fossa)"
layout: post
date: 2024-01-23 14:20
image: ../assets/images/jenkins-docker-compose/main.png
headerImage: true
tag:
    - linux
    - ubuntu
    - docker
category: blog
author: guneycansanli
description: Running jenkis with Docker Compose
---

# Before Installation of Jenkins with Docker compose?

- I assume that You have already installed docker and docker compose for this tutorial.
- I will run the container as root user, this is not a best practise for any docker image. 

# Prepare Docker Compose File for Jenkins

- Let's create a new directory for your Jenkins docker compose yaml file and Create a yaml file.
{% highlight raw %}
mkdir docker-compose
cd docker-compose
touch docker-compose.yaml
{% endhighlight %}

- Edit your yaml file and You can use below compose file.
{% highlight raw %}
version: '3.8'
services:
  jenkins:
    image: jenkins/jenkins:lts
    restart: always
    privileged: true
    user: root
    ports:
      - 8080:8080
      - 50000:50000
    container_name: jenkins
    volumes:
      - /home/root/jenkins_compose/jenkins_configuration:/var/jenkins_home
      - /var/run/docker.sock:/var/run/docker.sock
{% endhighlight %}

- Save the yaml file.

# Run Docker Compose and Setup Jenkins

- We are ready for run compose file. Run docker-compose in the directory where you placed docker-compose.yaml.
{% highlight raw %}
docker-compose up -d
{% endhighlight %}
![jen][1]

- You can access Jenkis via 8080 port now and You will see unlock page.
- You can find the initial password from Docker container logs or from secrets file.
- Go bask your terminal and check container logs.
{% highlight raw %}
docker logs jenkins
{% endhighlight %}
![jen][2]

- Copy and paste the password.
![jen][3]

- Select Install Suggested Plugins on the next page. When Jenkins finishes, it will prompt you for a new admin user and password. Enter a user name and password and click Save and Continue.

- You may create your user.
![jen][4]

- The next page gives you a chance to change the host name of your controller. For this tutorial, you can accept the default and click Save and Finish.
![jen][5]


- Click start Jenkins. 
- And We are done for setup, We can set up an agent now!


# Adding Jenkins Agent With Docker Compose

- To make nodes status ready, we must install POD network addons like Calico or flannel.
- An agent is typically a machine, or container, which connects to a Jenkins controller and executes tasks when directed by the controller.
- We will add a new container as an agent of Jenkins.
- Before adding or running agent We need to create SSH key This key will be used controller to access the agent via SSH.
- Use ssh-keygen to create a key.
{% highlight raw %}
ssh-keygen -t rsa -f jenkins_agent
{% endhighlight %}

- This command creates two files: jenkins_agent, which holds the private key, and jenkins-agent.pub, the public key.
![jen][6]

- We will give the key to controller access to the agent.
- Start with the Manage Jenkins menu.
![jen][8]

- Then go to Manage Credentials.
![jen][9]

- Click Jenkins under Stores scoped to Jenkins.
- Then click Global credentials.
![jen][10]

- Finally, click Add Credentials in the menu on the left.
![jen][11]

- Set these options on this screen.
1. Select SSH Username with private key.
2. Limit the scope to System. This means the key can’t be used for jobs.
3. Give the credential an ID.
4. Provide a description.
5. Enter jenkins for a username. Don’t use the username used to create the key.
6. Under Private Key,  check Enter directly.
7. And, paste the contents of jenkins_agent in the text box.

- Click Create.
![jen][12]

- We are ready for setting up the agent.
- We will use jenkins_agent.pub in docker-compose.yaml.
- Add a new service to docker compose file.

![jen][13]
- New service-container with jenkins/ssh-agent:jdk11 image and options similar to the controller, except you’re exposing the SSH port, 22
- We addedenvironment variable with the public key. Add the exact contents of the text file, including the ssh-rsa prefix at the beginning. Don’t enclose it in quotes.


- And now We are ready to run again our compose file but before that We need to top Docker Compose.
{% highlight raw %}
docker-compose down
docker-compose up -d
{% endhighlight %}
![jen][14]

- We need to back Jenkins, click Manage Jenkins, and select Nodes
![jen][15]

- Click New Node.
![jen][16]

- Now define your Jenkins agent. Give a node name and select permanent agent.

- Top of the form, give the agent name and set the Remote root directory to /home/jenkins/agent.
- Then, in the next part of the form, select Use this node as much as possible under Usage.
- Under Launch method, select Launch agents via SSH.
- You can create label as agent
- For Host, enter agent. Each container can reach the others by using their container names as hostnames.
- Next, click the dropdown under Credentials and select the one you just defined.
- Now, under Host Key Verification Strategy, select Non verifying Verification Strategy.

![jen][17]
- Click Advanced on the right.
- This opens the advanced configuration page. You need to change one setting here. Set the JavaPath to /usr/local/openjdk-11/bin/java.
- And Also You can specify agent timeout values.
![jen][18]

- Click Save at the bottom, and now it’s time to watch the agent start.
- Jenkins will take you back to the node list. Click on your new node name.
![jen][19]

- Then click on Log in the dropdown menu.
![jen][20]


- The most important entry you’ll see is Agent successfully connected and online.
- But if you look at the other entries, you’ll see plenty of information about your agent, including the SSH key. 
![jen][21]



---
---

> :blush: :star: :boom: :fire: :+1: :eyes: :metal:

---

Guneycan Sanli.

---

[1]: ../assets/images/jenkins-docker-compose/jenkins1.jpg
[2]: ../assets/images/jenkins-docker-compose/jenkins2.jpg
[3]: ../assets/images/jenkins-docker-compose/jenkins3.jpg
[4]: ../assets/images/jenkins-docker-compose/jenkins4.jpg
[5]: ../assets/images/jenkins-docker-compose/jen5.jpg
[6]: ../assets/images/jenkins-docker-compose/jen6.jpg
[7]: ../assets/images/k8s/k8s-1-3.jpg
[8]: ../assets/images/jenkins-docker-compose/jen8.jpg
[9]: ../assets/images/jenkins-docker-compose/jen9.jpg
[10]: ../assets/images/jenkins-docker-compose/jen10.jpg
[11]: ../assets/images/jenkins-docker-compose/jen11.jpg
[12]: ../assets/images/jenkins-docker-compose/jen12.jpg
[13]: ../assets/images/jenkins-docker-compose/jen13.jpg
[14]: ../assets/images/jenkins-docker-compose/jen14.jpg
[15]: ../assets/images/jenkins-docker-compose/jen15.jpg
[16]: ../assets/images/jenkins-docker-compose/jen16.jpg
[17]: ../assets/images/jenkins-docker-compose/jen17.jpg
[18]: ../assets/images/jenkins-docker-compose/java18.jpg
[19]: ../assets/images/jenkins-docker-compose/jen19.jpg
[20]: ../assets/images/jenkins-docker-compose/jen20.jpg
[21]: ../assets/images/jenkins-docker-compose/jen21.jpg
