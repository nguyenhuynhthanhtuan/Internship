---
title: "Infrastructure Configuration"
date: 2026-07-17
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## 5.3. Infrastructure Configuration

#### Contents

- 5.3.3 Setup Authentication
- 5.3.4 Setup API Gateway
- 5.3.5 Setup DynamoDB
- 5.3.6 Setup S3 & Storage

### 5.3.3 Setup Authentication (Amazon Cognito)

#### 5.3.3.1 Prepare Before Starting
Open these two things first:
First, open the AWS Console in the browser:
- Open the browser (Chrome/Edge), go to: https://console.aws.amazon.com
- Sign in with your IAM user
- In the top-right corner, check the selected region — must be Asia Pacific (Singapore) ap-southeast-1. If it is not correct, click the region name → select Asia Pacific (Singapore)
Second, open a terminal on the local machine:
Windows: Press the Windows key → type PowerShell → Enter. or open Git Bash if Git is already installed.
Mac: Press Cmd + Space → type Terminal → Enter.
Configure AWS CLI
-Create an access key
- Create a profile, then enter the access key and secret key
Verify that the AWS CLI is configured correctly with this command:

```bash
aws sts get-caller-identity --profile irms-shared
```

![Setup Authentication 1](/Internship/images/5-Workshop/IRMS/section-03-001.png)
Expected result. The Account ID and UserId should match your AWS account:

```json
{
"UserId": "AIDXXXXXXXXXXXXXXXXX",
"Account": "123456789012",
"Arn": "arn:aws:iam::123456789012:user/your-username"
}
```

#### 5.3.3.2 Create a Cognito User Pool (in the AWS Console)

**Step 1: In the AWS Console search bar, type Cognito → click Amazon Cognito**

**Step 2: Click Create user pool (in the top-right corner)**

**Step 3: Configure sign-in experience:**

- Authentication providers: select Cognito user pool
- Cognito user pool sign-in options: check Email
- Click Next

**Step 4: Configure security requirements:**

- Password policy mode: select Custom
- Minimum length: 8
- Check: Contains at least 1 uppercase, 1 lowercase, 1 number, 1 special character
- Multi-factor authentication: select No MFA (demo is not needed)
- Click Next

**Step 5: Configure sign-up experience:**

- Keep all default settings
- Click Next

**Step 6: Configure message delivery:**

- Email provider: select Send email with Cognito (free and sufficient for the demo)
- Click Next

**Step 7: Integrate your app:**

- User pool name: irms-user-pool
- App client name: irms-web-client
- Client secret: select Don't generate a client secret
- Click Next

**Step 8: Review and create:**

- Scroll down and review all settings
- Click Create user pool

**Step 9: After creation, the new User Pool page appears. Save these two values to a local note file:**

User Pool ID:  ap-southeast-1_XXXXXXXXX  ← shown in the "Overview" tab
Client ID:     XXXXXXXXXXXXXXXXXXXXXXXXXX ← shown in the "App clients" tab
These two values are used many times in later steps, so save them carefully.

#### 5.3.3.3 Create Four Groups (RBAC)
Still on the Cognito User Pool that you just created:

**Step 1: Click tab Groups → Click Create group**

**Step 2: Create Group 1:**

Group name:  Admin
Description: Full system permissions
Precedence:  1
Click Create group

**Step 3: Repeat, create three more groups:**

Group name:  SecurityManager
Description: View all incidents, assign incidents, and view reports
Precedence:  2

Group name:  SecurityAnalyst
Description: Investigate incidents, upload evidence, and update status
Precedence:  3

Group name:  Auditor
Description: Read-only access
Precedence:  4
After finishing, the Groups tab should show four groups as follows:
Admin            (Precedence: 1)
SecurityManager  (Precedence: 2)
SecurityAnalyst  (Precedence: 3)
Auditor          (Precedence: 4)
![Setup Authentication 2](/Internship/images/5-Workshop/IRMS/section-03-002.png)

