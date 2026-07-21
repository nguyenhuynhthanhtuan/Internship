---
title: "Tích hợp frontend và triển khai web"
date: 2026-07-17
weight: 6
chapter: false
pre: " <b> 5.6. </b> "
---

## 5.6. Tích hợp frontend và triển khai web

#### Nội dung

- 5.6.12 Frontend & CloudFront

### 5.6.12 Frontend Integration & Web Deployment

#### 5.6.12.1 Tích hợp giao diện Stitch AI

Sau khi backend và API hoàn thành, nhóm cải thiện giao diện bằng Stitch AI. Giao diện được chuyển đổi sang React + Vite và giữ lại logic xác thực, gọi API hiện có.

UI hiện tại gồm:

- Dashboard
- Incidents
- Incident Detail
- Evidence
- Reports
- AI Assistant Panel

Trong trang Incident Detail, AI Assistant Panel gồm:

- AI Analysis
- Explain Severity
- Ask AI
- Suggested Questions
- Loading state khi request AI đang chạy
- Fallback message khi provider không khả dụng

Giao diện dùng cách gọi trung lập như AI Assistant, AI Analysis và Incident Intelligence. Frontend không hiển thị branding provider và không chứa API key.

Quá trình tích hợp gồm:

1. Export màn hình từ Stitch AI.
2. Chuyển giao diện thành React + Vite components.
3. Giữ luồng Cognito authentication.
4. Kết nối các màn hình với API Gateway endpoints.
5. Thay mock data bằng dữ liệu thật từ DynamoDB thông qua Lambda.
6. Kiểm thử AI Assistant Panel với `/ai/analyze`, `/ai/explain` và `/ai/chat`.

#### 5.6.12.2 Build React Application

Sau khi hoàn thành frontend, project được build bằng Vite.

```bash
cd frontend
npm install
npm run build
```

Sau khi build thành công, Vite tạo thư mục `dist/` để deploy.

#### 5.6.12.3 Deploy Frontend lên Amazon S3

Upload build output lên S3 frontend bucket.

```bash
aws s3 sync dist s3://irms-frontend-031577240048-ap-southeast-1   --delete   --profile irms-shared
```

Lệnh này upload HTML, CSS, JavaScript và static assets lên Amazon S3. Frontend không có API key.

#### 5.6.12.4 Deploy CloudFront bằng AWS SAM

CloudFront được khai báo trực tiếp trong `template.yaml` để giữ kiến trúc serverless và Infrastructure as Code.

CloudFront được cấu hình:

- HTTPS
- Redirect HTTP sang HTTPS
- Compression
- `PriceClass_100`
- React SPA support

Custom error response giúp React Router hoạt động đúng khi người dùng refresh trang.

| HTTP Error | Response |
| --- | --- |
| 403 | `index.html` với status `200` |
| 404 | `index.html` với status `200` |

Build và deploy backend + CloudFront stack:

```bash
sam build
sam deploy
```

Sau khi deploy, CloudFormation output trả về CloudFront distribution ID và domain name.

```text
CloudFrontDistributionId: E9646ICQMCQUR
CloudFrontDistributionDomainName: d1cdyvxfzvnsxg.cloudfront.net
Frontend URL: https://d1cdyvxfzvnsxg.cloudfront.net
```

#### 5.6.12.5 CloudFront Cache Invalidation

Sau mỗi lần cập nhật frontend, tạo invalidation để CloudFront phục vụ phiên bản mới nhất.

```bash
aws cloudfront create-invalidation   --distribution-id E9646ICQMCQUR   --paths "/*"
```

#### 5.6.12.6 Kiến trúc triển khai cuối cùng

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

AI flow cuối cùng:

```text
User -> React Frontend -> API Gateway -> Lambda AI Assistant -> Groq -> JSON Response -> Frontend
Fallback: Lambda AI Assistant -> Rule-based Engine -> Frontend
```
