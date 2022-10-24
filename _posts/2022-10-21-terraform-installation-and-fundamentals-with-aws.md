---
title: "Get started Terraform - Aws"
layout: post
date: 2022-10-21 20:20
image: ../assets/images/terraform/terraform.png
headerImage: true
tag:
- terraform
- devOps
- CI/CD
- Aws
- cloud
category: blog
author: guneycansanli
description: Get started Terraform - Aws
---

# What is Terraform?

[Terraform][1] is an tool for creating resources with a lot of different provider. It is an example for IAAS (infrastructure as code), It's official provider is HashiCorp but It is also open-source. Users define and provide data center infrastructure using a declarative configuration language known as **HashiCorp Configuration Language (HCL)**, or optionally **JSON**. Terraform will perform **CRUD** (Create Read Update Delete) actions on the user's behalf to accomplish the desired state. HashiCorp maintains a Terraform Module Registry, launched in 2017. In 2019, Terraform introduced the paid version called Terraform Enterprise for larger organization.

---

## Installing Terraform to Windows

You should follow that steps for Terraform installation for Windows 

* Navigate to the Terraform download page (https://www.terraform.io/downloads.html). It should list out Terraform downloads for various platforms. Navigate to the Windows section and download the respective version.

![Terraform2][2]



* After the donwload then you need to extract it the You will have an exe file which name is **Terraform.exe**.



* Update Path Environment Variable: For the last step you should define the environment variable for your user or for your system. It should point your Terrafor.exe file.  Next open the Start menu and search for Environment variables. Open the Environment variables settings page. On the Environment variables edit page, open the Path variable

![Terraform3][3]



### Test Terraform

Yes you donwload and configure Terraform. You can check and verify that Terraform is installed or not, just You can use your CLI with Terraform command. **terraform version** is the command for verify your terraform version.

![Terraform4][4]





### Creating AWS Resources with Terraform

Terrraform can create resources for a lot provider such as AWS, Azure, GCP ... But today I want to show creating AWS resources with Terraform fundamentals file types. Terraform has special file name calling **.tf**. You can use any text editor for using Terrafrom. I will prefer VS Code for it. Our resources model will be like below

![Terrafrom][5]

We will create NETWORKING, ROUTING, SECURITY GROUPS, INSTANCES with AWS provider inside the Terraform main folder. If you interest more you can inspect Terraform folder logics. There are more usefull method instead of colleting all resources under the main.tf file.

It is basicly has just 1 file (main.tf). 

![Terraform][6]


### PROVIDERS

{% highlight raw %}
provider "aws" {
  access_key = "ACCESS_KEY"
  secret_key = "SECRET_KEY"
  region     = "us-east-1"
}
{% endhighlight %}

Here you can see your provider cridentials. You should input your provider spesific information or you can use your system variables another option is that using with command.

### DATA

{% highlight raw %}
data "aws_ssm_parameter" "ami" {
  name = "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
}
{% endhighlight %}

This section is for your Image. AMI (Amazon Machine Image) is your EC2's image so you can define in that part. Terraform is user friendly. You can understand the resourse details.


### Networking 

As you see every resources starts with resources title in the Terraform file.

{% highlight raw %}
resource "aws_vpc" "vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true

}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.vpc.id

}

resource "aws_subnet" "subnet1" {
  cidr_block              = "10.0.0.0/24"
  vpc_id                  = aws_vpc.vpc.id
  map_public_ip_on_launch = true
}

{% endhighlight %}


### ROUTING 

{% highlight raw %}
resource "aws_route_table" "rtb" {
  vpc_id = aws_vpc.vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
}

resource "aws_route_table_association" "rta-subnet1" {
  subnet_id      = aws_subnet.subnet1.id
  route_table_id = aws_route_table.rtb.id
}
{% endhighlight %}


