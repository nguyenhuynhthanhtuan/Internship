---
title: "Week 6"
date: 2026-05-21
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

**Timeline:**  21/5 → 27/5 (5 working days)

## Day 1  21/5 → 27/5 (5 working days)

**Work completed:** ** I studied AWS Lambda and learned how serverless computing allows applications to execute code without managing servers. During the lab, I explored Lambda functions, execution environments, triggers, IAM Roles, environment variables, and the function lifecycle. I also practiced creating simple Lambda functions and reviewed how Lambda processes requests automatically when triggered by AWS services.


**Knowledge learned:** I understood that AWS Lambda enables developers to focus on application logic while AWS automatically manages infrastructure, scaling, and server maintenance.


**Result achieved:** I successfully created and tested Lambda functions and understood how serverless functions are deployed and executed on AWS.


**Difficulty and lesson learned:** Lambda functions have execution time and resource limitations, so application logic should be designed efficiently to avoid unnecessary execution costs.

## Day 2- 22/5: Amazon API Gateway

**Work completed:** I studied Amazon API Gateway and learned how it provides RESTful APIs for serverless applications. During the exercises, I explored API resources, methods, stages, request and response processing, and Lambda integration. I also learned how API Gateway routes incoming requests to backend Lambda functions and supports secure API management.

**Knowledge learned:** I understood that API Gateway serves as the entry point for serverless applications by managing API requests, routing traffic, and integrating with AWS services.


**Result achieved:** I understood the workflow of building APIs with API Gateway and integrating them with AWS Lambda.

**Difficulty and lesson learned:** Any configuration changes made in API Gateway must be deployed to a stage before they become available to clients.

## Day 3 - 25/5: Amazon SNS and Amazon SQS


**Work completed:** I studied Amazon Simple Notification Service (SNS) and Amazon Simple Queue Service (SQS). During the learning session, I explored the publish-subscribe messaging model of SNS and the message queue mechanism provided by SQS. I also compared their purposes and reviewed common scenarios where notifications and asynchronous message processing are required.


**Knowledge learned:** I understood that SNS is designed for message broadcasting, while SQS enables reliable asynchronous communication between distributed application components.

**Result achieved:** I understood how SNS and SQS improve scalability, reliability, and decoupling within serverless architectures.

**Difficulty and lesson learned:**Selecting between SNS and SQS depends on communication requirements, as each service is designed for different messaging patterns.

## Day 4 - 26/5: Amazon EventBridge

**Work completed:** I studied Amazon EventBridge and learned how event-driven architectures work on AWS. During the exercises, I explored event buses, rules, event patterns, and scheduled events. I also learned how EventBridge integrates with AWS Lambda and other AWS services to automate workflows based on system events.

**Knowledge learned:** I understood that EventBridge enables applications to respond automatically to events, reducing manual intervention and improving system automation.


**Result achieved:** I understood the basic process of creating event rules and connecting AWS services through event-driven workflows.

**Difficulty and lesson learned:**  Event rules must be configured carefully to ensure that only the intended events trigger application workflows.

## Day 5- 27/5: Serverless architecture review

**Work completed:** I reviewed the AWS services studied during the week, including AWS Lambda, Amazon API Gateway, Amazon SNS, Amazon SQS, and Amazon EventBridge. I summarized how these services interact to build serverless applications capable of processing requests, exchanging messages, and responding automatically to events. I also documented common implementation patterns and best practices for designing scalable serverless architectures.

**Knowledge learned:**  I recognized that serverless applications are built by integrating multiple AWS managed services, allowing developers to build scalable and event-driven systems without managing server infrastructure.

**Result achieved:** I consolidated my understanding of AWS serverless services and prepared detailed notes to support future laboratory exercises and project development.

**Difficulty and lesson learned:** Building serverless architectures requires understanding how individual AWS services communicate with one another. Careful service integration is essential for achieving reliability, scalability, and efficient event processing.
