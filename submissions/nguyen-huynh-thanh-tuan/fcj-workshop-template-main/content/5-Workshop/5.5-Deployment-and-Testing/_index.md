---
title: "Deployment and Testing"
date: 2026-07-17
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## 5.5. Deployment and Testing

#### Contents

- 5.5.8 API Gateway Production
- 5.5.9 EventBridge & GuardDuty
- 5.5.10 Build & Deploy
- 5.5.11 Testing & Validation

### 5.5.8 API Gateway Production (SAM)

#### 5.5.8.1 API Layer Overview

Amazon API Gateway is the middle layer between the React SPA frontend and the Lambda backend of IRMS. Instead of calling Lambda directly from the browser, every request goes through API Gateway.

```text
React SPA
  |
  v
API Gateway
  |-- GET    /incidents      -> incident-crud Lambda
  |-- POST   /incidents      -> incident-crud Lambda
  |-- PUT    /incidents/{id} -> incident-crud Lambda
  |-- DELETE /incidents/{id} -> incident-crud Lambda
  |
  |-- POST   /evidence       -> upload-evidence Lambda
  |-- POST   /evidence/{id}  -> upload-evidence Lambda
  |
  |-- GET    /reports        -> generate-report Lambda
  |
  |-- POST   /ai/analyze     -> ai-assistant Lambda
  |-- POST   /ai/explain     -> ai-assistant Lambda
  `-- POST   /ai/chat        -> ai-assistant Lambda
```

| Role | Meaning |
| --- | --- |
| Routing | Route each endpoint to the correct Lambda function |
| Authentication | Verify JWT tokens from Amazon Cognito |
| CORS | Allow the React SPA to call the API |
| Security | Avoid exposing Lambda functions directly |
| Production URL | Provide an HTTPS endpoint for the frontend |

#### 5.5.8.2 Why API Gateway Is Needed

Without API Gateway, the frontend does not have a stable HTTPS endpoint for backend calls. API Gateway acts as the main entry point of the system:

```text
User -> Browser -> API Gateway -> Lambda -> DynamoDB / S3 / Groq API
```

Main advantages:

- Automatic HTTPS endpoint.
- Cognito Authorizer integration.
- Central route management.
- Easier request logging.
- Easier expansion when adding new endpoints.
- Good fit for a serverless architecture.

#### 5.5.8.3 API Gateway in the SAM Template

In `infrastructure/template.yaml`, API Gateway is declared as an `AWS::Serverless::Api` resource.

```yaml
IrmsApi:
  Type: AWS::Serverless::Api
  Properties:
    Name: irms-api
    StageName: prod
    Cors:
      AllowMethods: "'GET,POST,PUT,DELETE,OPTIONS'"
      AllowHeaders: "'Content-Type,Authorization'"
      AllowOrigin: "'*'"
```

For local frontend testing, `AllowOrigin: "'*'"` is acceptable. In a real production environment, restrict CORS to the specific frontend domain, for example:

```yaml
AllowOrigin: "'https://your-domain.com'"
```

#### 5.5.8.4 API Routes for Incident CRUD

The Incident routes use the same `incident-crud` Lambda function.

```yaml
Events:
  GetIncidents:
    Type: Api
    Properties:
      RestApiId: !Ref IrmsApi
      Path: /incidents
      Method: GET

  CreateIncident:
    Type: Api
    Properties:
      RestApiId: !Ref IrmsApi
      Path: /incidents
      Method: POST

  UpdateIncident:
    Type: Api
    Properties:
      RestApiId: !Ref IrmsApi
      Path: /incidents/{id}
      Method: PUT

  DeleteIncident:
    Type: Api
    Properties:
      RestApiId: !Ref IrmsApi
      Path: /incidents/{id}
      Method: DELETE
```

| Method | Path | Function |
| --- | --- | --- |
| GET | `/incidents` | Get the incident list |
| POST | `/incidents` | Create a new incident |
| PUT | `/incidents/{id}` | Update an incident |
| DELETE | `/incidents/{id}` | Delete an incident |

#### 5.5.8.5 API Routes for Evidence

Evidence upload has two steps: get a presigned URL, then confirm metadata after upload.

```yaml
Events:
  CreateEvidenceUploadUrl:
    Type: Api
    Properties:
      RestApiId: !Ref IrmsApi
      Path: /evidence
      Method: POST

  ConfirmEvidenceUpload:
    Type: Api
    Properties:
      RestApiId: !Ref IrmsApi
      Path: /evidence/{id}
      Method: POST