#### 5.3.3.4 Create a Test User
Still on the User Pool, click tab Users → Create user:
User 1 (Admin):
Invitation message:     Don't send an invitation
Email address:          admin@example.com
Email address verified: check ✅
Temporary password:     select "Set a password"
Password:               YOUR_TEMP_PASSWORD
User must create a new password: uncheck the option
Click Create user → click to user that you just created → Add user to group → select Admin
![Setup Authentication 3](/Internship/images/5-Workshop/IRMS/section-03-003.png)
Repeat the same steps for the remaining three users and assign each user to the correct group:
manager@example.com  → group SecurityManager  → pass: YOUR_TEMP_PASSWORD
analyst@example.com  → group SecurityAnalyst   → pass: YOUR_TEMP_PASSWORD
auditor@example.com  → group Auditor           → pass: YOUR_TEMP_PASSWORD
![Setup Authentication 4](/Internship/images/5-Workshop/IRMS/section-03-004.png)

#### 5.3.3.5 Verify from the Terminal
Open a terminal on your machine and run the following command. Replace `YOUR_CLIENT_ID` with the Client ID saved earlier in section 5.3.3.2:

```bash
aws cognito-idp initiate-auth \
--auth-flow USER_PASSWORD_AUTH \
--auth-parameters USERNAME=analyst@example.com,PASSWORD=YOUR_TEMP_PASSWORD \
--client-id YOUR_CLIENT_ID \
--region ap-southeast-1 \
--profile irms-shared
```

Expected result — seeing three tokens means success:

```json
{
"AuthenticationResult": {
"AccessToken": "eyJraWQiOi...",
"IdToken": "eyJraWQiOi...",
"RefreshToken": "eyJjdHki...",
"ExpiresIn": 3600,
"TokenType": "Bearer"
}
}
```
![Setup Authentication 5](/Internship/images/5-Workshop/IRMS/section-03-005.png)
![Setup Authentication 6](/Internship/images/5-Workshop/IRMS/section-03-006.png)
![Setup Authentication 7](/Internship/images/5-Workshop/IRMS/section-03-007.png)
If you see the error NotAuthorizedException → verify password or email.
If you see the error UserNotFoundException → verify email was created correctly.
Verify JWT token:
- Copy the full IdToken string (which is long and starts with eyJ...)
- Open the browser, go to https://jwt.io
- Paste it into the Encoded box on the left
- In the Payload section on the right, you should see:

```json
{
"email": "analyst@example.com",
"cognito:groups": ["SecurityAnalyst"]
}
```
If these two fields are visible, Cognito is configured correctly.
![Setup Authentication 8](/Internship/images/5-Workshop/IRMS/section-03-008.png)

### 5.3.4 Setup API Gateway
Note about the approach: this part uses the Console only to understand the Cognito Authorizer mechanism and test JWT tokens. The real API routes (`/incidents`, `/evidence`, `/reports`, `/ai`) are generated by SAM later when deploying Lambda, so do not create them manually here to avoid conflicts.

#### 5.3.4.1 Create the REST API

**Step 1: In the AWS Console search bar, type API Gateway → click API Gateway**

**Step 2: Click Create API**

![Setup API Gateway 1](/Internship/images/5-Workshop/IRMS/section-04-001.png)

**Step 3: Select the API type:**

- Select REST API (not REST API Private and not HTTP API)
- Click Build
![Setup API Gateway 2](/Internship/images/5-Workshop/IRMS/section-04-002.png)

**Step 4: Enter information:**

API details:        New API
API name:           irms-api-learning
Description:        Used to learn the Authorizer mechanism; delete it before moving to the production API section
API endpoint type:  Regional
Click Create API
![Setup API Gateway 3](/Internship/images/5-Workshop/IRMS/section-04-003.png)
⚠ This API is named `irms-api-learning` to distinguish it from the real API created later by SAM. After testing the Authorizer, this learning API can be deleted without affecting the system.

#### 5.3.4.2 Create a Cognito Authorizer

**Step 1: Left menu → click Authorizers → click Create authorizer**

**Step 2: Enter information:**

