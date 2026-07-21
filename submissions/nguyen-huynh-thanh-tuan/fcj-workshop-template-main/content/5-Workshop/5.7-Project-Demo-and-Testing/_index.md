---
title: "Project Demo and Testing"
date: 2026-07-17
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## 5.7. Project Demo and Testing

#### Contents

- 5.7.1 Demo access
- 5.7.2 Project roles
- 5.7.3 Login and dashboard check
- 5.7.4 Incident workflow test
- 5.7.5 Evidence upload and verification
- 5.7.6 AI-assisted incident analysis
- 5.7.7 AI Chat validation

This part is placed after the web deployment step. Its purpose is to verify that the deployed IRMS application can be used by a Security Analyst from the browser, not only tested through backend commands.

### 5.7.1 Demo access

Use the deployed frontend URL from the CloudFront distribution created in the previous section.

| Item | Value |
| --- | --- |
| Project URL | [https://d1cdyvxfzvnsxg.cloudfront.net/](https://d1cdyvxfzvnsxg.cloudfront.net/) |
| Main test flow | Security Analyst |

Demo users for testing. The guided workflow uses the Analyst account; to test other roles, contact the owner for login details:

| Role | Username | Password |
| --- | --- | --- |
| Admin | `admin@irms-demo.com` | Contact the owner for testing access |
| Manager | `manager@irms-demo.com` | Contact the owner for testing access |
| Analyst | `analyst@irms-demo.com` | Contact the owner for testing access |
| Auditor | `auditor@irms-demo.com` | Contact the owner for testing access |

### 5.7.2 Project roles

| Role | Main responsibility | Used during this test |
| --- | --- | --- |
| Security Analyst | Login, create incidents, update timeline, upload evidence, and request AI assistance | Yes |
| Incident Manager | Review incident status, severity, assignment, and report output | Optional |
| AWS Infrastructure / Backend | Maintain Cognito, API Gateway, Lambda, DynamoDB, S3, EventBridge, SNS, CloudFront, and Secrets Manager | Validation support |
| AI Assistant | Provide advisory analysis and chat-based security guidance | Yes |
| Report / Alert Handler | Generate reports and support event-driven notification flow | Validation support |

### 5.7.3 Login and dashboard check

Open the project URL in the browser and sign in with the Analyst demo account listed above.

![IRMS login screen](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-06.png)

After login, verify that the dashboard loads correctly and shows incident status cards, recent incidents, and security posture information.

![IRMS dashboard](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-07.png)

Expected result:

- The analyst is authenticated through Amazon Cognito.
- The frontend receives a valid session and can call protected API Gateway routes.
- The dashboard renders live data instead of a static mock screen.

### 5.7.4 Incident workflow test

Create a new incident from the Incidents page.

![Create new incident](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-09.png)

Submit the form and confirm that the incident appears in the incident list.

![Incident created successfully](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-10.png)

Open the new incident detail page and verify the status, severity, assignment, timeline, and evidence panel.

![Incident detail](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-11.png)

Add an investigation or containment timeline entry.

![Add timeline entry](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-12.png)

![Timeline entry saved](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-13.png)

Expected result:

- The incident is created through the Cognito-protected API.
- The incident record is stored in DynamoDB.
- Every state transition or analyst note is recorded in the incident timeline.

### 5.7.5 Evidence upload and verification

Select a local evidence file and associate it with the incident.

![Evidence upload form](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-14.png)

The frontend detects the file name, MIME type, and file size before upload.

![Evidence upload details](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-15.png)

Submit the evidence. The browser uploads the file directly to Amazon S3 using a short-lived presigned PUT URL.

![Submit evidence](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-16.png)

Verify the uploaded evidence object in the private S3 evidence bucket.

![Evidence object in S3](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-17.png)

Verify the metadata item in DynamoDB.

![Evidence metadata in DynamoDB](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-18.png)

Expected result:

- The Cognito token is sent only to API Gateway.
- The S3 upload request does not include the Cognito token.
- The original evidence file is stored in S3.
- Evidence metadata is stored separately in DynamoDB.

### 5.7.6 AI-assisted incident analysis

Open the AI Assistant page and choose **AI Analyst** mode.

![AI Assistant page](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-19.png)

Select the incident created during the test.

![Select incident for AI analysis](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-20.png)

Review the structured AI result.

![AI Analyst result](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-21.png)

The result summarizes likely impact, response priorities, recommendations, and supporting context.

![AI findings](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-22.png)

Important validation:

- AI output is advisory only.
- Incident status, severity, and assignee remain unchanged unless an authorized user modifies them.
- The AI provider key is read by Lambda through Secrets Manager and is not exposed to the frontend.

### 5.7.7 AI Chat validation

Switch to **AI Chat** mode.

![AI Chat mode](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-24.png)

Use a prompt related to the selected incident, for example:

```text
Explain why this incident has its current severity.
```

![AI Chat response](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-25.png)

Expected result:

- AI Chat answers the analyst question using the selected incident as context.
- The chat retrieves only incidents that the authenticated Security Analyst is authorized to access.
- The response is used as guidance and does not automatically change incident records.