```

```text
Browser
  |-- POST /evidence
  |     `-- Lambda returns a presigned URL
  |
  |-- PUT file directly to S3 with presigned URL
  |
  `-- POST /evidence/{id}
        `-- Lambda writes metadata into DynamoDB
```

The file does not pass through API Gateway, which reduces load and cost.

#### 5.5.8.6 API Routes for AI Assistant

The AI Assistant uses three Cognito-protected endpoints:

| Endpoint | Purpose |
| --- | --- |
| `POST /ai/analyze` | Analyze the incident, summarize it, assess severity, recommend actions, and suggest next steps |
| `POST /ai/explain` | Explain why a severity was selected, including reasoning, confidence, risk factors, and severity change conditions |
| `POST /ai/chat` | Ask incident-specific questions about containment, investigation, evidence collection, reporting, and recovery |

Example SAM events:

```yaml
Events:
  AnalyzeIncident:
    Type: Api
    Properties:
      RestApiId: !Ref IrmsApi
      Path: /ai/analyze
      Method: POST
  ExplainSeverity:
    Type: Api
    Properties:
      RestApiId: !Ref IrmsApi
      Path: /ai/explain
      Method: POST
  ChatWithAI:
    Type: Api
    Properties:
      RestApiId: !Ref IrmsApi
      Path: /ai/chat
      Method: POST
```

Example request for `POST /ai/analyze`:

```json
{
  "incidentId": "INC-001",
  "description": "Multiple failed SSH login attempts from unknown IP"
}
```

Example response:

```json
{
  "severity": "High",
  "confidence": 85,
  "summary": "Possible SSH brute force attack",
  "recommendedActions": [
    "Block suspicious IP",
    "Review SSH logs",
    "Enable MFA"
  ],
  "nextSteps": [
    "Collect evidence",
    "Update the incident timeline"
  ],
  "provider": "groq",
  "model": "llama-3.1-8b-instant",
  "fallback": false
}
```

#### 5.5.8.7 Cognito Authorizer

To protect the API, API Gateway verifies JWT tokens from Amazon Cognito.

```text
User logs in with Cognito
  |
  v
Frontend receives Access Token / ID Token
  |
  v
Frontend calls API with:
Authorization: Bearer <JWT_TOKEN>
  |
  v
API Gateway Cognito Authorizer verifies token
  |-- Valid token   -> call Lambda
  `-- Invalid token -> return 401 Unauthorized
```

Example SAM configuration:

```yaml
Auth:
  DefaultAuthorizer: CognitoAuthorizer
  Authorizers:
    CognitoAuthorizer:
      UserPoolArn: !GetAtt IrmsUserPool.Arn
```

#### 5.5.8.8 CORS Configuration

CORS is required because the React SPA frontend and API Gateway use different domains.

```text
Frontend local: http://localhost:3000
API Gateway   : https://abc123.execute-api.ap-southeast-1.amazonaws.com/prod
```

Example SAM CORS configuration:

```yaml
Cors:
  AllowMethods: "'GET,POST,PUT,DELETE,OPTIONS'"
  AllowHeaders: "'Content-Type,Authorization'"
  AllowOrigin: "'*'"
