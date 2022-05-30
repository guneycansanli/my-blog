---
title: "Nginx Load Balancer Example With Flask App"
layout: post
date: 2022-05-27 21:50
image: ../assets/images/nginx/nginx-1.png
headerImage: true
tag:
- nginx
- docker
- flask
- python
category: blog
author: guneycansanli
description: Load Balancer using with Nginx for Python Flask App in Containers?
---

# Introduction

[Nginx][1] is open source software for web serving, reverse proxying, caching, **load balancing**, media streaming, and more. It started out as a web server designed for maximum performance and stability. In addition to its HTTP server capabilities, NGINX can also function as a proxy server for email (IMAP, POP3, and SMTP) and a reverse proxy and load balancer for HTTP, TCP, and UDP servers. There are a lot of Web Servers like Nginx like Apache HTTP Server, Lighttpd Web Server, OpenLiteSpeed Web Server, Node.JS, Apache Tomcat Web Server, Caddy Web Server, Hiawatha Web Server, Cherokee Web Server and H2O Web Server. There the most famous ones. Every Web servers has some of features.

---

## Why Load Balacing is Important?

Load balancing lets you evenly distribute network traffic to prevent failure caused by overloading a particular resource. This strategy improves the performance and availability of applications, websites, databases, and other computing resources. It also helps process user requests quickly and accurately.

## What we will use?

We need to create a way of working for that project so, using Docker containers can save the time, if we make mistakes it will be easy for us to fix the problems. First step is creating a Docker container environment. We should have an Nginx web server for load balance and also We need to 2 or more web projects for traffic management, in that case We will use Python [Flask][2] Framework. Flask is a small and lightweight Python web framework that provides useful tools and features that make creating web applications in Python easier. It gives developers flexibility and is a more accessible framework for new developers since you can build a web application quickly using only a single Python file. Here I prepared a visual for clerify the prject.

![nginx-2][3]

A client send HTTP request to nginx and load balancer will control the flow of traffic according to the density. We will do it with curl command from client side. According to response We will understand that which server is responsing to us. When you reach a web site which is big or famous web site. You can't know how many they have clones but source code is same so you thing that you are using the same web server with the other people but maybe load balancer may sent you to another server. 

![nginx-4][4]

I created a file for the project then seperated all components before creatiing Docker images for the services. I should be like that

![nodered-tree][5]

We will implement two with flask, and they will be converted with dockerfile and run as a container. So first need is write 2 pytohn flask app.

{% highlight html %}
from flask import request, Flask
import json

app1 = Flask(_name_)
@app1.route('/')

def hello_world():
  
  return 'this is response from app1'

if _name__ == '_main_':
  app1.run(debug=True, host='0.0.0.0')
 
#5001 port response.

{% endhighlight %}

{% highlight html %}
from flask import request, Flask
import json

app2 = Flask(_name_)
@app2.route('/')

def hello_world():
  
  return 'this is response from app2'

if _name_ == '_main_':
  app2.run(debug=True, host='0.0.0.0')
 
#5002 response.
{% endhighlight %}

That images have some requirements so We need to create requirement file for our container environment. 

![nginx-5][6]

I used Flask==2.1.0 in this project. It may chance for your project with the new version some times the older ones can not work.

Now we will make a Dockerfile for each app and create an image for the pythons we have made. If you want, you can use a different image and install python, but I will use python:3 directly. you can use it for both apps.

{% highlight html %}
FROM python:3
COPY ./requirements.txt /requirements.txt
WORKDIR /
RUN pip install -r requirements.txt
COPY . /
ENTRYPOINT [ "python3" ]
CMD [ "app2.py" ]
{% endhighlight %}

![nginx-7][7]

We need create the containers for our flask app so now We should docker command for creating images.

{% highlight html %}
sudo docker build . -t web_app1
{% endhighlight %}

{% highlight html %}
sudo docker build . -t web_app2
{% endhighlight %}

please note, run these commands in the folder where the application is located.

![nginx-8][8]

We should run the images and expose them with the 5000 port then our falsk apps will be ready.

{% highlight html %}
sudo docker run -d -p 5001:5000 web_app1
{% endhighlight %}

{% highlight html %}
sudo docker run -d -p 5002:5000 web_app2
{% endhighlight %}

![nginx-9][9]

and now you can test your applications with curl command.

{% highlight html %}
curl localhost:5001
{% endhighlight %}

{% highlight html %}
curl localhost:5002
{% endhighlight %}
---

### Nginx Configuration  

Now we will configure nginx. We will make the nginx settings on a file called nginx.conf, create one, then put it into the image while dockerizing it. I will use the docker host's IP as the IP. If we want to access it from outside, we can also use the public IP.

![nginx-10][10]

Nginx load balancer configuration shoul be like the picture. We should specified the servers. We used docker host ip in that step because Nginx container has already in the Docker host so it can access directly to another container as in same VPN. The nest step is create a Dcoker file. While Dcoker file is running, İt will use conf file so Dcoker file should be:

{% highlight html %}
FROM nginx
RUN rm /etc/nginx/conf.d/default.conf
COPY nginx.conf /etc/nginx/conf.d/default.conf
CMD ["nginx", "-g", "daemon off;"]
{% endhighlight %}

After the image creattion We should run the image with the port expose for accesing from outside.

{% highlight html %}
sudo docker run -p 8080:80 nginx_lb
{% endhighlight %}

![nginx-11][11]

The last situtation should look like as picture.

---

### Test The Load Balancer

Finally We can test our app. For the testing We should send http request our nginx container so We can use curl command with in a loop.

{% highlight html %}
while true; do curl http://localhost:8080 && sleep 2; done;
{% endhighlight %}

![nginx-12][12]

As you see We got response from 2 another web apps. We sent request to Nginx and then Nginx load balancer control the traffic for wen apps.



[Source][13]

---

[1]: https://www.nginx.com/
[2]: https://flask.palletsprojects.com/en/2.1.x/
[3]: ../assets/images/nginx/nginx-flask2.png
[4]: ../assets/images/nginx/nginx-3.PNG
[5]: ../assets/images/nginx/nginx-tree.PNG
[6]: ../assets/images/nginx/nginx-req.PNG
[7]: ../assets/images/nginx/nginx-dockerfile.PNG
[8]: ../assets/images/nginx/nginx-images.PNG
[9]: ../assets/images/nginx/nginx-container.PNG
[10]: ../assets/images/nginx/nginx-conf.PNG
[11]: ../assets/images/nginx/docker-container-ls.PNG
[12]: ../assets/images/nginx/nginx-load.PNG
[13]: https://alperenhasanselcuk.medium.com/docker-ve-nginx-kullanarak-load-balancer-konfig%C3%BCrasyonu-3aa2fa89e33c

