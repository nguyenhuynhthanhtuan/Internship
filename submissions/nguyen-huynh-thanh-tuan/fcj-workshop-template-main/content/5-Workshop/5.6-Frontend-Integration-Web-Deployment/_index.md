---
title: "Frontend Integration and Web Deployment"
date: 2026-07-17
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## 5.6. Frontend Integration and Web Deployment

#### Contents

- 5.6.12 Frontend & CloudFront

### 5.6.12 Frontend Integration & Web Deployment

#### 5.6.12.1 Integrating the Stitch AI Frontend

After the backend and API were completed, the team improved the user interface with Stitch AI. The generated UI was adapted into the React + Vite application while keeping the existing authentication and API integration logic.

The current UI includes:

- Dashboard
- Incidents
- Incident Detail
- Evidence
- Reports
- AI Assistant Panel

Inside the Incident Detail page, the AI Assistant Panel includes:

- AI Analysis
- Explain Severity
- Ask AI
- Suggested Questions
- Loading state while the AI request is running
- Fallback message when the provider is unavailable

The interface uses provider-neutral wording such as AI Assistant, AI Analysis, and Incident Intelligence. It does not expose provider branding or API keys to the user.

The integration process included:

1. Export UI screens from Stitch AI.
2. Convert the screens into React + Vite components.
3. Keep the existing Cognito authentication flow.
4. Connect screens to API Gateway endpoints.
5. Replace mock data with real data from DynamoDB through Lambda.
6. Test the AI Assistant Panel with `/ai/analyze`, `/ai/explain`, and `/ai/chat`.

#### 5.6.12.2 Building the React Application

After the frontend was completed, the project was built with Vite.

```bash
cd frontend
npm install
npm run build
```

After a successful build, Vite generates the `dist/` directory for deployment.

#### 5.6.12.3 Deploying Frontend to Amazon S3

The frontend build output was uploaded to the S3 frontend bucket.

```bash
aws s3 sync dist s3://irms-frontend-031577240048-ap-southeast-1   --delete   --profile irms-shared
```

The deployment uploads the generated HTML, CSS, JavaScript, and static assets to Amazon S3. No frontend API key exists.

#### 5.6.12.4 Deploying CloudFront using AWS SAM

To keep the architecture serverless and managed as Infrastructure as Code, CloudFront was added directly to `template.yaml`.

CloudFront was configured with:

- HTTPS
- HTTP to HTTPS redirect
- Compression
- `PriceClass_100`
- React SPA support

Custom error responses were configured so React Router can handle page refreshes correctly.

| HTTP Error | Response |
| --- | --- |
| 403 | `index.html` with status `200` |
| 404 | `index.html` with status `200` |

Build and deploy the backend and CloudFront stack:

```bash
sam build
sam deploy
```

After deployment, CloudFormation outputs the CloudFront distribution ID and domain name.

```text
CloudFrontDistributionId: E9646ICQMCQUR
CloudFrontDistributionDomainName: d1cdyvxfzvnsxg.cloudfront.net
Frontend URL: https://d1cdyvxfzvnsxg.cloudfront.net
```

#### 5.6.12.5 CloudFront Cache Invalidation

After each frontend update, create an invalidation so CloudFront serves the latest version.

```bash
aws cloudfront create-invalidation   --distribution-id E9646ICQMCQUR   --paths "/*"
```

CloudFront clears the cached objects and distributes the new frontend version.

#### 5.6.12.6 Final Deployment Architecture

```text
User
  |
  v
React Frontend (S3 + CloudFront)
  |
  v
API Gateway (REST)
  |
  v
Cognito JWT Authorizer
  |
  v
Lambda Functions
  |-- DynamoDB
  |-- S3 Evidence
  |-- EventBridge
  |-- SNS
  `-- Lambda AI Assistant
        |-- Groq (llama-3.1-8b-instant)
        `-- Rule-based fallback
```

Final AI flow:

```text
User -> React Frontend -> API Gateway -> Lambda AI Assistant -> Groq -> JSON Response -> Frontend
Fallback: Lambda AI Assistant -> Rule-based Engine -> Frontend
```
