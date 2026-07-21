# IRMS AWS Internship Report

**Live Demo:** [View Report](https://mxt2003.github.io/Internship/)

[![AWS](https://img.shields.io/badge/AWS-Serverless-orange)](https://aws.amazon.com/)
[![Hugo](https://img.shields.io/badge/Hugo-Workshop%20Report-blue)](https://gohugo.io/)
[![GitHub Pages](https://img.shields.io/badge/GitHub%20Pages-Live%20Demo-brightgreen)](https://mxt2003.github.io/Internship/)

## Overview

This repository contains my First Cloud AI Journey internship report and the AWS Workshop Studio-style documentation for **IRMS - Incident Response Management System**.

IRMS is a serverless incident response management project built by a team of five members during the internship. My report focuses on my personal contributions, especially AWS infrastructure, AWS SAM, Cognito, API Gateway, Lambda, DynamoDB integration, EventBridge, SNS, CloudFront, React frontend integration, deployment, testing, and documentation.

## Final Implementation

The final IRMS implementation documented in this repository uses:

- **Frontend:** React + Vite, Stitch AI-assisted UI integration, deployed as static assets.
- **Hosting:** Amazon S3 and Amazon CloudFront.
- **Authentication:** Amazon Cognito with JWT-based API protection.
- **API Layer:** Amazon API Gateway REST API.
- **Compute:** AWS Lambda functions for incidents, evidence upload, reports, alerts, and AI assistance.
- **Database:** Amazon DynamoDB for incidents, timeline, users, and evidence metadata.
- **Storage:** Amazon S3 for evidence files and generated report artifacts.
- **Automation:** Amazon EventBridge and Amazon SNS for alert workflows.
- **AI Provider:** Groq with model `llama-3.1-8b-instant`.
- **Secret Management:** AWS Secrets Manager for the Groq API key.
- **Fallback:** Rule-based AI fallback when the provider is unavailable.
- **Future Enhancements:** Amazon Route 53, AWS WAF, and Amazon Bedrock.

## Report Contents

The published report includes seven main sections:

1. **Worklog** - personal internship progress from AWS labs to final IRMS completion.
2. **Proposal** - IRMS project proposal, architecture, risks, timeline, and expected outcomes.
3. **Translated Blogs** - AWS blog translation summaries and technical lessons learned.
4. **Events Participated** - AWS/cloud/AI events attended during the internship.
5. **Workshop** - step-by-step IRMS implementation documentation.
6. **Self-Assessment** - reflection on technical skills, teamwork, documentation, and limitations.
7. **Sharing and Feedback** - personal feedback about the internship program.

## Repository Structure

```text
Internship/
|-- README.md
|-- .github/workflows/
|   `-- deploy-nguyen-huynh-thanh-tuan-report.yml
`-- submissions/
    `-- nguyen-huynh-thanh-tuan/
        `-- fcj-workshop-template-main/
            |-- config.toml
            |-- content/
            |-- static/
            `-- themes/
```

## Local Development

The Hugo site is located at:

```text
submissions/nguyen-huynh-thanh-tuan/fcj-workshop-template-main
```

Run the report locally:

```bash
cd submissions/nguyen-huynh-thanh-tuan/fcj-workshop-template-main
hugo server -D
```

Build the production site:

```bash
hugo --minify --baseURL "https://nguyenhuynhthanhtuan.github.io/Intership/"
```

## Deployment

The report is deployed to GitHub Pages through the workflow:

```text
.github/workflows/deploy-nguyen-huynh-thanh-tuan-report.yml
```

Live report URL:

```text
https://nguyenhuynhthanhtuan.github.io/Intership/
```

## Internship Duration

April 17, 2026 - July 30, 2026

## Author

**Nguyen Huynh Thanh Tuan**  
FCAJ Cloud Intern - Cybersecurity specialization
