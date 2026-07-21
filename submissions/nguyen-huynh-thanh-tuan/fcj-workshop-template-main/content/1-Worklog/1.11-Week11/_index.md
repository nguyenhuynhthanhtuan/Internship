---
title: "Week 11"
date: 2026-06-25
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

**Timeline:** 25/6 → 1/7 (5 working days)

> IRMS was completed by a five-member team. The notes below focus on my individual contribution while collaborating with the team.

## Day 1 - 25/6: Functional testing of IRMS features
**Work completed:** I participated in functional testing of the main IRMS features after the initial development phase. I tested important workflows including user authentication, incident creation, incident updates, timeline tracking, evidence upload, and report generation. I verified whether frontend requests were correctly processed through API Gateway and Lambda before storing or retrieving data from DynamoDB and S3.

I reviewed the main requirements: incident management, timeline tracking, evidence upload, report generation, and alert handling. I then listed the AWS services needed for the project, including Cognito, API Gateway, Lambda, DynamoDB, S3 Evidence Store, EventBridge, SNS, Secrets Manager, CloudWatch, and CloudFront. I also separated the areas I would handle directly from the areas that required coordination with frontend and backend teammates.

**Knowledge learned:** I understood that functional testing is necessary to ensure each system feature works according to the original requirements and that different AWS services communicate correctly.

**Result achieved:** I identified several workflow issues and recorded test results to support the debugging process.

**Difficulty and lesson learned:** There were many possible extensions at first, so we had to prioritize the core features to fit the internship schedule.

## Day 2 - 26/6: Authorization and security testing

**Work completed:** I focused on testing authentication and authorization mechanisms in the IRMS system. I verified Cognito user authentication, JWT token validation, API Gateway authorization, and IAM permissions assigned to AWS resources. I tested different user scenarios, including valid users, expired tokens, and unauthorized requests, to ensure protected APIs could not be accessed without proper permissions.

I helped describe the data flow for Incident CRUD, Timeline, Evidence Upload, and Report Generation. For each flow, I wrote down the input, output, related AWS services, and testing points. I also discussed with the frontend teammate to align the endpoints, response format, and how the frontend should send the JWT in the Authorization header.

**Knowledge learned:** Authentication verifies user identity, while authorization controls access to specific resources and actions. Both mechanisms are important for protecting serverless applications.

**Result achieved:** The work items became clearer, which helped me prepare the SAM template and workshop documentation closer to the actual implementation.

**Difficulty and lesson learned:** Some requirements were still broad, so I needed to turn them into concrete checklists instead of relying on assumptions.

## Day 3 - 29/6: Performance testing and optimization

**Work completed:** I reviewed the system performance by monitoring Lambda execution logs, API response time, DynamoDB operations, and CloudWatch metrics. I analyzed possible performance bottlenecks and reviewed optimization methods such as improving Lambda logic, reducing unnecessary database operations, and using appropriate DynamoDB access patterns.

I checked the connection points between GuardDuty, EventBridge, Lambda Alert Handler, and SNS so the alerting section would be documented correctly. For AI Assistant, I clearly noted that Lambda reads the secret from Secrets Manager and calls the Groq API, avoiding API keys in the frontend or public documentation. I also updated notes to separate implemented architecture from future enhancements.

**Knowledge learned:** Serverless performance depends on how services are designed and integrated rather than only on individual service configurations.

**Result achieved:** I gained a better understanding of monitoring application performance and identifying areas that could be optimized.
**Difficulty and lesson learned:** If service names and arrows are not aligned early, later documentation pages can easily contradict each other.

## Day 4- 30/6: Bug fixing and interface improvement

**Work completed:** I collaborated with team members to fix errors discovered during testing. The main tasks included correcting API response formats, handling frontend error states, improving validation messages, and adjusting the user interface for better usability. I also reviewed the connection between frontend components and backend APIs to ensure consistent data exchange.

I validated the template with `sam validate`, reviewed Lambda IAM roles, and checked environment variables such as table names, bucket names, secret name, and AI provider. I also recorded the steps that needed to appear in the workshop, including AWS CLI/SAM CLI installation, profile configuration, build, validation, and deployment.

```bash
sam validate
sam build
sam deploy --guided
```

**Knowledge learned:** A stable application requires not only correct backend logic but also clear user interaction and proper error handling.

**Result achieved:** The backend infrastructure baseline was ready for the team to continue Lambda implementation and API testing.

**Difficulty and lesson learned:** Small template or IAM mistakes can block deployment, so validating early is better than waiting until the end.

## Day 5 - 1/7: Technical documentation completion and system evaluation

**Work completed:** I updated the technical documentation for the IRMS project, including architecture descriptions, AWS service configurations, testing scenarios, and deployment notes. I reviewed the final system workflow and evaluated the application's ability to operate through different test cases such as authentication, incident management, evidence storage, notifications, and monitoring.

I also recorded items that needed further validation in the following week, such as Cognito JWT, API Gateway CORS, Lambda logs, DynamoDB key design, S3 presigned URLs, and CloudFront deployment. Items that were not fully tested were marked for confirmation instead of being written as completed.

**Knowledge learned:** Complete documentation is an important part of software development because it helps explain system design, operation procedures, and future maintenance.

**Result achieved:** Worklog, Proposal, and Workshop started following the same project timeline from the week that includes July 1.

**Difficulty and lesson learned:** The worklog must describe my personal contribution in a five-member team, not present the whole system as work I completed alone.
