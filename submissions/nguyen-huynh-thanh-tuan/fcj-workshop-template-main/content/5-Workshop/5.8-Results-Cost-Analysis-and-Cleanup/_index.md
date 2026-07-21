---
title: "Results, Cost Analysis, and Cleanup"
date: 2026-07-17
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## 5.8. Results, Cost Analysis, and Cleanup

#### Contents

- 5.8.13 Results, Cost Analysis, and Cleanup

### 5.8.13 Results, Cost Estimation and Resource Cleanup

#### 5.8.13.1 Project Results

After deployment and testing, the IRMS system completed the main functions planned for the workshop:

- User authentication with Amazon Cognito
- Incident management: create, update, delete, and view incidents
- Incident timeline tracking
- Evidence upload to Amazon S3
- AI-assisted incident analysis
- Report generation
- Dashboard monitoring
- HTTPS access through CloudFront
- Infrastructure as Code with AWS SAM

The whole system was deployed using a serverless architecture on AWS.

#### 5.8.13.2 AWS Services Used

| Service | Purpose |
| --- | --- |
| Amazon Cognito | Authentication |
| Amazon API Gateway | REST API |
| AWS Lambda | Business logic |
| Amazon DynamoDB | Incident database |
| Amazon S3 | Evidence storage and frontend hosting |
| Amazon CloudFront | CDN and HTTPS |
| Amazon EventBridge | Event trigger |
| Amazon SNS | Notification |
| AWS SAM | Infrastructure as Code |
| AWS IAM | Permission management |

#### 5.8.13.3 Cost Analysis

During IRMS development and deployment, I used AWS Billing & Cost Management to review cost by service. The billing report shows that the demo workload stayed almost entirely within the AWS Free Tier. Most services ended with **USD 0.00** charges.

The only item with a small gross charge was **AWS Secrets Manager**, which recorded **USD 0.17** for the secret used to store the AI provider key. This amount was offset by AWS Free Tier/Credit **(USD 0.17)**, so the final net AWS cost remained **USD 0.00**.

| AWS Service | Main usage recorded | Amount (USD) | Note |
| --- | --- | --- | --- |
| Amazon API Gateway | 956 requests | 0.00 | Within request free tier |
| AWS Lambda | 501 requests, 32.507 GB-seconds | 0.00 | Within Lambda free tier |
| Amazon DynamoDB | 677 read request units, 44 write request units | 0.00 | Very small demo-scale pay-per-request usage |
| Amazon Cognito | 4 monthly active users | 0.00 | Demo users for Admin, Manager, Analyst, and Auditor |
| Amazon S3 | 250 PUT/COPY/POST/LIST, 1,612 GET/other requests, 0.131 GB-Mo storage | 0.00 | Frontend hosting and evidence storage |
| Amazon CloudFront | 187 HTTPS requests, 8 invalidation URLs, about 0.003 GB data out | 0.00 | Within the global free tier |
| Amazon CloudWatch | 0.122 metrics, 6 GetMetricData metrics, log usage within free tier | 0.00 | Logs, metrics, and operational checks |
| Amazon EventBridge | 2 64K chunks | 0.00 | Event routing / scheduler |
| Amazon SNS | 79 requests | 0.00 | Notification testing |
| AWS CloudFormation | 279 resource handler operations | 0.00 | Stack deployment using IaC |
| AWS KMS | 29 total requests across regions | 0.00 | KMS request free tier |
| AWS X-Ray | 699 traces stored | 0.00 | Tracing for testing |
| AWS Secrets Manager | 0.426 secret-month, 72 API requests | 0.17 | Offset by AWS Free Tier/Credit |

Some billing lines such as AWS Glue requests appeared across multiple regions with **USD 0.00** amount and were not primary IRMS cost drivers. For that reason, the summary focuses on the services actually used by the IRMS architecture.

#### 5.8.13.4 AI Service Cost

The final implementation uses Groq for development and demo AI workloads. The API key is stored in AWS Secrets Manager and is read only by Lambda when calling the AI provider. No API key exists in the frontend.

| Item | Value |
| --- | --- |
| Current provider | Groq |
| Model | `llama-3.1-8b-instant` |
| Cost model used | Groq Free Tier for development/demo |
| Related AWS service | AWS Secrets Manager |
| Secrets Manager gross cost | USD 0.17 |
| Free Tier/Credit offset | (USD 0.17) |
| Net Secrets Manager cost | USD 0.00 |
| Future optional provider | Amazon Bedrock |

In this project:

- Only a small number of incident analysis, explain, and chat requests were sent.
- No model training was performed.
- No large-volume data processing was performed.
- If Groq is unavailable, the Lambda returns a rule-based fallback response.

The current AI cost does not come from Groq because the project used its free tier for development/demo. On AWS, the AI-related cost driver is mainly Secrets Manager because it stores the provider key safely. Amazon Bedrock was not enabled in the final version, so no Bedrock inference charge was generated.

#### 5.8.13.5 Total Project Cost

| Category | Gross Cost | Credit / Free Tier | Net Cost |
| --- | --- | --- | --- |
| AWS services within Free Tier | USD 0.00 | USD 0.00 | USD 0.00 |
| AWS Secrets Manager | USD 0.17 | (USD 0.17) | USD 0.00 |
| Groq API | USD 0.00 | Free Tier | USD 0.00 |
| Amazon Bedrock | USD 0.00 | Not enabled | USD 0.00 |
| **Actual total** | **USD 0.17** | **(USD 0.17)** | **USD 0.00** |

```text
Primary AWS usage       : Lambda, API Gateway, CloudFront, S3, DynamoDB, Cognito, CloudWatch
Small gross cost driver : Secrets Manager (USD 0.17 before credit)
Current AI provider     : Groq for development/demo usage
Future AI provider      : Amazon Bedrock only if enabled
Final net AWS cost      : USD 0.00 after Free Tier/Credit
```

The demo was kept cost-aware by using serverless services, checking billing after deployment, reviewing active resources, and cleaning up resources that were no longer needed.

#### 5.8.13.6 Resource Cleanup

After completing the workshop and evaluation, clean up resources to avoid unexpected cost.

Recommended cleanup order:

1. Disable and delete the old CloudFront distribution if it is no longer needed.
2. Delete the CloudFormation stack.

```bash
aws cloudformation delete-stack \
  --stack-name irms-serverless \
  --region ap-southeast-1 \
  --profile irms-shared
```

3. Check and delete any remaining resources:

- Amazon S3 frontend bucket, after backing up required files
- Amazon S3 evidence bucket, after backing up required data
- Lambda functions, if they were not removed with the stack
- API Gateway, if it was not removed with the stack
- DynamoDB tables, if they were not removed with the stack
- SNS topic
- EventBridge rules
- IAM roles created specifically for the project
- Secrets Manager secret for the Groq API key

Keep the Amazon Cognito User Pool only if there is a plan to continue development or reuse it for a later version.
