---
title: "Integrating n8n AI Agent with Proxmox for Server Management"
layout: post
date: 2025-03-07 12:20
image: ../assets/images/n8n-prx/main.jpg
headerImage: true
tag:
    - n8n
    - proxmox
    - ai-agent
category: blog
author: guneycansanli
description: Integrating n8n AI Agent with Proxmox for Server Management
---

# Introduction

[n8n](https://n8n.io/) is a low code drag and drop automation tool, with n8n you can create many pipelines, workflows or AI agents. With the increasing demand in recent years, AI agents are becoming ubiquitous and can be really useful when used correctly. Today I will explain how I integrated n8n AI agent for managing my proxmox server. I will use pre-define workflow which is created by **Amjid Ali** [Proxmox AI agent with n8n](https://n8n.io/workflows/2749-proxmox-ai-agent-with-n8n-and-generative-ai-integration/)

---

## Prerequisites

Ensure you have the following before proceeding:

- A **running Proxmox server** (either public or self-hosted)
- A **running n8n instance** (self-hosted, cloud, or public)
- **Both Proxmox and n8n must be accessible via a public network**

### Creating Proxmox API Key

1. Open your web browser and navigate to your Proxmox web UI:
```bash
https://your-proxmox-ip:8006  
```
2. Log in with your root credentials.
3. In the left sidebar, click on Datacenter.
4. Go to the **Permissions** tab.
5. Click on **API Tokens**.
6. Click the **"Add"** button to create a new API Token.
7. In the User field, select **root@pam** (or any other user you want to generate a token for).
8. In the Token ID field, enter a descriptive name (e.g., n8n-integration).
9. (Optional) You can Check **"Privileged"** to give full access or manually assign specific permissions.
10. Click "Add" to generate the API token.
11. Save your API key in a safe place

![n8n-prx1][1]



### Import Template Workflow to n8n

1. You can download or copy the workflow to your PC and save it as **json** file. 
2. In **n8n** click **+** icon and create new workflow.
3. Click **...** next to **save** button and import your file.
4. You can also directly use templae URL to import any template workflow.
5. Once you importat the template You will be able see nodes. 

![n8n-prx2][2]

**Here is the how it should look like.**

![n8n-prx2][3]

### Setting up AI agent Proxmox Credentials

1. Every HTTP Request includes Proxmox API Credentials so We need to update all HTTP Request Credentials.

![n8n-prx2][4]

- Also We need to udpate AI Agent text and Train our AI agent how it should process and which urls it should use 
- Example:

```bash
#You are a Proxmox AI Agent expert designed to generate API commands based on user input. 
#This is Proxmox which will help you to get the details of existing Proxmox installations, ensure to append to existing url : https://192.168.1.11:8006/api2/ to #get response from existing proxmox 
#My prommox nodes are named as pve
#pve : https://192.168.1.11:8006/api2/

```

NOTE: Plase check **AI Agent** and edit for your requirements. AI agent will be main component to manage all workflow, You can use also diffrent AI Model since Gemini is free to ues I will use Gemini. 

- Edit your HTTP request and use your proxmox server credential.

![n8n-prx2][5]

![n8n-prx2][6]

- You may need to create new credential.

### Testing Workflow

1. Since template uses Chat Message trigger We can use n8n chat box, We can use Telegram , Whatsapp or Slack etc. 
2. Let's test with to have my proxmox server running VMs overviews. 
3. Save and Active your workflow before testing.

![n8n-prx2][7]

- Here is example I can chat with AI agent and It tells me all running VMs overview.

![n8n-prx2][8]

- List all Proxmox server users  

![n8n-prx2][9]

- Overview of Proxmox server.

![n8n-prx2][10]


# Conclusion

I had a great time talking with the AI agent while managing my Proxmox server. With AI, we can automate everything, making tasks easier and more efficient. AI agents will become incredibly popular in the future, helping manage various systems and tasks in everyday life.


---

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

[1]: ../assets/images/n8n-prx/n8n-prx1.jpg
[2]: ../assets/images/n8n-prx/n8n-prx2.jpg
[3]: ../assets/images/n8n-prx/n8n-prx3.jpg
[4]: ../assets/images/n8n-prx/n8n-prx4.jpg
[5]: ../assets/images/n8n-prx/n8n-prx5.jpg
[6]: ../assets/images/n8n-prx/n8n-prx6.jpg
[7]: ../assets/images/n8n-prx/n8n-prx7.jpg
[8]: ../assets/images/n8n-prx/n8n-prx8.jpg
[9]: ../assets/images/n8n-prx/n8n-prx9.jpg
[10]: ../assets/images/n8n-prx/n8n-prx10.jpg



