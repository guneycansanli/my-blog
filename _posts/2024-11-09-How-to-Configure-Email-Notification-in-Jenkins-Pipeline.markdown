---
title: "How to Configure Email Notification in Jenkins Pipeline"
layout: post
date: 2024-11-09 14:20
image: ../assets/images/jenkins-email-noti/main.jpg
headerImage: true
tag:
    - jenkins
    - ubuntu
    - pipeline
    - python
category: blog
author: guneycansanli
description: How to Configure Email Notification in Jenkins Pipeline
---

# Jenkins Docker Agent with Python and Pipeline Configuration

### Introduction

This guide will walk you through setting up email notifications within a Jenkins Pipeline. Email notifications help keep your team informed about the status of builds, including successes, failures, and other alerts, directly from Jenkins. There are different type of notifications that We can use etc. **Slack**

### Benefits of Email Notifications in Jenkins
1. Immediate Feedback: Notifies the team of build status changes in real time.
2. Reduced Downtime: Allows for quicker response to issues by alerting developers instantly.
3. Improved Collaboration: Keeps all stakeholders in the loop regarding build health and deployment status.
4. Increased Reliability: Ensures issues are not overlooked, improving the CI/CD pipeline’s robustness.

* * *

# Prerequisites

1.  **Jenkins Server**: A running Jenkins server.
2.  **SMTP Configuration**: Email server details for sending notifications. (I will use gmail SMTP since I have not personal SMTP server)
3. **Valid Email Addresses**: Email addresses of the team members to notify.

* * *

# Step 1: Configure Gmail Account for SMTP

**Gmail** provides SMTP connection for any of gmail account. For Email configuration SMTP to work in Jenkins there are three things you need to do:

1. Enable 2-step verification on your Google account.
2. Create an App password.
3. Enable 2-step verification on your Google account:
    1. Open Goggle account Security Section.
    2. Enale 2-Factor.


![jen-mail][1]



### Step 2: Create an App password

We need to create app password which is basicly for smtp authentication.

1. Click on 2-step verification and scroll down to the app password section. click on the app password button.

![jen-mail][2]
    
2. It will generate a password, Save it for Jenkins E-mail configuration


* * *

# Step 3: Configure Jenkins Cloud for Docker Agent

We can configure E-mail Notification and Extended E-mail Notification with our SMTP credentials.

E-mail Notification:
1.  **Go to Jenkins Dashboard** > Manage Jenkins > Configure System.
2.  Scroll down to E-mail Notification.
3.  Enter your SMTP server details:
    *   SMTP Server: E.g., smtp.example.com (I use gmail)
    *   Default User E-mail Suffix: E.g., @example.com
    *   SMTP Port: 465
    *   Use SSL : Checked
4.  Test Email Notification and after We make sure It works We can configure **Extended E-mail Notification** with same configurations

![jen-mail][3]

![jen-mail][4]

Note: Jenkins has 2 different e-mail configurations.

1. E-mail Notification is from the plugin Mailer and allows to send a basic email.
2. Extended E-mail Notification is from the plugin Email-ext and allows more customization, e.g. from the plugin's page: "when an email is sent, who should receive it, and what the email says". This plugin also allows to set default global parameters (e.g. recipient list) so you don't have to set these parameters on each and every project.


Extended E-mail Notification: 

1. Find out **xtended E-mail Notification** in same system configuration page (Should be above of E-mail Notification)
2. Use same credentials.

![jen-mail][5]

3. Here is what extended verison has extra features. We can configure content type of e-mail, default reply, default decipients and etc.
4. We will use **Default Subject** and **Default Content** with pipeline spesific details.
    1. We will use some pipeline variables
        *   Default Subject: **$PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS!** 
        *   Default Content:
        ```
            Hi, Here is the report
            $PROJECT_NAME - Build # $BUILD_NUMBER - $BUILD_STATUS:

            Check console output at $BUILD_URL to view the results.


            Thanks,
            Guney
        ```

Here is my full configs:

![jen-mail][6]

* * *

# Step 4: Configure Email Notifications in Jenkins Pipeline

We are ready to use E-mail feature in our any of pipeline. I have already have a pipeline. I jusst need to edit my Jenkins file and add post step/job. 

1. My example post step will be:
        ```
         post {
            always {
                emailext (
                    subject: "Pipeline Status: ${BUILD_NUMBER}",
                    body: '''<html>
                                <body>
                                    <p>Build Status: ${BUILD_STATUS}</p>
                                    <p>Build Number: ${BUILD_NUMBER}</p>
                                    <p>Check the <a href="${BUILD_URL}">console output</a>.</p>
                                </body>
                            </html>''',
                    to: 'guneycansanli@gmail.com',
                    from: 'jenkins@example.com',
                    replyTo: 'jenkins@example.com',
                    mimeType: 'text/html'
                )
            }
        }
        ```

![jen-mail][7]

2. After adding new post job. You can trigger your pipeline.
3. We can also use some cool context in our mail body. I kept it basic for now :D

![jen-mail][8]

![jen-mail][9]

# Conclusion

By following these steps, you can configure email notifications in your Jenkins pipeline to keep your team informed about build statuses. This setup helps streamline communication, enabling faster responses to build issues and enhancing the CI/CD process.

* * *

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

[1]: ../assets/images/jenkins-email-noti/jen-mail-1.jpg
[2]: ../assets/images/jenkins-email-noti/jen-mail-2.jpg
[3]: ../assets/images/jenkins-email-noti/jen-mail-3.jpg
[4]: ../assets/images/jenkins-email-noti/jen-mail-4.jpg
[5]: ../assets/images/jenkins-email-noti/jen-mail-5.jpg
[6]: ../assets/images/jenkins-email-noti/jen-mail-6.jpg
[7]: ../assets/images/jenkins-email-noti/jen-mail-7.jpg
[8]: ../assets/images/jenkins-email-noti/jen-mail-8.jpg
[9]: ../assets/images/jenkins-email-noti/jen-mail-9.jpg