```

In production, replace `*` with the real frontend domain.

#### 5.5.8.9 Validate API Gateway in `template.yaml`

This step does not deploy yet. It verifies that API Gateway and required routes are already declared in the template.

**Step 1: Open CMD and go to the repository.**

```bat
cd /d "D:\Thuc Tap\irms-serverless"
```

**Step 2: Verify `AWS::Serverless::Api`.**

```bat
findstr /C:"AWS::Serverless::Api" infrastructure\template.yaml
```

Expected result:

```text
Type: AWS::Serverless::Api
```

![API Gateway Production 1](/Internship/images/5-Workshop/IRMS/section-08-001.png)

**Step 3: Verify Incident routes.**

```bat
findstr /C:"/incidents" infrastructure\template.yaml
```

Expected result:

```text
Path: /incidents
Path: /incidents/{id}
```

![API Gateway Production 2](/Internship/images/5-Workshop/IRMS/section-08-002.png)

**Step 4: Verify Evidence routes.**

```bat
findstr /C:"/evidence" infrastructure\template.yaml
```

Expected result:

```text
Path: /evidence
Path: /evidence/{id}
```

![API Gateway Production 3](/Internship/images/5-Workshop/IRMS/section-08-003.png)

**Step 5: Verify AI route.**

```bat
findstr /C:"/ai/analyze" infrastructure\template.yaml
findstr /C:"/ai/explain" infrastructure\template.yaml
findstr /C:"/ai/chat" infrastructure\template.yaml
```

Expected result:

```text
Path: /ai/analyze
Path: /ai/explain
Path: /ai/chat
```

![API Gateway Production 4](/Internship/images/5-Workshop/IRMS/section-08-004.png)

**Step 6: Verify CORS.**

```bat
findstr /C:"Cors:" infrastructure\template.yaml
findstr /C:"AllowOrigin" infrastructure\template.yaml
findstr /C:"AllowHeaders" infrastructure\template.yaml
findstr /C:"AllowMethods" infrastructure\template.yaml
```

![API Gateway Production 5](/Internship/images/5-Workshop/IRMS/section-08-005.png)

**Step 7: Verify Cognito Authorizer.**

```bat
findstr /C:"Authorizer" infrastructure\template.yaml
findstr /C:"UserPoolArn" infrastructure\template.yaml
```

Expected result should include `DefaultAuthorizer`, `CognitoAuthorizer`, and `UserPoolArn`.

![API Gateway Production 6](/Internship/images/5-Workshop/IRMS/section-08-006.png)

**Step 8: Validate the SAM template.**

```bash
sam validate --template-file infrastructure/template.yaml --region ap-southeast-1
```

Expected result:

```text
infrastructure/template.yaml is a valid SAM Template.
```

![API Gateway Production 7](/Internship/images/5-Workshop/IRMS/section-08-007.png)

#### 5.5.8.10 Checklist 8

- Understand the role of API Gateway in the IRMS architecture.
- Understand why the frontend does not call Lambda directly.
- Understand how API Gateway routes requests to each Lambda function.
- Verify `AWS::Serverless::Api` in `template.yaml`.
- Verify routes for `/incidents`, `/evidence`, `/ai/analyze`, `/ai/explain`, and `/ai/chat`.
- Understand CORS and why the frontend needs it.
- Understand how Cognito Authorizer protects the API with JWT tokens.
- Verify the Authorizer configuration in `template.yaml`.
- Confirm that `sam validate` runs successfully.
- `sam build` and `sam deploy` have not been run yet in this section.

### 5.5.9 EventBridge, GuardDuty Simulation and Alert Automation

#### 5.5.9.1 Event-driven Alert Automation Overview

This section verifies automatic incident creation when a security event occurs.

```text
GuardDuty Finding / Simulated Event
  |
  v
Amazon EventBridge
  |
  v
Alert Handler Lambda
  |-- Create a new incident in DynamoDB
  `-- Send notification through SNS
```

In the MVP, a simulated event is used instead of real GuardDuty findings to reduce cost and simplify the demo.

#### 5.5.9.2 Role of EventBridge

EventBridge helps IRMS receive security events in an event-driven way. Instead of requiring an analyst to create incidents manually, the system can automatically create an incident when a matching event arrives.

```json
{
  "source": "aws.guardduty",
  "detail-type": "GuardDuty Finding",
  "detail": {
    "severity": 8.0,
    "type": "UnauthorizedAccess:EC2/SSHBruteForce",
    "description": "SSH brute force attack detected"
  }
}
```

#### 5.5.9.3 Alert Handler Lambda

The `alert-handler` Lambda receives an EventBridge event, parses it, maps severity, creates an incident, writes a timeline record, and sends an SNS notification.

```text
7.0 - 10.0 -> Critical
4.0 - 6.9  -> High
1.0 - 3.9  -> Medium
```

#### 5.5.9.4 SNS Notification

SNS sends a notification when an incident is created automatically.

```text
Topic  : irms-alert-topic
Subject: [IRMS ALERT] Critical - Auto-detected GuardDuty Finding
Message: New Critical incident created from GuardDuty finding.
```

In the internship demo, subscribe a personal or team email address to the SNS topic to receive alerts.

