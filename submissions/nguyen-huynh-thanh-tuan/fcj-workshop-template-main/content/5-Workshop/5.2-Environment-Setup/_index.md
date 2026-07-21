---
title: "Environment Setup"
date: 2026-07-17
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## 5.2. Environment Setup

#### Contents

- 5.2.2 Prerequisites

### 5.2.2 Prerequisites

#### 5.2.2.1 AWS Account and Permissions

- An active AWS account. In this workshop, the team used one shared AWS account.
- An IAM user with enough permissions to create and configure the required AWS services.
- Region: `ap-southeast-1` (Singapore). All team members should use the same region throughout the workshop.

#### 5.2.2.2 Install Tools

Install and verify the tools used to build and deploy the IRMS application.

**AWS CLI**

```bash
# Verify installation
aws --version

# Configure the shared profile after installation
aws configure --profile irms-shared
# AWS Access Key ID: <enter key>
# AWS Secret Access Key: <enter secret>
# Default region name: ap-southeast-1
# Default output format: json
```

If the AWS CLI is not installed yet, follow the official AWS CLI v2 installation guide:
`https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html`

**AWS SAM CLI**

```bash
# Verify installation
sam --version
```

If SAM CLI is not installed yet, follow the official SAM CLI installation guide:
`https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html`

Expected result:

```text
SAM CLI, version 1.x.x
```

**Node.js and npm**

Node.js is required for the React + Vite frontend.

```bash
node --version   # >= 18.x
npm --version
```

**Python**

Python is required for the Lambda functions.

```bash
python --version   # >= 3.12
```

#### 5.2.2.3 Set Up a Budget Alert

This step should be completed before deploying any AWS resources. A budget alert helps the team monitor spending and avoid unexpected charges.

1. Open AWS Console -> Billing -> Budgets -> Create budget.
![Prerequisites 1](/Internship/images/5-Workshop/IRMS/section-02-001.png)
2. Select **Cost budget**.
3. Budget name: `irms-demo-budget`.
4. Budgeted amount: `$5`.
5. Alert threshold: `80%`, which means AWS sends an alert when the cost reaches `$4`.
6. Notification email: use the team lead email or a team mailbox.
7. Select **Create budget**.
![Prerequisites 2](/Internship/images/5-Workshop/IRMS/section-02-002.png)

#### 5.2.2.4 Project Folder Structure

The project is organized so infrastructure templates, Lambda functions, frontend code, and documentation can be maintained separately.

```text
irms/
|-- infrastructure/
|   |-- template-auth.yaml
|   |-- template-api.yaml
|   |-- template-database.yaml
|   |-- template-storage.yaml
|   |-- template-security.yaml
|   `-- template-ai.yaml
|-- functions/
|   |-- incident-crud/
|   |   `-- handler.py
|   |-- upload-evidence/
|   |   `-- handler.py
|   |-- generate-report/
|   |   `-- handler.py
|   |-- alert-handler/
|   |   `-- handler.py
|   `-- ai-assistant/
|       `-- handler.py
|-- frontend/
|   `-- React SPA
|-- docs/
|   |-- architecture/
|   `-- api-spec.md
`-- README.md
```
