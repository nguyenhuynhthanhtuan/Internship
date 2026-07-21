---
title: "Phát triển ứng dụng"
date: 2026-07-17
weight: 4
chapter: false
pre: " <b> 5.4. </b> "
---

## 5.4. Phát triển ứng dụng

#### Nội dung

- 5.4.7 Lambda Functions

### 5.4.7 Lambda Functions

#### 5.4.7.1 Tổng quan Compute Layer

AWS Lambda là trung tâm xử lý nghiệp vụ của IRMS. Thay vì duy trì server chạy liên tục như EC2, Lambda chỉ khởi động khi có request hoặc event, xử lý xong thì tự dừng. Cách này phù hợp với hệ thống demo/internship vì chi phí phụ thuộc vào số lần thực thi thực tế.

**Luồng request qua Compute Layer:**

```text
Browser
  |
  v
API Gateway (Production)
  |
  +--> Lambda Incident CRUD
  |      +--> DynamoDB: irms-incidents, irms-timeline
  |
  +--> Lambda Upload Evidence
  |      +--> S3 Evidence Store: presigned URL
  |      +--> DynamoDB: irms-evidence-metadata
  |
  +--> Lambda Generate Report
  |      +--> DynamoDB: đọc dữ liệu tổng hợp
  |      +--> S3 Evidence Store: lưu file CSV
  |      +--> Trigger: EventBridge Scheduler
  |
  +--> Lambda Alert Handler
  |      +--> DynamoDB: tạo incident từ alert
  |      +--> SNS: gửi thông báo
  |      +--> Trigger: EventBridge
  |
  +--> Lambda AI Assistant
         +--> Secrets Manager: đọc Groq API key
         +--> Groq API: phân tích AI
         +--> Rule-based fallback khi provider không khả dụng
```

**Vai trò của từng Lambda:**

| Lambda | Trách nhiệm chính | Dịch vụ liên quan |
| --- | --- | --- |
| `incident-crud` | Tạo, đọc, cập nhật, xóa Incident và Timeline | API Gateway, DynamoDB |
| `upload-evidence` | Tạo presigned URL và ghi metadata bằng chứng | API Gateway, S3, DynamoDB |
| `generate-report` | Tổng hợp dữ liệu và xuất báo cáo CSV | EventBridge, DynamoDB, S3 |
| `alert-handler` | Nhận alert/security event và tạo Incident tự động | EventBridge, DynamoDB, SNS |
| `ai-assistant` | Phân tích incident, giải thích severity và hỏi đáp về incident | Secrets Manager, Groq API, rule-based fallback |

**Vì sao chọn Lambda thay vì EC2:**

| Tiêu chí | Lambda | EC2 |
| --- | --- | --- |
| Chi phí | Trả theo lượt gọi | Trả theo giờ chạy dù không có request |
| Scale | Tự động scale theo request/event | Cần cấu hình Auto Scaling Group |
| Vận hành | Không cần quản lý OS, patch, update | Phải tự quản lý hệ điều hành và runtime |
| Phù hợp với | Traffic không đều, demo internship, event-driven workload | Server chạy liên tục 24/7 |

#### 5.4.7.2 Cấu trúc thư mục backend/
Mở repo, kiểm tra cấu trúc thư mục backend/:

```text
backend/
├── incident-crud/
│   ├── handler.py
│   └── requirements.txt
│
├── upload-evidence/
│   ├── handler.py
│   └── requirements.txt
│
├── generate-report/
│   ├── handler.py
│   └── requirements.txt
│
├── alert-handler/
│   ├── handler.py
│   └── requirements.txt
│
└── ai-assistant/
├── handler.py
└── requirements.txt
```

Nhiệm vụ từng Lambda:

| Lambda | Nhiệm vụ | Trigger |
| --- | --- | --- |
| incident-crud | CRUD Incident, Timeline | API Gateway |
| upload-evidence | Tạo Presigned URL, ghi metadata | API Gateway |
| generate-report | Tổng hợp dữ liệu, xuất CSV lên S3 | EventBridge Scheduler |
| alert-handler | Tạo Incident tự động, gửi thông báo | EventBridge |
| ai-assistant | Phân tích sự cố, gợi ý severity | API Gateway |