#### 5.5.9.5 Validate EventBridge in `template.yaml`

**Step 1: Go to the repository.**

```bat
cd /d "D:\Thuc Tap\irms-serverless"
```

**Step 2: Verify EventBridge rule.**

```bat
findstr /C:"AWS::Events::Rule" infrastructure\template.yaml
```

![EventBridge & GuardDuty 1](/Internship/images/5-Workshop/IRMS/section-09-001.png)

**Step 3: Verify EventBridge pattern.**

```bat
findstr /C:"EventPattern" infrastructure\template.yaml
findstr /C:"aws.guardduty" infrastructure\template.yaml
findstr /C:"GuardDuty Finding" infrastructure\template.yaml
```

![EventBridge & GuardDuty 2](/Internship/images/5-Workshop/IRMS/section-09-002.png)

**Step 4: Verify that `alert-handler` is triggered by EventBridge.**

```bat
findstr /C:"alert-handler" infrastructure\template.yaml
findstr /C:"AlertHandler" infrastructure\template.yaml
```

![EventBridge & GuardDuty 3](/Internship/images/5-Workshop/IRMS/section-09-003.png)

**Step 5: Verify SNS topic and publish permission.**

```bat
findstr /C:"TopicName" infrastructure\template.yaml
findstr /C:"SNS" infrastructure\template.yaml
```

![EventBridge & GuardDuty 4](/Internship/images/5-Workshop/IRMS/section-09-004.png)
![EventBridge & GuardDuty 5](/Internship/images/5-Workshop/IRMS/section-09-005.png)
![EventBridge & GuardDuty 6](/Internship/images/5-Workshop/IRMS/section-09-006.png)

**Step 6: Verify Lambda permission for EventBridge.**

```bat
findstr /C:"AWS::Lambda::Permission" infrastructure\template.yaml
findstr /C:"events.amazonaws.com" infrastructure\template.yaml
```

![EventBridge & GuardDuty 7](/Internship/images/5-Workshop/IRMS/section-09-007.png)

**Step 7: Validate SAM template.**

```bash
sam validate --template-file infrastructure/template.yaml --region ap-southeast-1
```

#### 5.5.9.6 Checklist 9

- Understand that EventBridge is used to receive security events.
- Understand that Alert Handler Lambda creates incidents automatically.
- Understand the severity mapping from GuardDuty to IRMS.
- Understand that SNS is used to send alerts.
- Verify `AWS::Events::Rule`, `EventPattern`, SNS topic, and Lambda permission for EventBridge.
- Confirm that `sam validate` runs successfully.
- `sam build` and `sam deploy` have not been run yet in this section.

### 5.5.10 Build & Deploy with AWS SAM

#### 5.5.10.1 Objective

After this section, the backend system is deployed to AWS.

```text
CloudFormation Stack
  |-- API Gateway
  |-- Lambda Functions
  |-- DynamoDB Tables
  |-- SNS Topic
  |-- EventBridge Rules
  |-- IAM Roles
  |-- CloudWatch Log Groups
  `-- Permissions
```

The React frontend is deployed later in section 5.6.

#### 5.5.10.2 Verify Tools and Profile

```bash
aws --version
sam --version
aws configure list-profiles
aws sts get-caller-identity --profile irms-shared
```

The expected profile is `irms-shared`. If configured correctly, STS returns `Account`, `UserId`, and `Arn`.

#### 5.5.10.3 Build Project

```bat
cd /d "D:\Thuc Tap\irms-serverless"
sam build ^
  --template-file infrastructure\template.yaml
```

SAM copies Lambda source code, installs dependencies from `requirements.txt`, and creates the `.aws-sam` build directory.

![Build & Deploy 1](/Internship/images/5-Workshop/IRMS/section-10-001.png)

Verify build output:

```bat
dir .aws-sam
dir .aws-sam\build
```

Expected build folders:

```text
IncidentCrudFunction
UploadEvidenceFunction
GenerateReportFunction
AlertHandlerFunction
AiAssistantFunction
template.yaml
```

![Build & Deploy 2](/Internship/images/5-Workshop/IRMS/section-10-002.png)

#### 5.5.10.4 Deploy Guided

Before the first deployment, prepare Cognito parameters.

```bash
aws cognito-idp list-user-pools --max-results 10 --region ap-southeast-1 --profile irms-shared
aws cognito-idp list-user-pool-clients --user-pool-id YOUR_USER_POOL_ID --region ap-southeast-1 --profile irms-shared
```

Run guided deployment:

```bat
sam deploy --guided ^
  --template-file .aws-sam\build\template.yaml ^
  --profile irms-shared
