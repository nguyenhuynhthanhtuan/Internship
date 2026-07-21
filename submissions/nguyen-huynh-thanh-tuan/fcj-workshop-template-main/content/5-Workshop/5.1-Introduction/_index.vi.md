---
title: "Giới thiệu"
date: 2026-07-17
weight: 1
chapter: false
pre: " <b> 5.1. </b> "
---

## 5.1. Giới thiệu

#### Nội dung

- 5.1.1 Giới thiệu

### 5.1.1 Giới thiệu

#### 5.1.1.1 Tổng quan
IRMS (Incident Response Management System) là hệ thống quản lý sự cố an ninh mạng dạng SaaS, được xây dựng hoàn toàn trên kiến trúc Serverless AWS. Workshop này hướng dẫn bạn triển khai từng bước toàn bộ hệ thống từ đầu đến cuối.
Sau khi hoàn thành workshop, bạn sẽ có một hệ thống IRMS chạy thật trên AWS với đầy đủ chức năng: xác thực người dùng, quản lý Incident, lưu trữ bằng chứng, tự động phát hiện mối đe dọa và hỗ trợ AI phân tích sự cố.

#### 5.1.1.2 Kiến trúc tổng thể
![Sơ đồ kiến trúc IRMS](/Internship/images/5-Workshop/IRMS/IRMS_architecture_v10.drawio.png)

Hệ thống gồm các lớp sau:
Global/Edge:
- CloudFront → S3 Static Website (React + Vite + Stitch UI)
Regional — Entry:
- API Gateway → Cognito (JWT Authorizer)
Regional — Compute:
- 5 Lambda function: Incident CRUD, Upload Evidence, Generate Report, Alert Handler, AI Assistant
Regional — Data:
- DynamoDB (Incidents, Timeline, Users, EvidenceMetadata)
- S3 Evidence Store
Regional — Security & Automation:
- GuardDuty → EventBridge → Lambda Alert Handler → DynamoDB + SNS
External:
- Groq API cho AI Assistant, sử dụng model `llama-3.1-8b-instant`; API key chỉ được lưu trong AWS Secrets Manager. Amazon Bedrock được giữ như lựa chọn mở rộng trong tương lai.

#### 5.1.1.3 Các dịch vụ AWS sử dụng

| Service | Mục đích |
| --- | --- |
| Amazon Route 53 | Future Enhancement: custom domain và DNS |
| Amazon CloudFront | CDN, cổng vào duy nhất |
| AWS WAF | Future Enhancement: bảo vệ web bổ sung |
| Amazon S3 | Web Hosting + Evidence Store |
| Amazon API Gateway | REST API |
| Amazon Cognito | Xác thực, phân quyền |
| AWS Lambda | Xử lý nghiệp vụ |
| Amazon DynamoDB | Database |
| Amazon GuardDuty | Phát hiện mối đe dọa |
| Amazon EventBridge | Event routing, Scheduler |
| Amazon SNS | Gửi thông báo |
| AWS Secrets Manager | Lưu trữ credentials |
| Amazon CloudWatch | Monitoring, Logs |