#### 5.4.7.3 Incident CRUD Lambda

Nhiệm vụ: Xử lý toàn bộ vòng đời của một Incident — từ tạo mới, đọc danh sách, cập nhật trạng thái đến xóa.
Flow từng operation:
GET /incidents — Lấy danh sách:
```text
Request (có JWT token)
│
▼
Lambda đọc query string: ?status=Open&assignedTo=analyst@...
│
▼
Query DynamoDB bảng irms-incidents
- Nếu có filter status   → dùng GSI status-index
- Nếu có filter assigned → dùng GSI assignedTo-index
- Không có filter        → Scan toàn bảng
│
▼
Trả về danh sách Incident dạng JSON
POST /incidents — Tạo mới:
Request body: {title, severity, description}
│
▼
Lambda validate input (thiếu title → 400 Bad Request)
│
▼
Generate incidentId: "INC-" + timestamp
│
▼
PutItem vào irms-incidents
│
▼
PutItem vào irms-timeline (action: "Incident Created")
│
▼
Trả về incidentId vừa tạo
PUT /incidents/{id} — Cập nhật:
Request body: {status, assignedTo, ...}
│
▼
Lambda kiểm tra incidentId tồn tại
│
▼
UpdateItem irms-incidents (chỉ update field được gửi lên)
│
▼
PutItem irms-timeline (action: "Status Changed" / "Assigned")
│
▼
Trả về record đã cập nhật
DELETE /incidents/{id} — Xóa:
Request params: incidentId
│
▼
DeleteItem irms-incidents
│
▼
Trả về 204 No Content
```


#### 5.4.7.4 Upload Evidence Lambda

Nhiệm vụ: Tạo Presigned URL để browser upload file bằng chứng thẳng lên S3, sau đó ghi metadata vào DynamoDB. File không đi qua Lambda.
Cách Lambda tạo Presigned URL: Lambda dùng AWS SDK (boto3) kết hợp với IAM Role được gán cho Lambda để ký Presigned URL. Người dùng không cần có quyền S3 — họ chỉ dùng URL đã được Lambda ký sẵn để upload trong thời gian cho phép (300 giây).
Flow chi tiết:
Bước 1 — Lấy Presigned URL:
POST /evidence
```text
Body: {incidentId, fileName, fileType}
│
▼
Lambda generate evidenceId (UUID)
│
▼
Lambda dùng boto3 + IAM Role của Lambda để ký Presigned URL:
- Bucket: irms-evidence-031577240048-ap-southeast-1
- Key: {incidentId}/{evidenceId}/{fileName}
- Expiry: 300 giây
- Method: PUT
│
▼
Trả về {evidenceId, presignedUrl}
Bước 2 — Browser upload thẳng lên S3:
Browser nhận presignedUrl
│
▼
Browser PUT request THẲNG lên S3 (không qua Lambda, không qua API Gateway)
- URL: presignedUrl
- Body: file binary
- Header: Content-Type: image/png (hoặc đúng type)
│
▼
S3 lưu file → trả về 200 OK cho browser
Bước 3 — Xác nhận và ghi metadata:
POST /evidence/{id}
Body: {evidenceId, incidentId}
│
▼
Lambda PutItem vào irms-evidence-metadata:
{
evidenceId, incidentId, fileName,
fileType, s3Key, uploadedBy,
uploadedAt, fileSize
}
│
▼
Trả về 200 OK
```


#### 5.4.7.5 Generate Report Lambda

Nhiệm vụ: Đọc dữ liệu tổng hợp từ DynamoDB, tạo file CSV và lưu lên S3. Với MVP hiện tại, Lambda này chủ yếu được gọi tự động bởi EventBridge Scheduler.
Lưu ý MVP: Xuất CSV là scope hiện tại. Xuất PDF là Future Enhancement.
Trigger:
```text
EventBridge Scheduler (cron hàng tháng)
│
▼
Lambda Generate Report
Nếu user muốn xem hoặc tải report, dùng GET /reports để đọc danh sách file report từ S3 hoặc metadata — không gọi generate trực tiếp.
Flow:
EventBridge Scheduler trigger
│
▼
Lambda xác định khoảng thời gian báo cáo
(mặc định: tháng hiện tại)
│
▼
Query / Scan DynamoDB irms-incidents:
- Tổng số incident theo severity
- Tổng số incident theo status
- Thời gian xử lý trung bình
│
▼
Tạo file CSV bằng thư viện csv của Python
│
▼
PutObject lên S3:
Key: reports/{year}/{month}/irms-report-{timestamp}.csv
│
▼
```

