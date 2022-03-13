---
title: "AWS : Tips and Tricks"
date: 2022-03-13T17:57:50.856Z
summary: ""
draft: false
featured: false
tags:
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
e.2. create a lambda function with the package-type of `Image` and code that is `ImageUri `of docker image. 

f. To respond to an CloudWatch event which is triggered whenever something changes in your AWS infrastructure, we should:\
f.1.