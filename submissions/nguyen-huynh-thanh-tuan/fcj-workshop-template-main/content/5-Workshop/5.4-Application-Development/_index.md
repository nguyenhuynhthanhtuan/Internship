---
title: "Application Development"
date: 2026-07-17
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## 5.4. Application Development

#### Contents

- 5.4.7 Lambda Functions

### 5.4.7 Lambda Functions

#### 5.4.7.1 Compute Layer Overview

AWS Lambda is the business-processing layer of IRMS. Instead of keeping servers running continuously like EC2, each Lambda function starts only when an API request or event arrives, processes the task, and then stops. This model is suitable for an internship/demo system because cost depends on actual executions.

**Request flow through the compute layer:**

```text
Browser
  |
  v
API Gateway (Production)
  |
  +-- Lambda Incident CRUD
  |     +-- DynamoDB: irms-incidents, irms-timeline
  |
  +-- Lambda Upload Evidence
  |     +-- S3 Evidence Store: presigned URL
  |     +-- DynamoDB: irms-evidence-metadata
  |
  +-- Lambda Generate Report
  |     +-- DynamoDB: aggregate read
  |     +-- S3 Evidence Store: CSV output
  |     +-- Trigger: EventBridge Scheduler
  |
  +-- Lambda Alert Handler
  |     +-- DynamoDB: create incident from alert
  |     +-- SNS: send notification
  |     +-- Trigger: EventBridge
  |
  +-- Lambda AI Assistant
        +-- Secrets Manager: read Groq API key
        +-- Groq API: AI analysis
        +-- Rule-based fallback when provider is unavailable
```

**Role of each Lambda function:**

| Lambda | Main responsibility | Related services |
| --- | --- | --- |
| `incident-crud` | Create, read, update, and delete incidents and timeline entries | API Gateway, DynamoDB |
| `upload-evidence` | Generate presigned URLs and store evidence metadata | API Gateway, S3, DynamoDB |
| `generate-report` | Aggregate data and export CSV reports | EventBridge, DynamoDB, S3 |
| `alert-handler` | Receive alert/security events and create incidents automatically | EventBridge, DynamoDB, SNS |
| `ai-assistant` | Analyze incidents, explain severity, and answer incident questions | Secrets Manager, Groq API, rule-based fallback |

**Why Lambda instead of EC2:**

| Criteria | Lambda | EC2 |
| --- | --- | --- |
| Cost | Pay per invocation | Pay per running hour even when idle |
| Scaling | Scales automatically by request/event | Requires Auto Scaling configuration |
| Operations | No OS, patching, or runtime management | Requires server and runtime management |
| Best fit | Irregular traffic, internship demo, event-driven workload | Always-on servers |

#### 5.4.7.2 Backend Folder Structure

Open the repository and check the `backend/` folder structure:

```text
backend/
|-- incident-crud/
|   |-- handler.py
|   `-- requirements.txt
|
|-- upload-evidence/
|   |-- handler.py
|   `-- requirements.txt
|
|-- generate-report/
|   |-- handler.py
|   `-- requirements.txt
|
|-- alert-handler/
|   |-- handler.py
|   `-- requirements.txt
|
`-- ai-assistant/
    |-- handler.py
    `-- requirements.txt
```

**Responsibilities by Lambda function:**

| Lambda | Responsibility | Trigger |
| --- | --- | --- |
| `incident-crud` | CRUD incidents and timeline records | API Gateway |
| `upload-evidence` | Create presigned URLs and write metadata | API Gateway |
| `generate-report` | Aggregate data and export CSV to S3 | EventBridge Scheduler |
| `alert-handler` | Create incidents automatically and send notifications | EventBridge |
| `ai-assistant` | Analyze incidents and suggest severity | API Gateway |

#### 5.4.7.3 Incident CRUD Lambda

Responsibility: process the full lifecycle of an incident, from creating a new incident to reading the list, updating status, and deleting records.

**Operation flow:**

