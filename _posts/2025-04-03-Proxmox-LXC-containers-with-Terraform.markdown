---
title: "Creating Proxmox LXC Containers with Terraform: A Step-by-Step Guide"
layout: post
date: 2025-04-03 12:20
image: ../assets/images/prx-terra/main.jpg
headerImage: true
tag:
    - terraform
    - proxmox
category: blog
author: guneycansanli
description: Creating Proxmox LXC Containers with Terraform A Step-by-Step Guide
---

# Creating Proxmox LXC Containers with Terraform: A Step-by-Step Guide


## Introduction

This guide details how to use Terraform to automate the creation of Linux Containers (LXC) on a Proxmox VE (Virtual Environment) server. By defining our container specifications as code (Infrastructure as Code - IaC), we can achieve consistent, repeatable, and efficient deployments.

We will use the `telmate/proxmox` Terraform provider to interact with the Proxmox API and deploy a Debian 12 LXC container based on a pre-downloaded template.

## Prerequisites

Before you begin, ensure you have the following set up:

1.  **Proxmox VE Server**: A running Proxmox VE instance accessible over the network.
2.  **Terraform**: Terraform installed on the machine where you will run the commands. ([Download Terraform](https://www.terraform.io/downloads.html))
3.  **Proxmox API Token**: An API token generated within Proxmox with sufficient permissions (e.g., `PVEVMAdmin` role on `/vms`). Note the **Token ID** and **Secret**.
4.  **LXC Template**: The necessary LXC template file downloaded onto your Proxmox node's designated storage (e.g., `local` storage). This guide uses: `debian-12-standard_12.7-1_amd64.tar.zst`.
5.  **Git (Optional)**: Useful for version controlling your Terraform configuration.


You can find out terraform files (main.tf and variables.tf) [here](https://github.com/guneycansanli/my-blog/tree/main/assets/images/prx-terra)

## The Setup Process

### Step 1: Obtain the Terraform Files

First, you need the Terraform configuration files. If you have them in a Git repository, clone it. Otherwise, ensure you have the following files in a dedicated directory:

* `main.tf`: Defines the Proxmox provider and the LXC container resource.
* `variables.tf`: Declares the input variables needed for configuration.
* `terraform.tfvars.example`: A template file showing the required variables.
* `.gitignore`: Prevents sensitive files (like `terraform.tfvars`) and Terraform state from being committed to Git.


![prx][1]
Description: A screenshot of a file explorer or terminal showing the four essential files (main.tf, variables.tf, terraform.tfvars.example, .gitignore) in a project directory.



### Step 2: Configure Your Deployment Variables

Terraform uses variables to customize deployments without changing the core logic.

1.  **Create `terraform.tfvars`**: Make a copy of the example template:
    ```bash
    cp terraform.tfvars.example terraform.tfvars
    ```

2.  **Edit `terraform.tfvars`**: Open the new `terraform.tfvars` file with your text editor and fill in the values specific to your Proxmox environment. Pay close attention to:
    * `pm_api_url`: Your Proxmox server's API URL (e.g., `https://192.168.1.50:8006/api2/json`).
    * `pm_api_token_id`: The full ID of your Proxmox API token (e.g., `terraform-user@pve!mytoken`).
    * `pm_api_token_secret`: The secret value for your API token. **Treat this like a password!**
    * `node_name`: The name of your target Proxmox node (e.g., `pve`).
    * `lxc_template`: Ensure this matches the filename of the template on your Proxmox storage.
    * `lxc_password`: The desired root password for the new container. **Use a strong password!**
    * Adjust `lxc_count`, `lxc_cores`, `lxc_memory` as needed.

    ```bash
    # Example terraform.tfvars content
    pm_api_url          = "https://YOUR_PROXMOX_IP:8006/api2/json"
    pm_api_token_id     = "YOUR_API_TOKEN_ID"
    pm_api_token_secret = "YOUR_SECRET_VALUE" # Consider using env vars instead!
    node_name           = "your_proxmox_node_name"
    lxc_template        = "debian-12-standard_12.7-1_amd64.tar.zst"
    lxc_count           = 1
    lxc_cores           = 2
    lxc_memory          = 1024
    lxc_password        = "YourStrongPassword" # Consider using env vars instead!
    ```

    **Security Note:** For `pm_api_token_secret` and `lxc_password`, it's highly recommended to use environment variables (`TF_VAR_pm_api_token_secret`, `TF_VAR_lxc_password`) instead of writing them directly into the file, especially if using version control. The provided `.gitignore` prevents `terraform.tfvars` from being committed.

## The Deployment Process

Now that the configuration is ready, we can use Terraform commands to deploy the container. Open your terminal in the directory containing the `.tf` files.

### Step 1: Initialize Terraform

Initialize the Terraform working directory. This downloads the Proxmox provider plugin specified in `main.tf`.
```bash
terraform init
```

### Step 2:  Plan the Deployment

Generate and review an execution plan. This command shows what actions Terraform will perform (e.g., creating one LXC resource) without actually doing anything.

```bash
terraform plan
```

![prx][2]

Optionally, save the plan to a file:

```bash
terraform plan -out=tfplan
```

![prx][3]


### Step 3: Apply the Configuration
Execute the actions proposed in the plan to create the LXC container(s).
If you saved the plan:

```bash
terraform apply tfplan
```

If you did not save the plan:

```bash
terraform apply
```

Terraform will ask for confirmation before proceeding. Type yes and press Enter.

![prx][4]


## Verifying the Deployment

### Check Proxmox Web UI
Log in to your Proxmox web interface. Navigate to your target node. You should now see the newly created LXC container listed (e.g., lxc-debian-1 if lxc_count was 1) with the specified resources and status (likely running, if start = true).

### Access the Container (Optional)
You can access the container via the Proxmox console or SSH (if configured) using the root user and the password you set in terraform.tfvars.

![prx][5]


## Cleaning Up (Destroying Resources)
To remove the LXC container(s) created by this Terraform configuration, run:

```bash
terraform destroy
```

Terraform will show you the resources to be destroyed and ask for confirmation. Type yes and press Enter.


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

[1]: ../assets/images/prx-terra/prx-1.jpg
[2]: ../assets/images/prx-terra/prx-2.jpg
[3]: ../assets/images/prx-terra/prx-3.jpg
[4]: ../assets/images/prx-terra/prx-4.jpg
[5]: ../assets/images/prx-terra/prx-5.jpg