Authorizer name:    irms-cognito-authorizer
Authorizer type:    Cognito
Cognito user pool:  select "irms-user-pool"
Token source:       Authorization
Token validation:   (leave blank)
Click Create authorizer
![Setup API Gateway 4](/Internship/images/5-Workshop/IRMS/section-04-004.png)
![Setup API Gateway 5](/Internship/images/5-Workshop/IRMS/section-04-005.png)

#### 5.3.4.3 Create Two Test Routes
Go to Resources in the left menu. Objective: create two routes for clear testing:
GET /ping       → does not have an Authorizer → always returns 200
GET /ping-auth  → has Authorizer       → 401 without a token, 200 with a valid token
Create /ping (does not have Authorizer):

**Step 1: Click / (root) → click Create resource**

Resource name:  ping
Resource path:  /ping
CORS:           do not check (because SAM will manage this later)
Click Create resource

**Step 2: Click /ping → click Create method**

Method type:        GET
Integration type:   Mock
![Setup API Gateway 6](/Internship/images/5-Workshop/IRMS/section-04-006.png)
Click Create method
![Setup API Gateway 7](/Internship/images/5-Workshop/IRMS/section-04-007.png)

**Step 3: Go to Integration Response → Mapping Templates → add a template:**

Content-Type:   application/json
Template body:
{
"message": "pong",
"auth": false
}
Click Save

**Step 4: Confirm that /ping does not use an Authorizer:**

- Click method GET
- Tab Method request → Authorization: must be NONE

Create /ping-auth (has Authorizer):

**Step 1: Click / (root) → click Create resource**

Resource name:  ping-auth
Resource path:  /ping-auth
CORS:           do not check
Click Create resource

**Step 2: Click /ping-auth → click Create method**

Method type:        GET
Integration type:   Mock
Click Create method
![Setup API Gateway 8](/Internship/images/5-Workshop/IRMS/section-04-008.png)

**Step 3: Go to Integration Response → Mapping Templates → add a template:**

Content-Type:   application/json
Template body:
{
"message": "Authorized",
"auth": true
}
Click Save
![Setup API Gateway 9](/Internship/images/5-Workshop/IRMS/section-04-009.png)

**Step 4: Attach the Authorizer to /ping-auth:**

- Click method GET of /ping-auth
- Tab Method request → Authorization → click Edit
- Select irms-cognito-authorizer
- Click Save

#### 5.3.4.4 Deploy the dev Stage

**Step 1: Click Deploy API (in the top-right corner)**

**Step 2: In the Deploy API dialog:**

Stage:       *New stage*
Stage name:  dev
Click Deploy
![Setup API Gateway 10](/Internship/images/5-Workshop/IRMS/section-04-010.png)
![Setup API Gateway 11](/Internship/images/5-Workshop/IRMS/section-04-011.png)

**Step 3: The Stage Editor appears → save the Invoke URL:**

Invoke URL: https://ymag7i4369.execute-api.ap-southeast-1.amazonaws.com/dev
⚠ This URL is only used to test the Authorizer in this section. The real system URL is created later by SAM and will have a different value, so use the SAM output URL at that point.

#### 5.3.4.5 Test with Postman
Install Postman if it is not already installed:
- Go to https://www.postman.com/downloads/
- Download → install → open it → select Skip (account creation is not required)
Get the JWT token from Cognito:
Open Terminal (Windows: PowerShell or Git Bash / Mac: Terminal), then run the following command. Replace `YOUR_CLIENT_ID` with the Client ID saved earlier:
aws cognito-idp initiate-auth \
--auth-flow USER_PASSWORD_AUTH \
--auth-parameters USERNAME=analyst@example.com,PASSWORD=YOUR_PASSWORD\
--client-id YOUR_CLIENT_ID \
--region ap-southeast-1 \
--profile irms-shared
![Setup API Gateway 12](/Internship/images/5-Workshop/IRMS/section-04-012.png)
Copy the full IdToken string (starts with eyJ...) to Notepad.

Test 1 — /ping does not require a token:
Open Postman:
- Method: GET
- URL: https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/dev/ping
- do not add anything in Headers
- Click Send
Expected result:
{
"message": "pong",
"auth": false
}
![Setup API Gateway 13](/Internship/images/5-Workshop/IRMS/section-04-013.png)
Status: 200 OK → ✅ API Gateway works