#### SECURITY GROUPS
# Nginx security group 
resource "aws_security_group" "nginx-sg" {
  name   = "nginx_sg"
  vpc_id = aws_vpc.vpc.id

  # HTTP access from anywhere
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # outbound internet access
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

### INSTANCES 

{% highlight raw %}
resource "aws_instance" "nginx1" {
  ami                    = nonsensitive(data.aws_ssm_parameter.ami.value)
  instance_type          = "t2.micro"
  subnet_id              = aws_subnet.subnet1.id
  vpc_security_group_ids = [aws_security_group.nginx-sg.id]

  user_data = <<EOF
#! /bin/bash
sudo amazon-linux-extras install -y nginx1
sudo service nginx start
sudo rm /usr/share/nginx/html/index.html
echo '<html><head><title>Taco Team Server</title></head><body style=\"background-color:#1F778D\"><p style=\"text-align: center;\"><span style=\"color:#FFFFFF;\"><span style=\"font-size:28px;\">You did it! Have a &#127790;</span></span></p></body></html>' | sudo tee /usr/share/nginx/html/index.html
EOF

}
{% endhighlight %}

The basic part of main.tf file should be like that:



{% highlight raw %}
##################################################################################
# PROVIDERS
##################################################################################

provider "aws" {
  access_key = "ACCESS_KEY"
  secret_key = "SECRET_KEY"
  region     = "us-east-1"
}

##################################################################################
# DATA
##################################################################################

data "aws_ssm_parameter" "ami" {
  name = "/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-gp2"
}

##################################################################################
# RESOURCES
##################################################################################

# NETWORKING #
resource "aws_vpc" "vpc" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true

}

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.vpc.id

}

resource "aws_subnet" "subnet1" {
  cidr_block              = "10.0.0.0/24"
  vpc_id                  = aws_vpc.vpc.id
  map_public_ip_on_launch = true
}

# ROUTING #
resource "aws_route_table" "rtb" {
  vpc_id = aws_vpc.vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }
}

resource "aws_route_table_association" "rta-subnet1" {
  subnet_id      = aws_subnet.subnet1.id
  route_table_id = aws_route_table.rtb.id
}

## SECURITY GROUPS
### Nginx security group 
resource "aws_security_group" "nginx-sg" {
  name   = "nginx_sg"
  vpc_id = aws_vpc.vpc.id

  # HTTP access from anywhere
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  # outbound internet access
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}

## INSTANCES
resource "aws_instance" "nginx1" {
  ami                    = nonsensitive(data.aws_ssm_parameter.ami.value)
  instance_type          = "t2.micro"
  subnet_id              = aws_subnet.subnet1.id
  vpc_security_group_ids = [aws_security_group.nginx-sg.id]

  user_data = <<EOF
#! /bin/bash
sudo amazon-linux-extras install -y nginx1
sudo service nginx start
sudo rm /usr/share/nginx/html/index.html
echo '<html><head><title>Taco Team Server</title></head><body style=\"background-color:#1F778D\"><p style=\"text-align: center;\"><span style=\"color:#FFFFFF;\"><span style=\"font-size:28px;\">You did it! Have a &#127790;</span></span></p></body></html>' | sudo tee /usr/share/nginx/html/index.html
EOF

}


{% endhighlight %}



## Terraform Commands 

 Open the main.tf file in your code editor and replace the values 
 for the AWS keys in the config file

 !! DO NOT COMMIT THESE TO SOURCE CONTROL !!

 Now we will follow the standard Terraform workflow
**terraform init**
**terraform plan -out m3.tfplan**
**terraform apply "m3.tfplan**

 Got to the Console and get the Public IP address for the EC2 instance
 and browse to port 80.

 If you are done, you can tear things down to save $$

**terraform destroy**

Thanks for reading :)



---

[1]: https://www.terraform.io/
[2]: ../assets/images/terraform/terraform2.PNG
[3]: ../assets/images/terraform/terraform3.PNG
[4]: ../assets/images/terraform/terraform4.PNG
[5]: ../assets/images/terraform/terraform5.PNG