```text
GET /incidents
  Request with JWT token
  Lambda reads query string: ?status=Open&assignedTo=analyst@...
  Query DynamoDB table irms-incidents
    - if a status filter exists, use status-index GSI
    - if an assigned user filter exists, use assignedTo-index GSI
    - if no filter exists, scan the table
  Return incident list as JSON

POST /incidents
  Request body: {title, severity, description}
  Lambda validates input
  Generate incidentId: "INC-" + timestamp
  PutItem into irms-incidents
  PutItem into irms-timeline with action "Incident Created"
  Return the created incidentId

PUT /incidents/{id}
  Request body: {status, assignedTo, ...}
  Lambda verifies that the incidentId exists
  UpdateItem irms-incidents with only the fields sent in the request
  PutItem into irms-timeline with action "Status Changed" or "Assigned"
  Return the updated record

DELETE /incidents/{id}
  Request params: incidentId
  DeleteItem from irms-incidents
  Return 204 No Content
```

#### 5.4.7.4 Upload Evidence Lambda

Responsibility: create a presigned URL so the browser can upload evidence files directly to S3, then write evidence metadata into DynamoDB. The file does not pass through Lambda.

How the presigned URL is created: Lambda uses AWS SDK for Python (`boto3`) together with its IAM role to sign the presigned URL. The user does not need direct S3 permissions; the browser only uses the signed URL within the allowed time window.

```text
Step 1 - Request a presigned URL
  POST /evidence
  Body: {incidentId, fileName, fileType}
  Lambda generates evidenceId (UUID)
  Lambda signs a presigned URL:
    - Bucket: irms-evidence-031577240048-ap-southeast-1
    - Key: {incidentId}/{evidenceId}/{fileName}
    - Expiry: 300 seconds
    - Method: PUT
  Return {evidenceId, presignedUrl}

Step 2 - Browser uploads directly to S3
  Browser receives presignedUrl
  Browser sends PUT request directly to S3
    - URL: presignedUrl
    - Body: file binary
    - Header: Content-Type, for example image/png
  S3 stores the file and returns 200 OK

Step 3 - Confirm metadata
  POST /evidence/{id}
  Body: {evidenceId, incidentId}
  Lambda writes metadata into irms-evidence-metadata:
    evidenceId, incidentId, fileName, fileType,
    s3Key, uploadedBy, uploadedAt, fileSize
  Return 200 OK
```

#### 5.4.7.5 Generate Report Lambda

Responsibility: read aggregated data from DynamoDB, create a CSV report, and save it to S3. In the current MVP, this Lambda is mainly triggered automatically by EventBridge Scheduler.

MVP note: CSV export is the current scope. PDF export is a future enhancement.

```text
EventBridge Scheduler (monthly cron)
  |
  v
Lambda Generate Report
  |
  +-- Determine report time range, defaulting to the current month
  +-- Query or scan irms-incidents
  |     +-- Count incidents by severity
  |     +-- Count incidents by status
  |     +-- Calculate average processing time
  |
  +-- Create CSV file with Python csv library
  +-- PutObject to S3
        Key: reports/{year}/{month}/irms-report-{timestamp}.csv
```

If users want to view or download reports, use `GET /reports` to read the list of report files from S3 or metadata. The frontend should not call report generation directly for the scheduled MVP flow.

#### 5.4.7.6 Alert Handler Lambda

Responsibility: receive events from EventBridge, automatically create an incident in DynamoDB, and send a notification through SNS. GuardDuty and EventBridge rule configuration details are handled in section 5.5.9.

```text
EventBridge Event (GuardDuty Finding)
  |
  v
Alert Handler Lambda
  |
  +-- Parse event detail
  +-- Map GuardDuty severity to IRMS severity
  |     7.0 - 10.0 -> Critical
  |     4.0 - 6.9  -> High
  |     1.0 - 3.9  -> Medium
  |
  +-- PutItem into irms-incidents
  |     incidentId: INC-AUTO-{timestamp}
  |     title: Auto-detected finding
  |     status: Open
  |     source: GuardDuty
  |
  +-- Publish notification to SNS
        Topic: irms-alert-topic
        Subject: [IRMS ALERT] {severity} - {title}
```

#### 5.4.7.7 AI Assistant Lambda

Responsibility: receive authenticated incident context from the frontend, route the request through the AI provider abstraction, call Groq with model `llama-3.1-8b-instant`, and return a structured JSON response to the user interface.

The frontend never stores or sends any provider API key. The Groq API key is stored only in AWS Secrets Manager and is read by the Lambda function at runtime.

**Provider architecture:**

```text
Frontend
  |
  v
API Gateway
  |
  v
Lambda AI Assistant
  |
  v
Groq API
  |
  v
Structured JSON Response
  |
  v
Frontend
```