Test 2 — /ping-auth without a token:
- Method: GET
- URL: https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/dev/ping-auth
- do not add anything in Headers
- Click Send
Expected result:
{
"message": "Unauthorized"
}
![Setup API Gateway 14](/Internship/images/5-Workshop/IRMS/section-04-014.png)
Status: 401 Unauthorized → ✅ Authorizer is blocking requests correctly

Test 3 — /ping-auth has token:
- Method: GET
- URL: https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/dev/ping-auth
- Headers tab -> add:
Key:    Authorization
Value:  Bearer <eyJraWQiOi...>   ← paste the IdToken here
- Click Send
Expected result:
{
"message": "Authorized",
"auth": true
}
![Setup API Gateway 15](/Internship/images/5-Workshop/IRMS/section-04-015.png)
Status: 200 OK → ✅ Authorizer JWT authentication succeeds

Test 4 — /ping-auth with an invalid token:
- Keep the same setup Test 3 but replace the token with any random string:
Authorization: Bearer thisisafaketoken
- Click Send
Expected result:
{
"message": "Unauthorized"
}
![Setup API Gateway 16](/Internship/images/5-Workshop/IRMS/section-04-016.png)
Status: 401 Unauthorized → ✅ Authorizer rejects the invalid token

#### 5.3.4.6 Checklist 4
Before moving to 5, confirm the following items:
- ✅ API Gateway (Regional) already create ten irms-api-learning
- ✅ Cognito Authorizer already create, points to irms-user-pool
- ✅ GET /ping  → Mock, does not have Authorizer
- ✅ GET /ping-auth → Mock, has Cognito Authorizer
- ✅ Deployed the dev stage
- ✅ Test Postman:
/ping          → 200 (does not require a token)
/ping-auth     → 401 (without a token)
/ping-auth     → 200 (has valid token)
/ping-auth     → 401 (an invalid token)
- ✅ Saved the Invoke URL for temporary testing

### 5.3.5 Setup DynamoDB

#### 5.3.5.1 Design the Schema Before Creation
Before using the Console, understand the data structure. IRMS uses four tables:
Incidents          → store incident information
Timeline           → store the processing timeline
EvidenceMetadata   → store evidence file metadata
Users              → store user information
Table details:
Table Incidents:
Partition key:  incidentId (String)
GSI 1:          status-index     → query by status (Open/Investigating/Closed)
GSI 2:          assignedTo-index → query by assigned user
Attributes:
incidentId      String   "INC-001"
title           String   "SQL Injection Attack"
severity        String   "Critical / High / Medium / Low"
status          String   "Open / Investigating / Containment / Resolved / Closed"
description     String   description details
assignedTo      String   email of the assigned user
createdBy       String   email of the creator
createdAt       String   ISO timestamp
updatedAt       String   ISO timestamp

Table Timeline:
Partition key:  incidentId (String)
Sort key:       timestamp  (String)
Attributes:
incidentId   String
timestamp    String   ISO timestamp
action       String   "Created / Assigned / Status Changed / Note Added"
description  String   action description
performedBy  String   email of the user performing the action

Table EvidenceMetadata:
Partition key:  evidenceId  (String)
GSI 1:          incidentId-index → query evidence theo incident
Attributes:
evidenceId    String   UUID
incidentId    String   "INC-001"
fileName      String   "screenshot.png"
fileType      String   "image/png"
s3Key         String   S3 path
uploadedBy    String   email of the uploader
uploadedAt    String   ISO timestamp
fileSize      Number   bytes

Table Users:
Partition key:  email (String)
Attributes:
email         String
fullName      String
role          String   "Admin / SecurityManager / SecurityAnalyst / Auditor"
createdAt     String

#### 5.3.5.2 Create Table Incidents

**Step 1: In the AWS Console search bar, type DynamoDB → click DynamoDB**

**Step 2: Click Create table**

**Step 3: Enter basic information:**

