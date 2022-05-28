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

Inside the node you can use Java Script syntax code. Important point is that Node-RED uses *msg* properties by default, *msg.payload* is an object and most of nodes recognize the variable and implement it.

![nodered-3][5]

We can think of the entries in this picture as variables, what does it mean? It means I can give a value for *url* in the previous node which is function nodes. If you check the picture I specified msg.url with the value. It comes in the node as *msg.url* and assigne to the text entry within the node. API request method should setted by from you and You need to select your node's return type If you want you use your variable after request you should select return as Json object. It is not necessary you can convert it in next functions node but It can be more useful.

![nodered-4][6]

Don't forget the what we did before they are storing by *msg.payload* variable even We are preparing next nodes, some of times we can lose them if the the current node return a new *msg.payload* variable. Next step is preparing the our e-mail template you can prepare html file also possible diffrent formatting in that node. *Msg.property* is your return value so You will use it in next node.

![nodered-5][7]

The last function node is for email configuration. As I mentioned before every node has diffrent value and they store some spesific variables. Before config You should intall the email plugin for using SMTP and sending emil with it. You can use setting than search plugin for downloading the email nodes. Node-RED is open source, developers add new features and plugins for it. The *msg.payload* variable is using for email body in the email nodes. You need to set your template as *msg.payload*, function nodes is using for some coding.

---

# Setting the SMTP for Email Sending

![nodered-6][8]

I will use the gmail for SMTP. What does SMTP mean?. SMTP stands for Simple Mail Transfer Protocol, and it’s an application used by mail servers to send, receive, and/or relay outgoing mail between email senders and receivers. For the gmail SMTP you can follow [this article][9]. After complated the STMP configureation you should Just enter the information in the picture. By default the server works like this. When everything is done you should just click the trigger for starting the flow.

---

![nodered-7][10]

Yes You did it! Check your mail inbox you have APOD Astronomy Picture of the Day. Today's Picture is so cool is not it!!!

![nodered-8][11]

---

[1]: https://www.nginx.com/
[2]: https://flask.palletsprojects.com/en/2.1.x/
[3]: ../assets/images/nginx/nginx-flask2.png
[4]: ../assets/images/nginx/nginx-3.PNG
[5]: ../assets/images/nodered/nodered-3.PNG
[6]: ../assets/images/nodered/nodered-4.PNG
[7]: ../assets/images/nodered/nodered-5.PNG
[8]: ../assets/images/nodered/nodered-6.PNG
[9]: https://kinsta.com/blog/gmail-smtp-server/
[10]: ../assets/images/nodered/nodered-7.PNG
[11]: ../assets/images/nodered/nodered-7.jpg

