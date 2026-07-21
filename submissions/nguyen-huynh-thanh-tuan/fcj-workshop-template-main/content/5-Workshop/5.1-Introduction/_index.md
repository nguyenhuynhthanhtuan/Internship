---
title: "Introduction"
date: 2026-07-17
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## 5.1. Introduction

#### Contents

- 5.1.1 Introduction

### 5.1.1 Introduction

#### 5.1.1.1 Overview
IRMS (Incident Response Management System) is a SaaS-style security incident management system built entirely on AWS serverless architecture. This workshop guides you through deploying the system step by step from start to finish.
After completing the workshop, you will have a working IRMS system on AWS with core functions such as user authentication, incident management, evidence storage, automatic threat detection, and AI-assisted incident analysis.

#### 5.1.1.2 Overall Architecture
![IRMS Architecture Diagram](/Internship/images/5-Workshop/IRMS/IRMS_architecture_v10.drawio.png)

The system includes the following layers:
Global/Edge:
- CloudFront → S3 Static Website (React + Vite + Stitch UI)
Regional — Entry:
- API Gateway → Cognito (JWT Authorizer)
Regional — Compute:
- 5 Lambda functions: Incident CRUD, Upload Evidence, Generate Report, Alert Handler, AI Assistant
Regional — Data:
- DynamoDB (Incidents, Timeline, Users, EvidenceMetadata)
- S3 Evidence Store
Regional — Security & Automation:
- GuardDuty → EventBridge → Lambda Alert Handler → DynamoDB + SNS
External:
- Groq API for AI Assistant, using model `llama-3.1-8b-instant`; the API key is stored only in AWS Secrets Manager. Amazon Bedrock remains an optional future provider.

#### 5.1.1.3 AWS Services Used

| Service | Purpose |
| --- | --- |
| Amazon Route 53 | Future Enhancement: custom domain and DNS resolution |
| Amazon CloudFront | CDN, single entry point |
| AWS WAF | Future Enhancement: additional web application protection |
| Amazon S3 | Web Hosting + Evidence Store |
| Amazon API Gateway | REST API |
| Amazon Cognito | Authentication and authorization |
| AWS Lambda | Business logic processing |
| Amazon DynamoDB | Database |
| Amazon GuardDuty | Threat detection |
| Amazon EventBridge | Event routing, Scheduler |
| Amazon SNS | Send notifications |
| AWS Secrets Manager | Store credentials |
| Amazon CloudWatch | Monitoring, Logs |