Table name:           irms-incidents
Partition key:        incidentId    Type: String
Sort key:             (leave blank)
![Setup DynamoDB 1](/Internship/images/5-Workshop/IRMS/section-05-001.png)

**Step 4: Table settings → select Customize settings**

**Step 5: Table class: DynamoDB Standard**

**Step 6: Read/write capacity settings:**

Capacity mode: On-demand
![Setup DynamoDB 2](/Internship/images/5-Workshop/IRMS/section-05-002.png)
Select On-demand because the demo does not have stable traffic. You only pay per actual request, which avoids charges when the table is idle.

**Step 7: Encryption: Owned by Amazon DynamoDB (default and sufficient for the demo)**

![Setup DynamoDB 3](/Internship/images/5-Workshop/IRMS/section-05-003.png)

**Step 8: Click Create table, wait about 30 seconds until the status changes to Active**

![Setup DynamoDB 4](/Internship/images/5-Workshop/IRMS/section-05-004.png)

#### 5.3.5.3 Create the GSI for the Incidents Table
After the table becomes Active, create Global Secondary Index to query quickly theo status and assignedTo:

**Step 1: Click table irms-incidents → tab Indexes → click Create index**

![Setup DynamoDB 5](/Internship/images/5-Workshop/IRMS/section-05-005.png)
Create GSI 1 — query by status:
Partition key:   status      Type: String
Sort key:        createdAt   Type: String
Index name:      status-index
Projected attributes: All
Click Create index → wait until the status changes to Active
![Setup DynamoDB 6](/Internship/images/5-Workshop/IRMS/section-05-006.png)
Create GSI 2 — query by assigned user:
Click Create index again:
Partition key:   assignedTo   Type: String
Sort key:        createdAt    Type: String
Index name:      assignedTo-index
Projected attributes: All
Click Create index → wait until it becomes Active
![Setup DynamoDB 7](/Internship/images/5-Workshop/IRMS/section-05-007.png)

#### 5.3.5.4 Create Table Timeline

**Step 1: Click Create table**

**Step 2:**
Table name:      irms-timeline
Partition key:   incidentId    Type: String
Sort key:        timestamp     Type: String
The sort key is `timestamp` because one incident can have many timeline entries. The sort key automatically orders them by time.

**Step 3: Table settings → Customize settings → Capacity mode: On-demand**

**Step 4: Click Create table → wait until it becomes Active**

![Setup DynamoDB 8](/Internship/images/5-Workshop/IRMS/section-05-008.png)

#### 5.3.5.5 Create Table EvidenceMetadata

**Step 1: Click Create table**

**Step 2:**
Table name:      irms-evidence-metadata
Partition key:   evidenceId    Type: String
Sort key:        (leave blank)

**Step 3: Table settings → Customize settings → Capacity mode: On-demand**

**Step 4: Click Create table → wait until it becomes Active**

**Step 5: Create a GSI to query evidence by incidentId:**

Tab Indexes → Create index:
Partition key:   incidentId    Type: String
Sort key:        uploadedAt    Type: String
Index name:      incidentId-index
Projected attributes: All
Click Create index → wait until it becomes Active
![Setup DynamoDB 9](/Internship/images/5-Workshop/IRMS/section-05-009.png)

#### 5.3.5.6 Create Table Users

**Step 1: Click Create table**

**Step 2:**
Table name:      irms-users
Partition key:   email    Type: String
Sort key:        (leave blank)

**Step 3: Table settings → Customize settings → Capacity mode: On-demand**

**Step 4: Click Create table → wait until it becomes Active**

![Setup DynamoDB 10](/Internship/images/5-Workshop/IRMS/section-05-010.png)

#### 5.3.5.7 Add Sample Data for Testing
After creating all four tables, add a few sample records to the Incidents table to test Lambda later:

**Step 1: Click table irms-incidents → tab Explore items → click Create item**

![Setup DynamoDB 11](/Internship/images/5-Workshop/IRMS/section-05-011.png)

**Step 2: Select JSON view, paste the following content:**