Ghi xong, Lambda kết thúc
(không cần trả về HTTP response vì trigger là EventBridge)


#### 5.4.7.6 Alert Handler Lambda

Nhiệm vụ: Nhận event từ EventBridge, tự động tạo Incident trong DynamoDB và gửi thông báo qua SNS. Chi tiết cấu hình GuardDuty và EventBridge rule sẽ làm ở 9.
Flow:
```text
EventBridge Event (GuardDuty Finding)
│
▼
Lambda parse event:
{
"detail-type": "GuardDuty Finding",
"detail": {
"severity": 8.0,
"type": "UnauthorizedAccess:EC2/SSHBruteForce",
"description": "..."
}
}
│
▼
Map severity GuardDuty → IRMS:
7.0 - 10.0  →  Critical
4.0 - 6.9   →  High
1.0 - 3.9   →  Medium
│
├──► PutItem irms-incidents:
│    {
│      incidentId: "INC-AUTO-{timestamp}",
│      title: "Auto-detected: {finding.type}",
│      severity: "Critical/High/Medium",
│      status: "Open",
│      source: "GuardDuty"
│    }
│
└──► SNS Publish:
Topic: irms-alert-topic
Message: "New {severity} incident: {title}"
Subject: "[IRMS ALERT] {severity} - {title}"
```


#### 5.4.7.7 AI Assistant Lambda

Nhiệm vụ: nhận ngữ cảnh incident từ frontend đã xác thực, xử lý qua lớp provider abstraction, gọi Groq với model `llama-3.1-8b-instant`, sau đó trả về kết quả JSON có cấu trúc cho giao diện người dùng.

Frontend không lưu và không gửi provider API key. Groq API key chỉ được lưu trong AWS Secrets Manager và Lambda đọc secret này khi chạy.

**Kiến trúc provider:**

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

**Kiến trúc fallback:**

```text
Nếu Groq không khả dụng
  |
  v
Rule-based analysis
  |
  v
Người dùng nhận thông báo fallback
```

Amazon Bedrock vẫn được giữ như một lựa chọn mở rộng trong tương lai vì provider abstraction giúp frontend không cần thay đổi contract API.

**Các AI endpoint đã triển khai:**

| Endpoint | Chức năng |
| --- | --- |
| `POST /ai/analyze` | Tóm tắt incident, đánh giá severity, đề xuất hành động và bước tiếp theo |
| `POST /ai/explain` | Giải thích vì sao chọn severity, reasoning, confidence, risk factors và điều kiện tăng/giảm severity |
| `POST /ai/chat` | Hỏi đáp về incident hiện tại, containment, investigation, evidence collection, reporting và recovery |

Tất cả AI endpoint đều yêu cầu Cognito authentication thông qua API Gateway.

Ví dụ response cho `POST /ai/analyze`:

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

**Vì sao không hardcode API key:**

- Scanner tự động có thể phát hiện key bị lộ rất nhanh.
- Secrets Manager hỗ trợ xoay vòng key mà không cần deploy lại Lambda.
- Frontend React không chứa API key.
- Cách làm này phù hợp với AWS Well-Architected Framework, Security Pillar.

#### 5.4.7.8 Environment Variables

Mở `infrastructure/template.yaml`, kiểm tra phần `Globals` hoặc từng Lambda function và xác nhận các biến môi trường sau:

