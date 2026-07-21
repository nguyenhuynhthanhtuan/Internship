---
title: "Proposal"
date: 2026-07-03
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# IRMS - Incident Response Management System

## Project Proposal

### 1. Executive Summary

IRMS (Incident Response Management System) is a SaaS-style platform for managing cybersecurity incidents on AWS serverless services. The project was implemented by a five-member team during the FCAJ internship program. My main contribution focused on AWS infrastructure, API integration, deployment, testing, and documentation.

The system standardizes the incident response workflow: detection, classification, assignment, investigation, evidence storage, reporting, and AI-assisted guidance. IRMS replaces informal tracking through Email, Excel, Zalo, or chat messages with a centralized web application. The final implementation uses Groq with model `llama-3.1-8b-instant`, stores the provider API key in AWS Secrets Manager, and includes a rule-based fallback when the AI provider is unavailable. Amazon Bedrock is kept as an optional future provider through the same provider abstraction.

### 2. Problem Statement

In many small and medium-sized organizations, security incidents such as exposed S3 buckets, suspicious authentication attempts, or phishing reports are handled through informal channels. This makes it difficult to track ownership, preserve evidence, review status changes, and generate reports for later assessment.

IRMS addresses this problem by providing a structured incident management workflow on AWS. The project prioritizes serverless services to reduce operational overhead and keep the demo environment cost-aware.

### 3. Solution Architecture

The system follows a Serverless-first model and avoids EC2, RDS, and NAT Gateway in the MVP to reduce cost and operational complexity.

![IRMS Solution Architecture](/Internship/images/2-Proposal/IRMS_architecture_v10.drawio.png)

#### Deployed Core Architecture

- **Amazon CloudFront**: Distributes the React frontend and provides HTTPS access.
- **Amazon S3 (Frontend Hosting)**: Stores the React + Vite static build output.
- **Amazon API Gateway (REST)**: Receives backend API requests from the frontend.
- **Amazon Cognito**: Handles login, issues JWT tokens, and supports role-based access.
- **AWS Lambda**: Runs business logic for incidents, evidence, reports, alerts, and AI assistance.
- **Amazon DynamoDB**: Stores incidents, timeline records, users, and evidence metadata.
- **Amazon S3 (Evidence Store)**: Stores evidence files and generated report artifacts.
- **Amazon EventBridge**: Routes scheduled and simulated security events.
- **Amazon SNS**: Sends notification messages for alert workflows.
- **AWS Secrets Manager**: Stores the Groq API key securely.
- **Amazon CloudWatch**: Stores logs and supports troubleshooting.

#### AI Provider Layer

- **Current provider:** Groq.
- **Model:** `llama-3.1-8b-instant`.
- **Secret storage:** AWS Secrets Manager.
- **Fallback:** Rule-based analysis if the provider is unavailable or times out.
- **Future Enhancement:** Amazon Bedrock can be enabled later without changing the frontend API contract.

#### Future Enhancements

- **Amazon Route 53:** Custom domain and DNS management.
- **AWS WAF:** Additional web request filtering in front of CloudFront.
- **Amazon Bedrock:** Native AWS AI provider option when project requirements and account access allow it.

### 4. Technical Implementation

**Frontend:** React + Vite with Stitch AI-assisted UI integration. Main screens include Dashboard, Incidents, Incident Detail, Evidence, Reports, and the AI Assistant Panel. The application is built into static assets, uploaded to S3, and served through CloudFront.

**Backend:** Python Lambda functions behind API Gateway REST endpoints. Cognito JWT authentication protects the API. DynamoDB stores operational data, while S3 stores evidence and report artifacts.

**AI Assistant:** The AI Lambda exposes `POST /ai/analyze`, `POST /ai/explain`, and `POST /ai/chat`. These endpoints use a provider abstraction. In the final implementation, requests are sent to Groq, and fallback logic returns a structured response if the provider is unavailable.