{
"incidentId": { "S": "INC-001" },
"title": { "S": "Public S3 Bucket Detected" },
"severity": { "S": "Critical" },
"status": { "S": "Open" },
"description": { "S": "S3 bucket company-data has public access enabled, which may expose customer data" },
"assignedTo": { "S": "analyst@example.com" },
"createdBy": { "S": "admin@example.com" },
"createdAt": { "S": "2026-06-01T08:00:00Z" },
"updatedAt": { "S": "2026-06-01T08:00:00Z" }
}
Click Create item

**Step 3: Create the second sample record:**

{
"incidentId": "INC-002",
"title": "SSH Brute Force Attack",
"severity": "High",
"status": "Investigating",
"description": "Detected 500 failed SSH login attempts from IP 203.0.113.42 within 10 minutes",
"assignedTo": "analyst@example.com",
"createdBy": "manager@example.com",
"createdAt": "2026-06-02T10:30:00Z",
"updatedAt": "2026-06-02T11:00:00Z"
}
Click Create item
![Setup DynamoDB 12](/Internship/images/5-Workshop/IRMS/section-05-012.png)
![Setup DynamoDB 13](/Internship/images/5-Workshop/IRMS/section-05-013.png)

#### 5.3.5.8 Verify from the Console

**Step 1: Click table irms-incidents → tab Explore items**

**Step 2: Find the two records INC-001 and INC-002 → ✅**

![Setup DynamoDB 14](/Internship/images/5-Workshop/IRMS/section-05-014.png)

**Step 3: Test query by GSI:**

- Click Scan/Query → select Query
- Index: select status-index
- Partition key value: Open
- Click Run
- The result should return INC-001 → ✅ GSI works correctly
![Setup DynamoDB 15](/Internship/images/5-Workshop/IRMS/section-05-015.png)

#### 5.3.5.9 Hybrid Deployment with AWS SAM
In this workshop, the DynamoDB tables are created manually in the AWS Console first so learners can clearly understand schema design, Partition Key, Sort Key, and Global Secondary Index (GSI).
After completing the hands-on Console steps, the IRMS project moves to a Hybrid Deployment model.
In this model:
- Amazon Cognito and Amazon DynamoDB are kept from the earlier Console configuration steps.
- AWS SAM deploys only the remaining system components, such as:
- Amazon API Gateway
- AWS Lambda
- Amazon S3
- Amazon SNS
- Amazon EventBridge
- AWS Secrets Manager
This helps:
- Avoid creating duplicate DynamoDB tables.
- Keep the sample data created in section 5.
- Help learners understand manual configuration while becoming familiar with Infrastructure as Code (IaC) using AWS SAM.
DynamoDB tables used by SAM
Instead of create a new item, the AWS SAM template references existing tables directly:

| Table | Purpose |
| --- | --- |
| irms-incidents | Save information Incident |
| irms-timeline | Store the processing timeline |
| irms-evidence-metadata | Save metadata Evidence |
| irms-users | Save information user |

Table names are passed to Lambda through SAM Parameters and Environment Variables, so they do not need to be recreated with CloudFormation.
Template file used
In the IRMS source repository, the infrastructure is managed by:
infrastructure/template.yaml
This template is already configured for the Hybrid Deployment model, including:
- Use the existing Amazon Cognito User Pool.
- Use the DynamoDB tables created in section 5.
- Create new resources:
- Amazon API Gateway
- AWS Lambda Functions
- Amazon S3 Buckets
- Amazon SNS
- Amazon EventBridge
- AWS Secrets Manager
Verify Configuration
need to verify template before deploy with command:
sam validate ^
  --template-file infrastructure/template.yaml ^
  --region ap-southeast-1
Expected result:
Template provided at 'infrastructure/template.yaml' was successfully validated.
![Setup DynamoDB 16](/Internship/images/5-Workshop/IRMS/section-05-016.png)

#### 5.3.5.10 Checklist 5
- ✅ Successfully created 4 table DynamoDB:
- irms-incidents
- irms-timeline
- irms-evidence-metadata
- irms-users
- ✅ The two Global Secondary Indexes of the irms-incidents works:
- status-index
- assignedTo-index
- ✅ The Global Secondary Index of the irms-evidence-metadata works:
- incidentId-index
- ✅ Added two sample records (`INC-001`, `INC-002`) to the `irms-incidents` table.
- ✅ Verified that queries through `status-index` return the correct result.
- ✅ Successfully validated the Hybrid SAM template for the next deployment stage.

