---
title: "Terraform: Deploying a multi-tiered web application in AWS"
date: 2021-10-18T03:21:40.228Z
draft: false
featured: false
tags:
  - Terraform
  - AWS
categories:
  - Terraform
  - AWS
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
**modules\autoscaling:**

```
cloud_config.yaml
-------------------------------------------
#cloud-config
write_files:
  -   path: /etc/server.conf
      owner: root:root
      permissions: "0644"
      content: |
        {
          "user":  "${user}",
          "password": "${password}",
          "database": "${database}",
          "netloc": "${hostname}:${port}"
        }
runcmd:
  - curl -sL https://api.github.com/repos/scottwinkler/vanilla-webserver-src/releases/latest | jq -r ".assets[].browser_download_url" | wget -qi - 
  - unzip deployment.zip
  - ./deployment/server
packages:
  - jq
  - wget
  - unzip 

main.tf:
----------------------------------
module "iam_instance_profile" {
  source  = "terraform-in-action/iip/aws"
  actions = ["logs:*", "rds:*"] #A
}

data "cloudinit_config" "config" {
  gzip          = true
  base64_encode = true
  part {
    content_type = "text/cloud-config"
    content      = templatefile("${path.module}/cloud_config.yaml", var.db_config) #B
  }
}

data "aws_ami" "ubuntu" {
  most_recent = true
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-bionic-18.04-amd64-server-*"]
  }
  owners = ["099720109477"]
}

resource "aws_launch_template" "webserver" {
  name_prefix   = var.namespace
  image_id      = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  user_data     = data.cloudinit_config.config.rendered
  key_name      = var.ssh_keypair
  iam_instance_profile {
    name = module.iam_instance_profile.name
  }
  vpc_security_group_ids = [var.sg.websvr]
}

resource "aws_autoscaling_group" "webserver" {
  name                = "${var.namespace}-asg"
  min_size            = 1
  max_size            = 3
  vpc_zone_identifier = var.vpc.private_subnets
  target_group_arns   = module.alb.target_group_arns
  launch_template {
    id      = aws_launch_template.webserver.id
    version = aws_launch_template.webserver.latest_version
  }
}

module "alb" {
  source             = "terraform-aws-modules/alb/aws"
  version            = "~> 5.0"
  name               = var.namespace
  load_balancer_type = "application"
  vpc_id             = var.vpc.vpc_id
  subnets            = var.vpc.public_subnets
  security_groups    = [var.sg.lb]

  http_tcp_listeners = [
    {
      port               = 80, #C
      protocol           = "HTTP"
      target_group_index = 0
    }
  ]

  target_groups = [
    { name_prefix      = "websvr",
      backend_protocol = "HTTP",
      backend_port     = 8080
      target_type      = "instance"
    }
  ]
}
outputs.tf:
--------------------------------
output "lb_dns_name" {
  value = module.alb.this_lb_dns_name
}

variables.tf:
-----------------------------------
variable "namespace" {
  type = string
}

variable "ssh_keypair" {
  type = string
}

variable "vpc" {
  type = any
}

variable "sg" {
  type = any
}

variable "db_config" {
  type = object( #A
    { #A
      user     = string #A
      password = string #A
      database = string #A
      hostname = string #A
      port     = string #A
    } #A
  ) #A
}
```

**modules\database:**

```
main.tf:
--------------------------
# Uses the random provider to create a 16-character password
resource "random_password" "password" { #A
  length           = 16
  special          = true
  override_special = "/@"
}
resource "aws_db_instance" "database" {
  allocated_storage      = 10
  engine                 = "mysql"
  engine_version         = "8.0"
  instance_class         = "db.t2.micro"
  identifier             = "${var.namespace}-db-instance"
  name                   = "pets"
  username               = "admin"
  password               = random_password.password.result
  # The next 2 values, came from the networking module
  db_subnet_group_name   = var.vpc.database_subnet_group #B
  vpc_security_group_ids = [var.sg.db] #B
  skip_final_snapshot    = true
}

outputs.tf:
----------------------------------
output "db_config" {
  value = {
    user     = aws_db_instance.database.username #A
    password = aws_db_instance.database.password #A
    database = aws_db_instance.database.name #A
    hostname = aws_db_instance.database.address #A
    port     = aws_db_instance.database.port #A
  }
}

variables.tf:
-----------------------------------
variable "namespace" {
  type = string
}
variable "vpc" {
  # A type constraint of “any” means Terraform will skip type checking.
  type = any #A
}
variable "sg" {
  type = any #A
}
```

