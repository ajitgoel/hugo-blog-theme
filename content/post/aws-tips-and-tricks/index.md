---
title: "AWS : Notes, Tips and Tricks"
date: 2022-03-13T17:57:50.856Z
summary: ""
draft: false
featured: false
tags:
  - AWS
  - AWS-Lambda
  - EventBridge
  - ALB
categories:
  - AWS
  - AWS-Lambda
  - EventBridge
  - ALB
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
**AWS Lambda:**\
\
a. To invoke a Lambda function on a schedule eg: for checking a stock price, we should:\
a.1. Create an event rule with a scheduled expression.\
a.2. Add permission to Lambda function by using the `aws lambda add-permission`command, so EventBridge(previously known as Amazon CloudWatch Events) can invoke it. \
a.3. Configure EventBridge to invoke the lambda function. \
\
b. To reduce AWS Lambda startup time to eliminate latency associated with cold starts, we should:\
b.1. configure the provisioned concurrency for the lambda function with provisioned concurrency.\
\
c. To minimize AWS Lambda cold start time, we should: \
c.1. keep the size of your deployment package as small as possible\
c.2. provision additional memory\
c.3. use a runtime environment like Python, Node.js, or Go instead of C# or Java.  \
\
d.  To expose a lambda function using an Application load balancers(ALBs), we should:\
d.1. add permission to Lambda function by using the `aws lambda add-permission`command, so ELB can invoke it. \
d.2. create an ALB target group with the target type set to lambda.\
d.3. use the `register-targets`command to register the Lambda function as a target.\
d.4. modify the listener for your ALB on port 80; then create a rule that forwards traffic destined for the /function path to your target group.\
\
Application load balancers(ALBs) can have multiple paths and targets configured for a single load balancer, sending portions of traffic to specific targets (Lambda functions, containers, EC2 instances, etc.) ALBs also support routing to Lambda functions using header values.

e. To package Lambda code in a container image, we should:\
e.1. create a Docker image and push it to an Amazon Elastic Container Registry(ECR) repository. \
e.2. create a lambda function with the package-type of `Image` and code that is `ImageUri`of docker image. 

f. To respond to an CloudWatch event which is triggered whenever something changes in your AWS infrastructure, we should:\
f.1. Set up the lambda function and subscribe to the cloudwatch events of interest, using CloudFormation template. Do not return a value in the lambda function, as the Lambda function is invoked asynchronously by a CloudWatch event.\
f.2. authorize the Lambda function to use other AWS services with an IAM role. \
We do this by creating a IAM policy attached to the IAM role(which defines the services and action that the Lambda function is allowed to access). The Lambda function then assumes the IAM role to be able to send authenticated and authorized requests to other AWS services. Temporary credentials are generated based on the IAM role and injected into each invocation

g. To access endpoints within a private network(VPC), we must:\
g.1. define the VPC, the subnets, as well as security groups for the Lambda function\
g.2. add network interfaces to the lambda function so it can access resources with the VPC\
**Cons:** Placing a Lambda function in a VPC increases complexity, especially when scaling to a large number of concurrent executions. For example, the number of available private IP addresses in a VPC is limited, but a Lambda function will need multiple private IP addresses to be able to scale the number of concurrent invocations.

**AWS ECS versus AWS Lambda:**\
\
**AWS ECS** is used for container orchestration. It should be used when \
a. you are running docker containers as ECS has better support for docker containers compared to AWS lambda\
b. You want flexibility to run in a managed EC2 environment or in a serverless environment(AWS Fargate). 
c. You have tasks or batch jobs running longer than 15 minutes. 
d. You need to schedule jobs(ECS provides a service scheduler), along with the ability to run tasks manually. 

**AWS Lambda** is used for serverless function deployment. It should be used when \
a. You have a application that runs in 15 minutes or less. 
b. You don’t care or need advanced EC2 instance configuration. Lambda manages, provisions, and secures EC2 instances for you, along with providing target groups, load balancing, and auto-scaling. 
c. You want to pay only for  the milliseconds the code has run and the number of times your code is triggered.  |