---
title: "Node-RED HTTP Request (REST API) and E-mail Sending"
layout: post
date: 2022-04-30 13:39
image: ../assets/images/nodered/nodered.png
headerImage: true
tag:
- nodered
- api
- email
category: blog
author: guneycansanli
description: How can we do api call with node-red and using smtp?
---

# Introduction

[Node-RED][1] is developed by IBM and It is a platform which is no need many coding. It based NodeJS so If you know JS you probably will be able to understand much more easily. You can develop IOT projects with Node-RED. It is also open source.

---

## Node-RED Installing

You can install **Node-RED** to your local or in Docker. You can find the official documentation and follow the steps. I will focus to API and E-mail sending today.

## Quick Review

Node-red is working with nodes you can create flows with connecting the nodes. There are many type nodes. 

![nodered-1][2]

You can see the nodes left of the page. If you want to learn about the nodes you can use library and you can read the details about the nodes. Most of time flows start with a trigger It is like start or run your code. It can start manuel also you can set a timer for it. In that flow I started with trigger and than I prepared our http request, We need to use authentication method. Token-based authentication is a protocol which allows users to verify their identity, and in return receive a unique access token. You should create a token or you should have a token for API call. I have already created a token from [NASA][3]. It free and open API.

![nodered-2][4]

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

[1]: https://nodered.org/
[2]: ../assets/images/nodered/nodered-1.PNG
[3]: https://api.nasa.gov/
[4]: ../assets/images/nodered/nodered-2.PNG
[5]: ../assets/images/nodered/nodered-3.PNG
[6]: ../assets/images/nodered/nodered-4.PNG
[7]: ../assets/images/nodered/nodered-5.PNG
[8]: ../assets/images/nodered/nodered-6.PNG
[9]: https://kinsta.com/blog/gmail-smtp-server/
[10]: ../assets/images/nodered/nodered-7.PNG
[11]: ../assets/images/nodered/nodered-7.jpg

