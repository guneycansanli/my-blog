---
title: "Configuring a VM on GCP"
layout: post
date: 2022-09-01 20:20
image: ../assets/images/gcp/gcp1.png
headerImage: true
tag:
- google
- devOps
- cloud
- gcp
- vm
category: blog
author: guneycansanli
description: Configuring a VM on GCP?
---

# Introduction

[Google Cloud Platform][1]  provides infrastructure as a service, platform as a service, and serverless computing environments.
In April 2008, Google announced App Engine, a platform for developing and hosting web applications in Google-managed data centers, which was the first cloud computing service from the company. The service became generally available in November 2011. Since the announcement of App Engine, Google added multiple cloud services to the platform.

---

## Creating Project

The first thing that you'd have to do is create a project. A project offers a namaespace so that you could isolate the infrastructure for one project from another. After clicking the new project button you should input a project name. There is one more section name is Location, It chooses your organization: organization typically depends on your Google account. You don't must give any location for your projects.

![newProject][2]

![newNotification][3]

## VM instances

Time to create a new intance on **GCP (Google Cloud Platform)** you should select Compute Engine from menu bar then you need to sleect **VM instances**. If you first time try to create an instance you need to enable **Compute Engine**.

![vmIntances][4]


**Here is exmaple of enabling for Compute Engine. Don't forget that every projects seperate from each other**.
![computeEngine][5]

## SSH Keys

The next thing before create an intance that I'm doing to do here is set up my  **SSH** keys. Under the **Metadata** item We can see SSH Keys section. Whenever I create a new instance, I want my authentication data to be added it automatically. We can create SSH for our project so We can use ssh keygen and take our public **RSA** key then We should add the key to our project.

![rsa][6]

**Adding RSA public key to Metadata.**

![publicKey][7]


## New VM Instance

An instance is nothing but a virtual machine created on Google Compute Engine as a platform. When you wnat to create a new instance You will see the options for instance like Region and Machine configuration. You can select what you need but It can effect your cost also you can see the price on in the left bar. I selected Ubuntu 18.04 Long Term Support for Boot Disc. If everythings are ready for you time to click create.

![createVM][8]


**Here is example for selecting boot image.**

![bootDisc][9]

If You done you can click create button and You will see processing for creating VM. Finally you can see your new instance.

![newInstanceReady][10]

## SSH to New Instance

You can reach you new instance with SSH from **GCP** there is a section for Secure Shell. Here is You can see.

![sshToVm][11]

Finally connecting from remote host to GCP inctance as You know we added RSA public key for SSH so We can connect from our local or any remote instances.

![sshFromRemote][12]



---

[1]: https://cloud.google.com/
[2]: ../assets/images/gcp/gcp2.PNG
[3]: ../assets/images/gcp/gcp3.PNG
[4]: ../assets/images/gcp/gcp4.PNG
[5]: ../assets/images/gcp/gcp5.PNG
[6]: ../assets/images/gcp/gcp6.PNG
[7]: ../assets/images/gcp/gcp7.PNG
[8]: ../assets/images/gcp/gcp8.PNG
[9]: ../assets/images/gcp/gcp9.PNG
[10]: ../assets/images/gcp/gcp10.PNG
[11]: ../assets/images/gcp/gcp11.PNG
[12]: ../assets/images/gcp/gcp12.PNG