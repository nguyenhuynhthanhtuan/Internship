---
title: "Week 10"
date: 2026-06-18
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

**Timeline:** 18/6 → 24/6 (5 working days)

## Day 1 - 18/6: Developing AWS Lambda functions

**Work completed:** I started implementing the backend functions of the Incident Response Management System (IRMS) using AWS Lambda. I created and tested Lambda functions responsible for processing system requests and handling application logic. I also reviewed Lambda execution flow, function configuration, IAM Role permissions, environment variables, and integration with other AWS services.

**Knowledge learned:** I understood how AWS Lambda can execute backend logic in a serverless environment without requiring traditional server management. I also learned the importance of configuring execution roles and permissions correctly to allow Lambda functions to access required resources.


**Result achieved:** I had clearer criteria to discuss project selection with the team.

**Difficulty and lesson learned:** The scope needed to be realistic for the remaining internship time.

## Day 2 - 19/6: Building APIs with Amazon API Gateway


**Work completed:** I developed API endpoints for the IRMS system using Amazon API Gateway. I configured API routes, HTTP methods, request handling, and integration between API Gateway and Lambda functions. The main focus was creating APIs that support incident management operations such as creating incidents, retrieving incident information, and updating incident status.

**Knowledge learned:** I understood that API Gateway acts as the communication layer between frontend applications and backend serverless functions, responsible for receiving requests and forwarding them to appropriate Lambda functions.

**Result achieved:** I had an initial service map based on my AWS learning.

**Difficulty and lesson learned:** Draft diagrams must be updated after the team confirms real requirements.

## Day 3 - 22/6: DynamoDB integration for incident management

**Work completed:** I integrated Amazon DynamoDB into the IRMS backend to store and manage incident-related data. I designed database operations for creating, retrieving, and updating incident records. I also reviewed partition keys, item structures, and access patterns to ensure that data retrieval could be performed efficiently.


**Knowledge learned:** Knowledge learned:** I understood how DynamoDB supports serverless applications by providing a scalable NoSQL database solution with low latency and flexible data structures.

**Result achieved:** I had a proposal structure for the team to review and improve.

**Difficulty and lesson learned:** The proposal should not promise more features than the team can implement.

## Day 4 - 23/6: IAM security configuration and CloudWatch monitoring


**Work completed:** I configured IAM permissions for Lambda functions and AWS services used in the IRMS system. I applied the principle of least privilege by assigning only necessary permissions for each component. I also integrated Amazon CloudWatch to monitor Lambda execution logs, track errors, and support debugging during development.

**Knowledge learned:** I understood that IAM and CloudWatch play important roles in maintaining system security, monitoring application behavior, and troubleshooting issues in serverless environments.

**Result achieved:** I had an initial structure for the Workshop section of the report.

**Difficulty and lesson learned:** If numbering is inconsistent early, sidebar and internal links become time-consuming to fix later.

## Day 5 SNS notification integration and system review

** I integrated Amazon SNS into the IRMS architecture to support notification processing when important events occur. I reviewed how Lambda can publish messages to SNS topics and how notifications can be delivered to subscribed users or system administrators. I also reviewed the interaction between Lambda, API Gateway, DynamoDB, SNS, IAM, and CloudWatch to ensure the overall serverless workflow worked correctly.


**I understood how event-driven services such as SNS help separate application processing logic from notification delivery, improving system scalability and flexibility.

**Result achieved:** I completed the integration of the main AWS services used in the initial IRMS backend and prepared the foundation for further development.

**Difficulty and lesson learned:** The worklog should stay honest: earlier weeks were learning and preparation, while IRMS implementation starts in the week including July 1.