**Infrastructure as Code:** AWS SAM defines serverless resources and deployment configuration. The backend is validated with `sam validate`, built with `sam build`, and deployed with `sam deploy`.

**Source Control:** GitHub is used for collaboration, with feature branches and review before merging team changes.

**Team assignment:**

- **Member 1:** Frontend UI and dashboard integration.
- **Member 2:** Authentication, Cognito, API Gateway, and RBAC.
- **Member 3:** Incident CRUD, DynamoDB schema, and report logic.
- **Member 4:** Evidence upload and storage integration.
- **Member 5:** Security automation, EventBridge/SNS, AI Assistant, deployment support, and documentation coordination.

### 5. Timeline & Milestones

- **Week 1:** Set up AWS accounts, IAM users, budget monitoring, SAM CLI, GitHub workflow, Cognito, API Gateway, and the first test Lambda.
- **Week 2:** Implement Incident CRUD, DynamoDB access patterns, frontend API integration, and login-to-incident end-to-end testing.
- **Week 3:** Implement evidence upload, report generation, EventBridge/SNS alert automation, and deployment validation.
- **Week 4:** Complete CloudFront/S3 hosting, integrate the AI Assistant with Groq, configure Secrets Manager, test Cognito-protected API routes, and run end-to-end validation.
- **Final period:** Refine the workshop, synchronize bilingual documentation, investigate Bedrock as a future provider, replace sensitive demo credentials with placeholders, and prepare the final report for submission.

### 6. Budget Estimation

The serverless architecture has no continuously running compute servers. Cost is mainly driven by actual requests, storage, logs, and secret storage.

| Service | Expected cost driver |
| --- | --- |
| AWS Lambda | Number of invocations and duration |
| API Gateway | Number of REST API requests |
| DynamoDB | Read/write capacity and storage |
| Amazon S3 | Frontend/evidence storage and requests |
| CloudFront | Data transfer and requests |
| Cognito | Monthly active users |
| EventBridge | Events and scheduler usage |
| SNS | Notification requests |
| Secrets Manager | Number of stored secrets |
| CloudWatch | Logs and metrics |
| Groq | Development/demo usage under the provider's free tier |
| Amazon Bedrock | Future inference cost only if enabled |

For the internship demo, the expected AWS usage is small. The team still monitors Billing and Cost Explorer regularly and removes unused resources after testing.

### 7. Risk Assessment

**Risk 1: AI provider availability or quota limitation**

Resolution: Keep AI access behind a provider abstraction. The current provider is Groq, the API key is stored in Secrets Manager, and the Lambda returns a rule-based fallback response if the provider is unavailable.

**Risk 2: AWS cost exceeds the expected demo budget**

Resolution: Use serverless services, avoid always-on resources such as EC2/RDS/NAT Gateway, monitor Billing and Cost Explorer, and clean up test resources after validation.

**Risk 3: Code conflicts when five members work together**

Resolution: Use feature branches, keep Lambda functions modular, agree on API contracts early, and review pull requests before merging.

**Risk 4: Team members are blocked by dependencies**

Resolution: Prioritize Cognito, API Gateway, and shared API contracts early. Frontend screens can use mock data while backend endpoints are being completed.

**Risk 5: Demo credentials or secrets are accidentally published**

Resolution: Use placeholders in documentation, store provider API keys in Secrets Manager, and avoid publishing passwords, access keys, tokens, or full presigned URLs.

### 8. Expected Outcomes

By the end of the project, the team will have a working IRMS system running on AWS with the following features:

- Login and role-based access through Cognito.
- Create, view, update, assign, and delete incidents.
- Record timeline activities for incident investigation.
- Upload and manage evidence files through S3 presigned URLs.
- Create alerts through EventBridge/SNS automation.
- Use AI-assisted incident analysis, severity explanation, and incident Q&A.
- Return a rule-based fallback response when the AI provider is unavailable.
- Export report data and document the cleanup process.