### 5.3.6 Setup S3 & Storage

#### 5.3.6.1 Storage Design
IRMS uses two S3 buckets with completely different purposes:
irms-frontend-031577240048-ap-southeast-1   → store the React SPA (public through CloudFront)
irms-evidence-031577240048-ap-southeast-1   → store evidence files (private, accessed only by Lambda)
The reason for adding the Account ID and Region to the bucket name is that S3 bucket names are globally unique. Using Account ID + Region keeps the name unique and avoids conflicts with other users.
Comparison of the two buckets:

|  | irms-frontend-... | irms-evidence-... |
| --- | --- | --- |
| Public access | Blocked, only CloudFront can read | Completely blocked |
| Accessed by | CloudFront | Lambda function |
| Stored content | HTML, CSS, JS, assets | Screenshot, log, PCAP, PDF report |
| Versioning | is not needed | Enabled to protect evidence |
| CORS | is not needed | Required for presigned URL uploads from the browser |

#### 5.3.6.2 Evidence Upload Flow

Understand the evidence upload flow to see why the Evidence bucket needs CORS.

```text
Browser                 Lambda                    S3
   |                       |                       |
   |-- POST /evidence ---->|                       |
   |   filename, type      |                       |
   |                       |-- Generate URL ------>|
   |                       |<-- presignedUrl ------|
   |<-- presignedUrl ------|                       |
   |                                               |
   |-- PUT presignedUrl -------------------------->|
   |   file binary                                 |
   |<-- 200 OK ------------------------------------|
   |                       |                       |
   |-- POST /evidence/{id}->|                       |
   |   evidenceId          |-- PutItem ----------->| DynamoDB
   |<-- 200 OK ------------|                       |
```

Advantages of Presigned URL: files are uploaded directly from the browser to S3 without passing through Lambda, which avoids the 6 MB Lambda payload limit and saves bandwidth.

#### 5.3.6.3 Verify S3 Configuration in `template.yaml`

The template already exists in the repository. In this step, only read and understand the configuration; do not add new resources.

Open `infrastructure/template.yaml`, find the S3 section, and review each property.

**Frontend Bucket**

```yaml
FrontendBucket:
  Type: AWS::S3::Bucket
  Properties:
    BucketName: !Ref FrontendBucketName
    PublicAccessBlockConfiguration:
      BlockPublicAcls: true
      BlockPublicPolicy: true
      IgnorePublicAcls: true
      RestrictPublicBuckets: true
```

Key points to note:

- `BlockPublicAcls: true`: no one can set a public ACL on objects.
- `BlockPublicPolicy: true`: no one can create a bucket policy that allows public access.
- There is no `WebsiteConfiguration` because the frontend is distributed through CloudFront, not direct S3 static website hosting.

**Evidence Bucket**

```yaml
EvidenceBucket:
  Type: AWS::S3::Bucket
  Properties:
    BucketName: !Ref EvidenceBucketName
    PublicAccessBlockConfiguration:
      BlockPublicAcls: true
      BlockPublicPolicy: true
      IgnorePublicAcls: true
      RestrictPublicBuckets: true
    VersioningConfiguration:
      Status: Enabled
    CorsConfiguration:
      CorsRules:
        - AllowedHeaders:
            - "*"
          AllowedMethods:
            - PUT
            - GET
          AllowedOrigins:
            - "*"
          MaxAge: 3600
```

Key points to note:

- `VersioningConfiguration: Enabled`: each upload with the same file name creates a new version instead of overwriting the old one, which is important for evidence integrity.
- `CorsRules` allows browser upload through a presigned URL.
- `MaxAge: 3600` lets the browser cache the CORS preflight response for one hour and reduces repeated `OPTIONS` requests.

#### 5.3.6.4 Verify Parameters in `template.yaml`

Find the `Parameters` section and confirm that both bucket names and default values are correct.

