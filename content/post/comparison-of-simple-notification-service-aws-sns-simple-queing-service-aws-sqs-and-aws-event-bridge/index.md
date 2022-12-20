---
title: Comparison of Simple Notification Service(AWS SNS), Simple Queing
  Service(AWS SQS) and AWS Event Bridge
date: 2022-12-20T23:43:51.961Z
draft: false
featured: false
tags:
  - AWS
image:
  filename: ""
  focal_point: Smart
  preview_only: false
---
Amazon Simple Notification Service (SNS) is a fully managed message-oriented middleware service that enables you to send and receive messages between distributed applications. It is designed to support messaging scenarios such as publish/subscribe, fan-out, and application-to-application communication.

Amazon Simple Queue Service (SQS) is a fully managed message queuing service that enables you to decouple and scale microservices, distributed systems, and serverless applications. It is designed to send, store, and receive messages between software systems in a loosely coupled way.

AWS EventBridge is a serverless event bus service that makes it easy to connect applications together using data from your own applications, integrated Software-as-a-Service (SaaS) applications, and AWS services. EventBridge enables you to send and receive data between applications in a flexible, reliable, and scalable way.

SNS, SQS, and EventBridge are all messaging services that can be used to exchange data between different applications, but they have some key differences:

* SNS is a publish/subscribe messaging service, while SQS is a message queue service. This means that SNS allows you to send a message to multiple subscribers (or "topics"), while SQS allows you to send a message to a single consumer.
* SNS and SQS are both designed for application-to-application communication, while EventBridge is designed for event-driven architectures and can be used to connect applications together using data from various sources, including your own applications, integrated SaaS applications, and AWS services.
* SNS and SQS are both managed services that are fully managed by AWS. EventBridge is also a managed service, but it provides more flexibility and customization options than SNS and SQS, such as the ability to filter and route events based on specific criteria.

Overall, the choice between SNS, SQS, and EventBridge depends on the specific requirements of your application and the type of messaging pattern you need. If you need a simple publish/subscribe messaging service, SNS may be the best choice. If you need a message queue service for decoupling and scaling microservices, SQS may be the best choice. If you need a flexible and customizable event-driven architecture, EventBridge may be the best choice.