**Fallback architecture:**

```text
If Groq is unavailable
  |
  v
Rule-based analysis
  |
  v
User receives a fallback notification
```

Amazon Bedrock support remains a future migration option because the provider abstraction keeps the frontend contract unchanged.

**Implemented AI endpoints:**

| Endpoint | Capability |
| --- | --- |
| `POST /ai/analyze` | Incident summary, severity assessment, recommended actions, and next steps |
| `POST /ai/explain` | Explain why a severity was selected, including reasoning, confidence, risk factors, and severity increase/decrease conditions |
| `POST /ai/chat` | Ask questions about the current incident, containment, investigation, evidence collection, reporting, and recovery |

All AI endpoints require Cognito authentication through API Gateway.

Example response for `POST /ai/analyze`:

```json
{
  "severity": "High",
  "confidence": 85,
  "summary": "Multiple failed SSH login attempts indicate a likely brute force attempt.",
  "recommendedActions": [
    "Block the suspicious source IP",
    "Review authentication logs",
    "Verify whether any account was compromised"
  ],
  "nextSteps": [
    "Collect SSH logs as evidence",
    "Update the incident timeline",
    "Notify the responsible security analyst"
  ],
  "provider": "groq",
  "model": "llama-3.1-8b-instant",
  "fallback": false
}
```

**Why the API key is not hardcoded:**

- Automated scanners can detect leaked keys within minutes.
- Secrets Manager allows key rotation without redeploying Lambda.
- No API key is exposed in the React frontend.
- This follows the AWS Well-Architected Framework, Security Pillar.

#### 5.4.7.8 Environment Variables

Open `infrastructure/template.yaml`, find the `Globals` section or each Lambda function, and confirm that the following environment variables exist:

| Environment Variable | Value | Used by |
| --- | --- | --- |
| `ENVIRONMENT` | `dev` | Used to identify the deployment environment |
| `INCIDENTS_TABLE` | `irms-incidents` | `incident-crud`, `alert-handler` |
| `TIMELINE_TABLE` | `irms-timeline` | `incident-crud` |
| `EVIDENCE_METADATA_TABLE` | `irms-evidence-metadata` | `upload-evidence` |
| `USERS_TABLE` | `irms-users` | `incident-crud` |
| `EVIDENCE_BUCKET` | `irms-evidence-031577240048-ap-southeast-1` | `upload-evidence`, `generate-report` |
| `AIProvider` | `groq` | `ai-assistant` |
| `GroqSecretName` | Secrets Manager secret name for the Groq API key | `ai-assistant` |
| `GroqModel` | `llama-3.1-8b-instant` | `ai-assistant` |
| `GroqTimeoutSeconds` | Provider request timeout | `ai-assistant` |

Why use environment variables instead of hardcoding:

- Switching between providers or stages only requires changing configuration values.
- No frontend API key exists.
- Real resource names and secret names can be changed without editing code.
- This follows the 12-Factor App configuration principle.

Example in Python:

```python
import os

TABLE_NAME = os.environ.get("INCIDENTS_TABLE")
BUCKET_NAME = os.environ.get("EVIDENCE_BUCKET")
AI_PROVIDER = os.environ.get("AIProvider", "groq")
GROQ_SECRET_NAME = os.environ.get("GroqSecretName")
GROQ_MODEL = os.environ.get("GroqModel", "llama-3.1-8b-instant")
GROQ_TIMEOUT_SECONDS = int(os.environ.get("GroqTimeoutSeconds", "20"))
```

#### 5.4.7.9 IAM Permissions and Least Privilege

Each Lambda function is granted only the permissions it needs. If a Lambda function is compromised, the attacker can only use what that Lambda is allowed to use, nothing more.

**incident-crud**

- Allowed: `dynamodb:GetItem`, `dynamodb:PutItem`, `dynamodb:UpdateItem`, `dynamodb:DeleteItem`, `dynamodb:Query`
- Scope: `irms-incidents`, `irms-timeline`, and required GSIs
- Not allowed: `dynamodb:*`, `s3:*`

**upload-evidence**

- Allowed: `s3:PutObject`, `s3:GetObject`, `dynamodb:PutItem`
- Scope: evidence bucket and `irms-evidence-metadata`
- Not allowed: `s3:DeleteObject`, `dynamodb:DeleteItem`