```yaml
Parameters:
  FrontendBucketName:
    Type: String
    Default: irms-frontend-031577240048-ap-southeast-1

  EvidenceBucketName:
    Type: String
    Default: irms-evidence-031577240048-ap-southeast-1
```

If both names are correct, continue.

![Setup S3 & Storage 1](/Internship/images/5-Workshop/IRMS/section-06-001.png)

#### 5.3.6.5 Verify Lambda Environment Variables in `template.yaml`

Lambda needs the Evidence bucket name to create presigned URLs. Find the `Globals` section and confirm that it contains:

```yaml
Globals:
  Function:
    Environment:
      Variables:
        EVIDENCE_BUCKET: !Ref EvidenceBucketName
```

If the variable is configured only for the Upload Evidence Lambda, it should look like this:

```yaml
UploadEvidenceFunction:
  Type: AWS::Serverless::Function
  Properties:
    Environment:
      Variables:
        EVIDENCE_BUCKET: !Ref EvidenceBucketName
```

#### 5.3.6.6 Verify `samconfig.toml`

Open `infrastructure/samconfig.toml` and confirm the deployment configuration.

```toml
version = 0.1

[default.deploy.parameters]
stack_name          = "irms-dev"
region              = "ap-southeast-1"
confirm_changeset   = false
capabilities        = "CAPABILITY_IAM CAPABILITY_NAMED_IAM"
profile             = "irms-shared"

parameter_overrides = [
  "Environment=dev",
  "UserPoolId=ap-southeast-1_XXXXXXXXX",
  "UserPoolClientId=XXXXXXXXXXXXXXXXXXXXXXXXXX",
  "UserPoolArn=arn:aws:cognito-idp:ap-southeast-1:031577240048:userpool/ap-southeast-1_XXXXXXXXX",
  "IncidentsTableName=irms-incidents",
  "TimelineTableName=irms-timeline",
  "EvidenceMetadataTableName=irms-evidence-metadata",
  "UsersTableName=irms-users",
  "FrontendBucketName=irms-frontend-031577240048-ap-southeast-1",
  "EvidenceBucketName=irms-evidence-031577240048-ap-southeast-1"
]
```

Replace the `XXXXXXXXX` placeholders with the real values saved in section 5.3.3.

![Setup S3 & Storage 2](/Internship/images/5-Workshop/IRMS/section-06-002.png)

#### 5.3.6.7 Validate Template

After confirming all items above, run `sam validate` to verify that the template has no syntax errors.

```bash
cd irms
sam validate ^
  --template-file infrastructure/template.yaml ^
  --region ap-southeast-1
```

Expected result:

```text
Template provided at 'infrastructure/template.yaml' was successfully validated.
```

![Setup S3 & Storage 3](/Internship/images/5-Workshop/IRMS/section-06-003.png)

Common errors:

- `Template format error: YAML not well-formed`: usually caused by incorrect indentation. Open `template.yaml` and check the line shown in the error message. Each indentation level should use two spaces, not tabs.
- `InvalidResourceException`: a resource is referencing another resource that does not exist in the template yet. If it references CloudFront or WAF that will be added later, confirm it with the team.

#### 5.3.6.8 Checklist 6
- ✅ Understand the purpose of the two buckets:
irms-frontend-031577240048-ap-southeast-1  → React SPA through CloudFront
irms-evidence-031577240048-ap-southeast-1  → evidence files through Lambda
- ✅ Understand the Presigned URL flow (Browser → Lambda gets URL → PUT directly to S3)
- ✅ Understand the configuration of FrontendBucket in template.yaml:
BlockPublicAcls, BlockPublicPolicy = true
- ✅ Understand the configuration of EvidenceBucket in template.yaml:
Versioning ON, CORS for PUT
- ✅ Confirm Parameters FrontendBucketName and EvidenceBucketName uses the correct names
- ✅ Confirm `EVIDENCE_BUCKET` exists in Lambda environment variables
- ✅ Confirm `samconfig.toml` uses `stack_name` = "irms-dev"
- ✅ `sam validate` runs successfully