```

Typical answers:

```text
Stack Name: irms-serverless
Region: ap-southeast-1
Confirm changes before deploy: N
Allow SAM IAM: Y
Save configuration: Y
```

After the first deployment, SAM generates `samconfig.toml`.

![Build & Deploy 3](/Internship/images/5-Workshop/IRMS/section-10-003.png)

#### 5.5.10.5 Subsequent Deployments

For later deployments:

```bash
sam build
sam deploy
```

Guided deployment is no longer required.

#### 5.5.10.6 Validate Deployment

```bash
aws cloudformation list-stacks --profile irms-shared
aws lambda list-functions --profile irms-shared
aws apigateway get-rest-apis --profile irms-shared
```

![Build & Deploy 4](/Internship/images/5-Workshop/IRMS/section-10-004.png)
![Build & Deploy 5](/Internship/images/5-Workshop/IRMS/section-10-005.png)
![Build & Deploy 6](/Internship/images/5-Workshop/IRMS/section-10-006.png)
![Build & Deploy 7](/Internship/images/5-Workshop/IRMS/section-10-007.png)

#### 5.5.10.7 Checklist

- AWS CLI works.
- SAM CLI works.
- Profile `irms-shared` works.
- `sam build` completes successfully.
- `.aws-sam` is created.
- `sam deploy` completes successfully.
- CloudFormation stack, API Gateway, Lambda, DynamoDB, EventBridge, and SNS are created.

### 5.5.11 Functional Testing & Validation

#### 5.5.11.1 Verify API Gateway Deployment

Goal: confirm that API Gateway has been deployed successfully, has the `dev` stage, has an Invoke URL, and exposes the expected endpoints.

Open AWS Console:

```text
API Gateway -> irms-api -> Stages -> dev
```

![Testing & Validation 1](/Internship/images/5-Workshop/IRMS/section-11-001.png)

List API resources:

```bash
aws apigateway get-resources ^
  --rest-api-id 07frck8bih ^
  --region ap-southeast-1 ^
  --profile irms-shared
```

![Testing & Validation 2](/Internship/images/5-Workshop/IRMS/section-11-002.png)
![Testing & Validation 3](/Internship/images/5-Workshop/IRMS/section-11-003.png)
![Testing & Validation 4](/Internship/images/5-Workshop/IRMS/section-11-004.png)
![Testing & Validation 5](/Internship/images/5-Workshop/IRMS/section-11-005.png)

#### 5.5.11.2 Test API Health Check

Goal: verify the flow from client to API Gateway to Lambda.

```bash
curl.exe https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/ping
```

![Testing & Validation 6](/Internship/images/5-Workshop/IRMS/section-11-006.png)
![Testing & Validation 7](/Internship/images/5-Workshop/IRMS/section-11-007.png)

#### 5.5.11.3 Test Incident CRUD

Goal: verify the full incident management flow.

```text
Client -> API Gateway -> IncidentCrud Lambda -> DynamoDB -> HTTP Response
```

Inspect the Lambda handler:

```bat
type backend\incident-crud\handler.py
```

![Testing & Validation 8](/Internship/images/5-Workshop/IRMS/section-11-008.png)

Create an incident:

```bash
curl.exe -X POST ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"title\":\"Unauthorized SSH Login\",\"description\":\"Multiple failed SSH login attempts detected\",\"severity\":\"High\",\"source\":\"manual\",\"assignedTo\":\"Security Analyst\"}"
```

![Testing & Validation 9](/Internship/images/5-Workshop/IRMS/section-11-009.png)
![Testing & Validation 10](/Internship/images/5-Workshop/IRMS/section-11-010.png)
![Testing & Validation 11](/Internship/images/5-Workshop/IRMS/section-11-011.png)

Get the incident list:

```bash
curl.exe ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents ^
  -H "Authorization: Bearer YOUR_ID_TOKEN"
