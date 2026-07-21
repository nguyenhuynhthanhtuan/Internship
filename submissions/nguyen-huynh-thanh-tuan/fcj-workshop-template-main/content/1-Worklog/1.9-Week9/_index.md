---
title: "Week 9"
date: 2026-06-11
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

**Timeline:** 11/6 → 17/6 (5 working days)

## Day 1 - 11/6: Project introduction and requirement analysis

**Work completed:** I started the group project named **Incident Response Management System (IRMS) on AWS Serverless**. During the first stage, I participated in discussions with team members to analyze the system requirements, identify the main objectives, and define the core features of the application. The team focused on understanding the incident management process, including incident creation, tracking, evidence management, status updates, and reporting.

**Knowledge learned:**  I understood that requirement analysis is an important step before implementation because it helps define system scope, necessary features, and technical solutions.

**Result achieved:** I had a list of core features to discuss with the team before starting the project.

**Difficulty and lesson learned:** If the design starts only from AWS services and ignores workflow, important functions may be missed.

## Day 2- 12/6: System architecture design

**Work completed:** I participated in designing the overall architecture of IRMS based on the AWS Serverless model. The team discussed how to combine AWS services such as Amazon Cognito, API Gateway, AWS Lambda, DynamoDB, Amazon S3, CloudWatch, EventBridge, and SNS to build a scalable and secure system. I also reviewed the role of each service in the architecture and how data flows between different components.

**Knowledge learned:** I understood how multiple AWS managed services can be integrated together to create a complete serverless application without managing traditional servers.

**Result achieved:** I had a draft to bring into team discussion about DynamoDB.

**Difficulty and lesson learned:** The model should not become too complex before the demo scope is confirmed.

## Day 3 - 15/6: Data flow design and system workflow

**Work completed:** I worked with the team to design the data flow and user request flow of IRMS. I analyzed how information moves from the frontend application to API Gateway, through Lambda functions, and then interacts with DynamoDB and S3. I also reviewed different workflows such as creating incidents, updating incident status, uploading evidence files, and retrieving reports.

**Knowledge learned:** I understood that data flow diagrams help visualize communication between system components and make the implementation process easier to manage.

**Result achieved:** I had a draft endpoint list for team discussion and adjustment.

**Difficulty and lesson learned:** Too many routes beyond demo scope would make testing and documentation heavier.

## Day 4 - 16/6: Task division and technology selection

**Work completed:** I participated in dividing tasks among team members based on project requirements and individual responsibilities. The team discussed backend development, frontend integration, database design, security configuration, and deployment processes. We also finalized the AWS services that would be used for each system component based on performance, security, and cost considerations.

**Knowledge learned:** Effective task division helps team members work independently while ensuring different system components can be integrated smoothly.

**Result achieved:** I had an early direction for report generation and alert automation.

**Difficulty and lesson learned:** The report must clearly separate implemented work from proposed extensions.

## Day 5 - 17/6: Project planning and implementation preparation

**Work completed:** I reviewed the project plan with the team and prepared the necessary information before starting implementation. I summarized the selected AWS services, architecture components, development workflow, and expected implementation steps. I also prepared technical notes related to authentication, API development, database operations, storage management, and monitoring.


**Knowledge learned:** Proper preparation before implementation helps reduce technical issues and makes the development process more organized.

**Result achieved:** I completed the preparation phase and had a clear roadmap for developing the IRMS system in the following weeks.

**Difficulty and lesson learned:** The worklog must state clearly that this week was research and preparation, not implementation.
