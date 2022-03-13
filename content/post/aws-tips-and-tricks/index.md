---
title: "AWS : Tips and Tricks"
date: 2022-03-13T17:57:50.856Z
summary: ""
draft: false
featured: false
tags:
  - AWS; AWS-Lambda; EventBridge
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
**AWS Lambda:**\
\
a. Invoke a Lambda function on a schedule eg: for checking a stock price, we should \
a.1. Create an event rule with a scheduled expression.\
a.2. Add permission to Lambda function by using the `aws lambda add-permission`command, so EventBridge(previously known as Amazon CloudWatch Events) can invoke it. \
a.3. Configure EventBridge to invoke the lambda function. \
\
b. Reduce AWS Lambda startup time to eliminate latency associated with cold starts, we should:\
configure the provisioned concurrency for the lambda function with provisioned concurrency.\
\
c.