| Environment Variable | Giá trị | Lambda sử dụng |
| --- | --- | --- |
| `ENVIRONMENT` | `dev` | Xác định môi trường triển khai |
| `INCIDENTS_TABLE` | `irms-incidents` | `incident-crud`, `alert-handler` |
| `TIMELINE_TABLE` | `irms-timeline` | `incident-crud` |
| `EVIDENCE_METADATA_TABLE` | `irms-evidence-metadata` | `upload-evidence` |
| `USERS_TABLE` | `irms-users` | `incident-crud` |
| `EVIDENCE_BUCKET` | `irms-evidence-031577240048-ap-southeast-1` | `upload-evidence`, `generate-report` |
| `AIProvider` | `groq` | `ai-assistant` |
| `GroqSecretName` | Tên secret trong Secrets Manager chứa Groq API key | `ai-assistant` |
| `GroqModel` | `llama-3.1-8b-instant` | `ai-assistant` |
| `GroqTimeoutSeconds` | Timeout khi gọi provider | `ai-assistant` |

Lý do dùng environment variables thay vì hardcode:

- Chuyển provider hoặc môi trường chỉ cần đổi cấu hình.
- Frontend không có API key.
- Tên tài nguyên và secret có thể thay đổi mà không sửa code.
- Cách làm này phù hợp với nguyên tắc 12-Factor App.

Ví dụ trong Python:

```python
import os

TABLE_NAME = os.environ.get("INCIDENTS_TABLE")
BUCKET_NAME = os.environ.get("EVIDENCE_BUCKET")
AI_PROVIDER = os.environ.get("AIProvider", "groq")
GROQ_SECRET_NAME = os.environ.get("GroqSecretName")
GROQ_MODEL = os.environ.get("GroqModel", "llama-3.1-8b-instant")
GROQ_TIMEOUT_SECONDS = int(os.environ.get("GroqTimeoutSeconds", "20"))
```

#### 5.4.7.9 IAM Permissions — Nguyên tắc Least Privilege
Mỗi Lambda chỉ được cấp đúng quyền cần thiết. Nếu một Lambda bị tấn công, attacker chỉ làm được đúng những gì Lambda đó được phép — không hơn.
incident-crud
- ✅ dynamodb:GetItem      → irms-incidents
- ✅ dynamodb:PutItem      → irms-incidents, irms-timeline
- ✅ dynamodb:UpdateItem   → irms-incidents
- ✅ dynamodb:DeleteItem   → irms-incidents
- ✅ dynamodb:Query        → irms-incidents (GSI)
❌ dynamodb:*            → không cần toàn bộ quyền
❌ s3:*                  → không cần S3

upload-evidence
- ✅ s3:PutObject          → irms-evidence-031577240048-ap-southeast-1
- ✅ s3:GetObject          → irms-evidence-031577240048-ap-southeast-1
- ✅ dynamodb:PutItem      → irms-evidence-metadata
❌ s3:DeleteObject       → không cần xóa
❌ dynamodb:DeleteItem   → không cần xóa

generate-report
- ✅ dynamodb:Query        → irms-incidents
- ✅ dynamodb:Scan         → irms-incidents (tổng hợp toàn bảng cho demo)
- ✅ s3:PutObject          → irms-evidence-031577240048-ap-southeast-1/reports/
❌ dynamodb:DeleteItem   → không cần xóa
❌ dynamodb:UpdateItem   → không cần sửa

alert-handler
- ✅ dynamodb:PutItem      → irms-incidents, irms-timeline
- ✅ sns:Publish           → topic irms-alerts (chỉ topic này)
❌ sns:CreateTopic       → không cần tạo topic mới
❌ dynamodb:DeleteItem   → không cần xóa

ai-assistant
- ✅ secretsmanager:GetSecretValue -> chỉ secret Groq đã cấu hình
❌ secretsmanager:*              → không cần tạo/xóa secret
❌ dynamodb:*                    → không cần đọc ghi DB
❌ s3:*                          → không cần S3

#### 5.4.7.10 Logging & Error Handling
Tất cả Lambda tự động ghi log vào Amazon CloudWatch Logs — không cần cấu hình thêm. Mỗi Lambda có 1 Log Group riêng, tên theo dạng /aws/lambda/<function-name>.
Dùng logger thay vì print:
import logging

logger = logging.getLogger()
logger.setLevel(logging.INFO)

`# Ghi log thông thường`
logger.info("Creating incident", extra={"incidentId": incident_id})

