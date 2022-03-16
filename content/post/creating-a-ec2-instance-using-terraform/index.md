---
title: Creating a EC2 instance using Terraform
date: 2021-10-26T04:02:04.182Z
draft: false
featured: false
tags:
  - Terraform
categories:
  - Terraform
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---


```
main.tf:
------------
provider "aws" {
  region = "us-west-2"
}
data "aws_ami" "ubuntu" {
  most_recent = true
  # select all AMIs with name matching regex expression
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
  # Ubuntu AWS account id
  owners = ["099720109477"]
}
resource "aws_vpc" "vpc" {
  cidr_block = "172.16.0.0/16"
  tags = {
    Name = "VPC"
  }
}
resource "aws_subnet" "subnet" {
  vpc_id            = aws_vpc.vpc.id
  cidr_block        = "172.16.10.0/24"
  availability_zone = "us-west-2a"
  tags = {
    Name = "Subnet"
  }
}
resource "aws_instance" "EC2" {
  ami           = data.aws_ami.ubuntu.id 
  instance_type = "t2.micro"
  subnet_id =aws_subnet.subnet.id
  tags = {
    Name = "EC2Instance"
  }
}
```