```

![Testing & Validation 12](/Internship/images/5-Workshop/IRMS/section-11-012.png)

Get incident details:

```bash
curl.exe ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents/INC-53DB6458 ^
  -H "Authorization: Bearer YOUR_ID_TOKEN"
```

![Testing & Validation 13](/Internship/images/5-Workshop/IRMS/section-11-013.png)

Update incident status:

```bash
curl.exe -X PUT ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents/INC-53DB6458 ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"status\":\"In Progress\",\"assignedTo\":\"Security Analyst\",\"actor\":\"analyst@example.com\"}"
```

![Testing & Validation 14](/Internship/images/5-Workshop/IRMS/section-11-014.png)

Get timeline:

```bash
curl.exe ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents/INC-53DB6458/timeline ^
  -H "Authorization: Bearer YOUR_ID_TOKEN"
```

![Testing & Validation 15](/Internship/images/5-Workshop/IRMS/section-11-015.png)

Verify DynamoDB:

```text
DynamoDB -> Tables -> irms-incidents -> Explore table items
DynamoDB -> Tables -> irms-timeline -> Explore table items
```

![Testing & Validation 16](/Internship/images/5-Workshop/IRMS/section-11-016.png)
![Testing & Validation 17](/Internship/images/5-Workshop/IRMS/section-11-017.png)

#### 5.5.11.4 Test Evidence Upload

Goal: verify evidence metadata creation, presigned URL generation, S3 upload, DynamoDB metadata, and retrieval through API.

```text
Client -> API Gateway -> UploadEvidence Lambda
                       |-- Generate presigned URL
                       |-- Write metadata to DynamoDB
Browser -> S3          |-- Upload file directly through presigned URL
```

Inspect source code:

```bat
more backend\upload-evidence\handler.py
```

![Testing & Validation 18](/Internship/images/5-Workshop/IRMS/section-11-018.png)
![Testing & Validation 19](/Internship/images/5-Workshop/IRMS/section-11-019.png)
![Testing & Validation 20](/Internship/images/5-Workshop/IRMS/section-11-020.png)

Create a test file:

```bat
echo This is IRMS evidence test file > evidence-test.txt
dir evidence-test.txt
```

![Testing & Validation 21](/Internship/images/5-Workshop/IRMS/section-11-021.png)

Create evidence metadata and get upload URL:

```bash
curl.exe -X POST ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/evidence ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"incidentId\":\"INC-53DB6458\",\"fileName\":\"evidence-test.txt\",\"fileType\":\"text/plain\",\"description\":\"Test evidence upload from CMD\",\"uploadedBy\":\"analyst@example.com\",\"fileSize\":32}"
```

![Testing & Validation 22](/Internship/images/5-Workshop/IRMS/section-11-022.png)

Upload the file to S3 using the presigned URL:

```bash
curl.exe -X PUT ^
  "PASTE_UPLOAD_URL_HERE" ^
  -H "Content-Type: text/plain" ^
  --upload-file evidence-test.txt
```

![Testing & Validation 23](/Internship/images/5-Workshop/IRMS/section-11-023.png)

Get evidence details:

```bash
curl.exe ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/evidence/845d4a06-f3c2-4a4d-84bb-d937a522da46 ^
  -H "Authorization: Bearer YOUR_ID_TOKEN"
```

![Testing & Validation 24](/Internship/images/5-Workshop/IRMS/section-11-024.png)

#### 5.5.11.5 Test AI Assistant

Goal: verify that authenticated requests reach the AI Assistant Lambda, the Lambda reads the Groq API key from Secrets Manager, and the provider abstraction returns a structured response or a safe fallback response.

![Testing & Validation 25](/Internship/images/5-Workshop/IRMS/section-11-025.png)
![Testing & Validation 26](/Internship/images/5-Workshop/IRMS/section-11-026.png)

Verify the configured Groq secret:

```bash
aws secretsmanager get-secret-value ^
  --secret-id YOUR_GROQ_SECRET_NAME ^
  --region ap-southeast-1 ^
  --profile irms-shared