`# Ghi log lỗi kèm stack trace`
logger.error("Failed to create incident", exc_info=True)
Nên log những gì:
- ✅ requestId      → để trace lại request khi có lỗi
- ✅ user email     → lấy từ JWT claims, biết ai đang thao tác
- ✅ incidentId     → để trace lại record liên quan
- ✅ action         → "Creating incident", "Updating status"...
- ✅ error message  → khi có exception
Tuyệt đối không log:
❌ JWT token đầy đủ      → attacker lấy được token từ log
❌ Groq API key         → lộ secret
❌ Presigned URL đầy đủ  → ai có URL là upload được file
❌ Password, thông tin cá nhân nhạy cảm

#### 5.4.7.11 Validate Source Code
này không chạy sam build hay sam deploy — chỉ kiểm tra cấu trúc repo.
**Bước 1: Mở Command Prompt (CMD) trên Windows.**
Nhấn phím Windows → gõ cmd → Enter.
**Bước 2: Di chuyển vào thư mục repo:**
cd /d "D:\Thực Tập\irms-serverless"
Thay đường dẫn bằng đường dẫn thật trên máy bạn.
**Bước 3: Kiểm tra thư mục backend có đủ 5 Lambda:**
dir backend
Kết quả mong đợi:
Directory of D:\Thực Tập\irms-serverless\backend

incident-crud
upload-evidence
generate-report
alert-handler
ai-assistant
**Bước 4: Kiểm tra từng Lambda có đủ file:**
dir backend\incident-crud
dir backend\upload-evidence
dir backend\generate-report
dir backend\alert-handler
dir backend\ai-assistant
Mỗi thư mục phải có ít nhất:
handler.py
requirements.txt
**Bước 5: Kiểm tra template.yaml tham chiếu đúng Handler:**
findstr /C:"Handler:" infrastructure\template.yaml
Kết quả mong đợi (5 dòng, mỗi Lambda 1 dòng):
Handler: handler.handler
Handler: handler.handler
Handler: handler.handler
Handler: handler.handler
Handler: handler.handler
**Bước 6: Kiểm tra CodeUri trỏ đúng thư mục:**
findstr /C:"CodeUri:" infrastructure\template.yaml
Kết quả mong đợi:
CodeUri: ../backend/incident-crud/
CodeUri: ../backend/upload-evidence/
CodeUri: ../backend/generate-report/
CodeUri: ../backend/ai-assistant/
CodeUri: ../backend/alert-handler/
**Bước 7: Chạy sam validate lần cuối:**
sam validate --template-file infrastructure\template.yaml --region ap-southeast-1
Kết quả mong đợi:
Template provided at 'infrastructure\template.yaml' was successfully validated.

#### 5.4.7.12 Checklist 7
- ✅ Hiểu vai trò AWS Lambda trong kiến trúc IRMS
- ✅ Hiểu vì sao chọn Lambda thay vì EC2
- ✅ Hiểu nhiệm vụ 5 Lambda:
- ✅ incident-crud     → CRUD Incident + Timeline
- ✅ upload-evidence   → Presigned URL + ghi metadata (file không qua Lambda)
- ✅ generate-report   → EventBridge trigger → tổng hợp DynamoDB → xuất CSV → S3
- ✅ alert-handler     → nhận EventBridge event → tạo Incident → SNS
- ✅ ai-assistant      → đọc Secrets Manager → gọi Groq API
- ✅ Hiểu 7 Environment Variables và lý do không hardcode
- ✅ Hiểu nguyên tắc Least Privilege, từng Lambda chỉ có quyền cần thiết
- ✅ Không có s3:GeneratePresignedUrl trong IAM (không phải IAM action hợp lệ)
- ✅ Đã hiểu CloudWatch Logs tự động ghi log cho Lambda
- ✅ Không log JWT token, API Key, Presigned URL, dữ liệu nhạy cảm
- ✅ Đã kiểm tra cấu trúc thư mục backend/ có đủ 5 Lambda
- ✅ Đã kiểm tra handler.py và requirements.txt tồn tại
- ✅ Đã kiểm tra CodeUri trỏ đúng backend/ trong template.yaml
- ✅ sam validate chạy thành công
- ✅ Chưa chạy sam build
- ✅ Chưa chạy sam deploy
- ✅ Chưa tạo bất kỳ resource production nào