**generate-report**

- Allowed: `dynamodb:Query`, `dynamodb:Scan`, `s3:PutObject`
- Scope: `irms-incidents` and the `/reports/` prefix in the evidence bucket
- Not allowed: `dynamodb:DeleteItem`, `dynamodb:UpdateItem`

**alert-handler**

- Allowed: `dynamodb:PutItem`, `sns:Publish`
- Scope: `irms-incidents`, `irms-timeline`, and the `irms-alerts` topic
- Not allowed: `sns:CreateTopic`, `dynamodb:DeleteItem`

**ai-assistant**

- Allowed: `secretsmanager:GetSecretValue`
- Scope: the configured Groq secret only
- Not allowed: broad `secretsmanager:*`, `dynamodb:*`, or `s3:*`

#### 5.4.7.10 Logging and Error Handling

All Lambda functions automatically write logs to Amazon CloudWatch Logs, so no extra configuration is needed. Each Lambda has its own Log Group named `/aws/lambda/<function-name>`.

Use `logger` instead of `print`:

```python
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)

logger.info("Creating incident", extra={"incidentId": incident_id})
logger.error("Failed to create incident", exc_info=True)
```

Recommended fields to log:

- `requestId`: trace a request when an error occurs.
- `user email`: read from JWT claims to identify the actor.
- `incidentId`: trace the related record.
- `action`: for example, `Creating incident` or `Updating status`.
- `error message`: log the exception message without exposing secrets.

Never log:

- Full JWT token.
- Groq API key.
- Full presigned URL.
- Passwords or sensitive personal information.

#### 5.4.7.11 Validate Source Code

This step does not run `sam build` or `sam deploy`; it only verifies the repository structure.

**Step 1: Open Command Prompt (CMD) on Windows.**

```text
Press Windows key -> type cmd -> Enter
```

**Step 2: Move to the repository directory.**

```bat
cd /d "D:\Thuc Tap\irms-serverless"
```

Replace the path with the real path on your machine.

**Step 3: Verify that the backend folder contains all five Lambda functions.**

```bat
dir backend
```

Expected result:

```text
incident-crud
upload-evidence
generate-report
alert-handler
ai-assistant
```

**Step 4: Verify that each Lambda has the required files.**

```bat
dir backend\incident-crud
dir backend\upload-evidence
dir backend\generate-report
dir backend\alert-handler
dir backend\ai-assistant
```

Each folder should contain at least:

```text
handler.py
requirements.txt
```

**Step 5: Verify that `template.yaml` references the correct handler.**

```bat
findstr /C:"Handler:" infrastructure\template.yaml
```

Expected result:

```text
Handler: handler.handler
Handler: handler.handler
Handler: handler.handler
Handler: handler.handler
Handler: handler.handler
```

**Step 6: Verify that `CodeUri` points to the correct folders.**

```bat
findstr /C:"CodeUri:" infrastructure\template.yaml
```

Expected result:

```text
CodeUri: ../backend/incident-crud/
CodeUri: ../backend/upload-evidence/
CodeUri: ../backend/generate-report/
CodeUri: ../backend/ai-assistant/
CodeUri: ../backend/alert-handler/
```

**Step 7: Run `sam validate` one last time.**

```bash
sam validate --template-file infrastructure/template.yaml --region ap-southeast-1
```

Expected result:

```text
Template provided at 'infrastructure/template.yaml' was successfully validated.
```

#### 5.4.7.12 Checklist 7

- Understand the role of AWS Lambda in the IRMS architecture.
- Understand why Lambda was selected instead of EC2.
- Understand the responsibilities of all five Lambda functions.
- Understand the seven environment variables and why values are not hardcoded.
- Understand the Least Privilege principle.
- Confirm that `s3:GeneratePresignedUrl` is not an IAM action.
- Understand that CloudWatch Logs automatically stores Lambda logs.
- Do not log JWT tokens, API keys, presigned URLs, passwords, or sensitive data.
- Verify that the `backend/` folder contains all five Lambda functions.
- Verify that `handler.py` and `requirements.txt` exist.
- Verify that `CodeUri` points to `backend/` in `template.yaml`.
- Confirm that `sam validate` runs successfully.
- `sam build` has not been run yet in this step.
- `sam deploy` has not been run yet in this step.
- No production resources are created in this step.
