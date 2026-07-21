---
title: "Chuẩn bị môi trường"
date: 2026-07-17
weight: 2
chapter: false
pre: " <b> 5.2. </b> "
---

## 5.2. Chuẩn bị môi trường

#### Nội dung

- 5.2.2 Điều kiện tiên quyết

### 5.2.2 Điều kiện tiên quyết

#### 5.2.2.1 Tài khoản & Quyền AWS
- AWS Account đã kích hoạt (account chung của nhóm)
- IAM User với quyền đủ để tạo các service liên quan
- Region sử dụng: ap-southeast-1 (Singapore) — thống nhất toàn nhóm, không đổi

#### 5.2.2.2 Cài đặt công cụ
AWS CLI:

```bash
`# Kiểm tra đã cài chưa`
aws --version
```

`# Nếu chưa có, cài theo hướng dẫn:`
`# https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html`

`# Cấu hình sau khi cài`
aws configure --profile irms-shared
`# AWS Access Key ID: <nhập key>`
`# AWS Secret Access Key: <nhập secret>`
`# Default region name: ap-southeast-1`
`# Default output format: json`
AWS SAM CLI:

```bash
`# Kiểm tra đã cài chưa`
sam --version
```

`# Cài đặt: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html`

`# Verify`
sam --version
`# SAM CLI, version 1.x.x`
Node.js & npm (cho Frontend):

```bash
node --version   # >= 18.x
npm --version
Python 3.12 (cho Lambda):
```

bash
python3 --version   # >= 3.12

#### 5.2.2.3 Thiết lập Budget Alert (BẮT BUỘC làm trước)
Bước này phải làm trước khi deploy bất kỳ resource nào.
- Vào AWS Console → Billing → Budgets → Create budget
![Điều kiện tiên quyết 1](/Internship/images/5-Workshop/IRMS/section-02-001.png)
- Chọn Cost budget
- Budget name: irms-demo-budget
- Budgeted amount: $5
- Alert threshold: 80% (tức là cảnh báo khi vượt $4)
- Email nhận cảnh báo: điền email của trưởng nhóm  hoặc cả nhóm
- Chọn Create budget
![Điều kiện tiên quyết 2](/Internship/images/5-Workshop/IRMS/section-02-002.png)

#### 5.2.2.4 Cấu trúc thư mục project

```text
irms/
├── infrastructure/
│   ├── template-auth.yaml
│   ├── template-api.yaml
│   ├── template-database.yaml
│   ├── template-storage.yaml
│   ├── template-security.yaml
│   └── template-ai.yaml
├── functions/
│   ├── incident-crud/
│   │   └── handler.py
│   ├── upload-evidence/
│   │   └── handler.py
│   ├── generate-report/
│   │   └── handler.py
│   ├── alert-handler/
│   │   └── handler.py
│   └── ai-assistant/
│       └── handler.py
├── frontend/
│   └── (React SPA)
├── docs/
│   ├── architecture/
│   └── api-spec.md
└── README.md
```