**modules\networking:**

```
main.tf:
------------
data "aws_availability_zones" "available" {}
# AWS VPC module published in the Terraform Registry
module "vpc" { #A
  source                           = "terraform-aws-modules/vpc/aws"
  version                          = "2.64.0"
  name                             = "${var.namespace}-vpc"
  cidr                             = "10.0.0.0/16"
  azs                              = data.aws_availability_zones.available.names
  private_subnets                  = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets                   = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]
  database_subnets                 = ["10.0.21.0/24", "10.0.22.0/24", "10.0.23.0/24"]
  create_database_subnet_group     = true
  enable_nat_gateway               = true
  single_nat_gateway               = true
}
module "lb_sg" {
  source = "terraform-in-action/sg/aws"
  vpc_id = module.vpc.vpc_id
  ingress_rules = [{
    port        = 80
    cidr_blocks = ["0.0.0.0/0"]
  }]
}
# Security group module published by author
module "websvr_sg" {
  source = "terraform-in-action/sg/aws"
  vpc_id = module.vpc.vpc_id
  ingress_rules = [
    {
      port            = 8080
      security_groups = [module.lb_sg.security_group.id]
    },
    {
      # next 2 lines, allows SSH for a potential bastion host
      port        = 22 #C
      cidr_blocks = ["10.0.0.0/16"] #C
    }
  ]
}
module "db_sg" {
  source = "terraform-in-action/sg/aws"
  vpc_id = module.vpc.vpc_id
  ingress_rules = [{
    port            = 3306
    security_groups = [module.websvr_sg.security_group.id]
  }]
}

outputs.tf:
-------------
output "vpc" {
  # Passes a reference to the entire vpc module as an output
  value = module.vpc #A
}

output "sg" {
  value = { #B
    # Constructs a new object containing the ID for each of the three security groups
    lb     = module.lb_sg.security_group.id #B
    db     = module.db_sg.security_group.id #B
    websvr = module.websvr_sg.security_group.id #B
  } #B
}

variables.tf:
--------------
variable "namespace" {
    type = string
}


```



```
main.tf:
--------------
module "autoscaling" {
  source      = "./modules/autoscaling" #A
  namespace   = var.namespace #B
  ssh_keypair = var.ssh_keypair #A
  vpc       = module.networking.vpc #A
  sg        = module.networking.sg #A
  db_config = module.database.db_config #A
}
module "database" {
  source    = "./modules/database" #A
  namespace = var.namespace #B
  # next 2 lines, data bubbles up from the networking module and trickles down into the database module.
  vpc = module.networking.vpc #A
  sg  = module.networking.sg #A
}
module "networking" {
  source    = "./modules/networking" #A
  namespace = var.namespace #B
}

outputs.tf:
------------------
output "db_password" {
  value = module.database.db_config.password
  # to ensure that the password is not output to console
  sensitive = true 
}
output "lb_dns_name" {
  value = module.autoscaling.lb_dns_name
}

providers.tf:
------------------
provider "aws" {
  region  = var.region
}

variables.tf:
---------------
variable "namespace" {
    type = string
}

terraform.tfvars:
---------------
namespace = "my-cool-project"
region    = "us-west-2"

variables.tf:
---------------
variable "namespace" {
  description = "The project namespace to use for unique resource naming"
  type        = string
}
variable "ssh_keypair" {
  description = "SSH keypair to use for EC2 instance"
  #set to null as it requires having a generated SSH keypair
  default     = null #A
  type        = string
}
variable "region" {
  description = "AWS region"
  default     = "us-west-2"
  type        = string
}

versions.tf:
------------
terraform {
  required_version = ">= 0.15"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.28"
    }
    random = {
      source  = "hashicorp/random"
      version = "~> 3.0"
    }
    cloudinit = {
      source  = "hashicorp/cloudinit"
      version = "~> 2.1"
    }
  }
}
```