```

Call incident analysis:

```bash
curl.exe -X POST ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/ai/analyze ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"incident\":{\"title\":\"Unauthorized SSH Login\",\"description\":\"Multiple failed SSH login attempts detected from unknown IP\",\"severity\":\"High\",\"source\":\"manual\"}}"
```

Call severity explanation:

```bash
curl.exe -X POST ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/ai/explain ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"incidentId\":\"INC-53DB6458\",\"severity\":\"High\"}"
```

Ask an incident-specific question:

```bash
curl.exe -X POST ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/ai/chat ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"incidentId\":\"INC-53DB6458\",\"question\":\"What evidence should I collect next?\"}"
```

If Groq is unavailable or times out, the Lambda returns a fallback notification and rule-based analysis so the user can continue the incident response workflow.

![Testing & Validation 28](/Internship/images/5-Workshop/IRMS/section-11-028.png)

#### 5.5.11.6 Test Generate Report

Get report list:

```bash
curl.exe ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/reports ^
  -H "Authorization: Bearer YOUR_ID_TOKEN"
```

![Testing & Validation 29](/Internship/images/5-Workshop/IRMS/section-11-029.png)

Generate report for an incident:

```bash
curl.exe -X POST ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/reports/generate ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"incidentId\":\"INC-53DB6458\"}"
```

![Testing & Validation 30](/Internship/images/5-Workshop/IRMS/section-11-030.png)

#### 5.5.11.7 Test EventBridge Alert Automation

This verifies the automated flow:

```text
Simulated GuardDuty Event -> EventBridge Rule -> AlertHandler Lambda -> DynamoDB + SNS
```

Create `guardduty-event.json`:

```json
[
  {
    "Source": "aws.guardduty",
    "DetailType": "GuardDuty Finding",
    "Detail": "{\"title\":\"Simulated SSH Brute Force Attack\",\"severity\":\"Critical\",\"description\":\"Simulated GuardDuty finding: multiple SSH brute force attempts detected from suspicious IP.\",\"source\":\"guardduty-simulation\",\"assignedTo\":\"soc-team\"}",
    "EventBusName": "default"
  }
]
```

Send the event to EventBridge:

```bash
aws events put-events ^
  --entries file://guardduty-event.json ^
  --region ap-southeast-1 ^
  --profile irms-shared
```

![Testing & Validation 31](/Internship/images/5-Workshop/IRMS/section-11-031.png)

Verify that an incident was created:

```bash
curl.exe ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents ^
  -H "Authorization: Bearer YOUR_ID_TOKEN"
```

![Testing & Validation 32](/Internship/images/5-Workshop/IRMS/section-11-032.png)

Verify DynamoDB and CloudWatch:

```bash
aws logs describe-log-groups ^
  --log-group-name-prefix /aws/lambda/alert-handler ^
  --region ap-southeast-1 ^
  --profile irms-shared
```

![Testing & Validation 33](/Internship/images/5-Workshop/IRMS/section-11-033.png)
![Testing & Validation 34](/Internship/images/5-Workshop/IRMS/section-11-034.png)

#### 5.5.11.8 CloudWatch Logs and Final Validation

Run final verification commands:

```bash
aws cloudformation describe-stacks ^
  --stack-name irms-serverless ^
  --region ap-southeast-1 ^
  --profile irms-shared ^
  --query "Stacks[0].StackStatus"

aws lambda list-functions ^
  --region ap-southeast-1 ^
  --profile irms-shared ^
  --query "Functions[].FunctionName"

aws dynamodb list-tables ^
  --region ap-southeast-1 ^
  --profile irms-shared

aws s3 ls ^
  --profile irms-shared

aws events list-rules ^
  --region ap-southeast-1 ^
  --profile irms-shared
```

![Testing & Validation 35](/Internship/images/5-Workshop/IRMS/section-11-035.png)
![Testing & Validation 36](/Internship/images/5-Workshop/IRMS/section-11-036.png)
![Testing & Validation 37](/Internship/images/5-Workshop/IRMS/section-11-037.png)
![Testing & Validation 38](/Internship/images/5-Workshop/IRMS/section-11-038.png)
![Testing & Validation 39](/Internship/images/5-Workshop/IRMS/section-11-039.png)

Expected final result:

- API Gateway calls Lambda successfully.
- Incident CRUD works.
- Timeline works.
- Evidence upload to S3 works.
- AI Assistant reaches Groq through the provider abstraction or returns the rule-based fallback response.
- EventBridge creates incidents automatically.
- SNS alert flow works.
- CSV report is created in S3.
- CloudWatch Logs contain verification logs.
