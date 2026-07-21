---
title: "Triển khai và kiểm thử"
date: 2026-07-17
weight: 5
chapter: false
pre: " <b> 5.5. </b> "
---

## 5.5. Triển khai và kiểm thử

#### Nội dung

- 5.5.8 API Gateway Production
- 5.5.9 EventBridge & GuardDuty
- 5.5.10 Build & Deploy
- 5.5.11 Testing & Validation

### 5.5.8 API Gateway Production (SAM)

#### 5.5.8.1 Tổng quan API Layer

Amazon API Gateway là lớp trung gian giữa frontend React SPA và các Lambda backend của IRMS.
Thay vì browser gọi trực tiếp Lambda, mọi request sẽ đi qua API Gateway:
```text
React SPA
│
▼
API Gateway
│
├── GET    /incidents          → incident-crud Lambda
├── POST   /incidents          → incident-crud Lambda
├── PUT    /incidents/{id}     → incident-crud Lambda
├── DELETE /incidents/{id}     → incident-crud Lambda
│
├── POST   /evidence           → upload-evidence Lambda
├── POST   /evidence/{id}→ upload-evidence Lambda
│
├── GET    /reports            → generate-report Lambda
│
|-- POST   /ai/analyze         -> ai-assistant Lambda
|-- POST   /ai/explain         -> ai-assistant Lambda
└── POST   /ai/chat            -> ai-assistant Lambda
```

API Gateway giúp IRMS:


| Vai trò | Ý nghĩa |
| --- | --- |
| Routing | Điều hướng từng endpoint đến đúng Lambda |
| Authentication | Kiểm tra JWT token từ Cognito |
| CORS | Cho phép React SPA gọi API |
| Security | Không expose Lambda trực tiếp |
| Production URL | Cung cấp HTTPS endpoint cho frontend |

#### 5.5.8.2 Vì sao cần API Gateway?
Nếu không có API Gateway, frontend sẽ không có endpoint chuẩn để gọi backend.
API Gateway đóng vai trò như “cửa chính” của hệ thống:
User -> Browser -> API Gateway -> Lambda -> DynamoDB / S3 / Groq API
Ưu điểm:
- Có HTTPS endpoint tự động.
- Tích hợp được Cognito Authorizer.
- Dễ quản lý route.
- Dễ log request.
- Dễ mở rộng thêm endpoint mới.
- Phù hợp serverless architecture.

#### 5.5.8.3 API Gateway trong SAM Template
Trong infrastructure/template.yaml, API Gateway thường được khai báo bằng resource dạng:
IrmsApi:
Type: AWS::Serverless::Api
Properties:
Name: irms-api
StageName: prod
Cors:
AllowMethods: "'GET,POST,PUT,DELETE,OPTIONS'"
AllowHeaders: "'Content-Type,Authorization'"
AllowOrigin: "'*'"
Trong MVP, có thể dùng:
AllowOrigin: "'*'"
Để dễ test frontend local.
Khi lên production thật, nên đổi thành domain frontend cụ thể, ví dụ:
AllowOrigin: "'https://your-domain.com'"

#### 5.5.8.4 API Routes cho Incident CRUD
Các route Incident dùng chung Lambda incident-crud.
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
Ý nghĩa:

| Method | Path | Chức năng |
| --- | --- | --- |
| GET | /incidents | Lấy danh sách incident |
| POST | /incidents | Tạo incident mới |
| PUT | /incidents/{id} | Cập nhật incident |
| DELETE | /incidents/{id} | Xóa incident |

#### 5.5.8.5 API Routes cho Evidence
Evidence upload gồm 2 bước:
- Lấy presigned URL.
- Xác nhận metadata sau khi upload.
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
Flow:
```text
Browser
│
├── POST /evidence
│       └── Lambda trả presigned URL
│
├── PUT file thẳng lên S3 bằng presigned URL
│
└── POST /evidence/{id}
└── Lambda ghi metadata vào DynamoDB
```

File không đi qua API Gateway, giúp giảm tải và giảm chi phí.


#### 5.5.8.6 API Routes cho AI Assistant

AI Assistant sử dụng ba endpoint được bảo vệ bằng Cognito:

| Endpoint | Mục đích |
| --- | --- |
| `POST /ai/analyze` | Phân tích incident, tóm tắt, đánh giá severity, đề xuất hành động và bước tiếp theo |
| `POST /ai/explain` | Giải thích vì sao chọn severity, confidence, risk factors và điều kiện tăng/giảm severity |
| `POST /ai/chat` | Hỏi đáp theo incident hiện tại về containment, investigation, evidence collection, reporting và recovery |

Ví dụ khai báo SAM:

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

Ví dụ request cho `POST /ai/analyze`:

```json
{
  "incidentId": "INC-001",
  "description": "Multiple failed SSH login attempts from unknown IP"
}
```

Ví dụ response:

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

Để bảo vệ API, API Gateway cần kiểm tra JWT token từ Amazon Cognito.
Luồng xác thực:
```text
User login bằng Cognito
│
▼
Frontend nhận Access Token / ID Token
│
▼
Frontend gọi API kèm header:
Authorization: Bearer <JWT_TOKEN>
│
▼
API Gateway Cognito Authorizer kiểm tra token
│
├── Token hợp lệ   → Cho gọi Lambda
└── Token không hợp lệ → Trả 401 Unauthorized
```

Ví dụ cấu hình trong SAM:
Auth:
DefaultAuthorizer: CognitoAuthorizer
Authorizers:
CognitoAuthorizer:
UserPoolArn: !GetAtt IrmsUserPool.Arn
Khi đó các endpoint mặc định sẽ yêu cầu Cognito token.


#### 5.5.8.8 CORS Configuration
CORS cần thiết vì frontend React SPA và API Gateway có domain khác nhau.
Ví dụ:
Frontend local:
http://localhost:3000

API Gateway:
https://abc123.execute-api.ap-southeast-1.amazonaws.com/prod
Nếu không bật CORS, browser sẽ chặn request.
Cấu hình CORS trong SAM:
Cors:
AllowMethods: "'GET,POST,PUT,DELETE,OPTIONS'"
AllowHeaders: "'Content-Type,Authorization'"
AllowOrigin: "'*'"
Trong production thật, không nên dùng *, mà nên giới hạn theo domain frontend.

#### 5.5.8.9 Validate API Gateway trong template.yaml
này chưa cần deploy. Trước tiên chỉ kiểm tra template đã khai báo API Gateway và route đúng chưa.
**Bước 1: Mở CMD và vào repo:**
cd /d "D:\Thực Tập\irms-serverless"
**Bước 2: Kiểm tra có khai báo AWS::Serverless::Api:**
findstr /C:"AWS::Serverless::Api" infrastructure\template.yaml
Kết quả mong đợi:
Type: AWS::Serverless::Api
![API Gateway Production 1](/Internship/images/5-Workshop/IRMS/section-08-001.png)
**Bước 3: Kiểm tra route /incidents:**
findstr /C:"/incidents" infrastructure\template.yaml
Kết quả mong đợi có các route:
Path: /incidents
Path: /incidents/{id}
![API Gateway Production 2](/Internship/images/5-Workshop/IRMS/section-08-002.png)
**Bước 4: Kiểm tra route /evidence:**
findstr /C:"/evidence" infrastructure\template.yaml
Kết quả mong đợi:
Path: /evidence
Path: /evidence/{id}
![API Gateway Production 3](/Internship/images/5-Workshop/IRMS/section-08-003.png)
**Bước 5: Kiểm tra route AI:**
findstr /C:"/ai/analyze" infrastructure\template.yaml
findstr /C:"/ai/explain" infrastructure\template.yaml
findstr /C:"/ai/chat" infrastructure\template.yaml
Kết quả mong đợi:
Path: /ai/analyze
Path: /ai/explain
Path: /ai/chat
![API Gateway Production 4](/Internship/images/5-Workshop/IRMS/section-08-004.png)
**Bước 6: Kiểm tra CORS:**
findstr /C:"Cors:" infrastructure\template.yaml
findstr /C:"AllowOrigin" infrastructure\template.yaml
findstr /C:"AllowHeaders" infrastructure\template.yaml
findstr /C:"AllowMethods" infrastructure\template.yaml
![API Gateway Production 5](/Internship/images/5-Workshop/IRMS/section-08-005.png)
**Bước 7: Kiểm tra Cognito Authorizer:**
findstr /C:"Authorizer" infrastructure\template.yaml
findstr /C:"UserPoolArn" infrastructure\template.yaml
Nếu template đã có Cognito Authorizer, kết quả sẽ có các dòng liên quan đến:
DefaultAuthorizer
CognitoAuthorizer
UserPoolArn
![API Gateway Production 6](/Internship/images/5-Workshop/IRMS/section-08-006.png)
**Bước 8: Validate SAM template:**
sam validate --template-file infrastructure\template.yaml --region ap-southeast-1
Kết quả mong đợi:
D:\Thực Tập\irms-serverless\infrastructure\template.yaml is a valid SAM Template.
![API Gateway Production 7](/Internship/images/5-Workshop/IRMS/section-08-007.png)

#### 5.5.8.10 Checklist 8
- ✅ Hiểu vai trò API Gateway trong kiến trúc IRMS
- ✅ Hiểu vì sao frontend không gọi trực tiếp Lambda
- ✅ Hiểu API Gateway route request đến từng Lambda
- ✅ Đã kiểm tra AWS::Serverless::Api trong template.yaml
- ✅ Đã kiểm tra route /incidents
- ✅ Đã kiểm tra route /incidents/{id}
- ✅ Đã kiểm tra route /evidence
- ✅ Đã kiểm tra route /evidence/{id}
- ✅ Đã kiểm tra route /ai/analyze
- ✅ Đã kiểm tra route /ai/explain
- ✅ Đã kiểm tra route /ai/chat
- ✅ Đã hiểu CORS và lý do frontend cần CORS
- ✅ Đã hiểu Cognito Authorizer bảo vệ API bằng JWT token
- ✅ Đã kiểm tra cấu hình Authorizer trong template.yaml
- ✅ sam validate chạy thành công
- ✅ Chưa chạy sam build
- ✅ Chưa chạy sam deploy
- ✅ Chưa tạo API Gateway production thật

### 5.5.9 EventBridge, GuardDuty Simulation và Alert Automation

#### 5.5.9.1 Tổng quan Event-driven Alert Automation

này kiểm tra phần tự động tạo incident khi có security event.
Luồng xử lý:
```text
GuardDuty Finding / Simulated Event
│
▼
Amazon EventBridge
│
▼
Alert Handler Lambda
│
├── Tạo incident mới trong DynamoDB
└── Gửi thông báo qua SNS
```

Trong MVP, có thể dùng simulated event thay cho GuardDuty thật để giảm chi phí và dễ demo.


#### 5.5.9.2 Vai trò của EventBridge
EventBridge giúp IRMS nhận sự kiện bảo mật theo kiểu event-driven.
Thay vì analyst phải tạo incident thủ công, hệ thống có thể tự động tạo incident khi có event phù hợp.
Ví dụ event:
{
"source": "aws.guardduty",
"detail-type": "GuardDuty Finding",
"detail": {
"severity": 8.0,
"type": "UnauthorizedAccess:EC2/SSHBruteForce",
"description": "SSH brute force attack detected"
}
}

#### 5.5.9.3 Alert Handler Lambda
Lambda alert-handler nhận event từ EventBridge, sau đó:
1. Parse event
2. Map severity
3. Tạo incidentId tự động
4. Ghi incident vào bảng irms-incidents
5. Ghi timeline vào bảng irms-timeline
6. Gửi SNS notification
Mapping severity:
7.0 - 10.0  → Critical
4.0 - 6.9   → High
1.0 - 3.9   → Medium

#### 5.5.9.4 SNS Notification
SNS dùng để gửi thông báo khi có incident tự động.
Ví dụ:
Topic: irms-alert-topic
Subject: [IRMS ALERT] Critical - Auto-detected GuardDuty Finding
Message: New Critical incident created from GuardDuty finding.
Trong demo internship, có thể subscribe email cá nhân vào SNS topic để nhận cảnh báo.

#### 5.5.9.5 Validate EventBridge trong template.yaml
**Bước 1: Vào repo:**
cd /d "D:\Thực Tập\irms-serverless"
**Bước 2: Kiểm tra có EventBridge rule không:**
findstr /C:"AWS::Events::Rule" infrastructure\template.yaml
![EventBridge & GuardDuty 1](/Internship/images/5-Workshop/IRMS/section-09-001.png)
**Bước 3: Kiểm tra có EventBridge pattern không:**
findstr /C:"EventPattern" infrastructure\template.yaml
findstr /C:"aws.guardduty" infrastructure\template.yaml
findstr /C:"GuardDuty Finding" infrastructure\template.yaml
![EventBridge & GuardDuty 2](/Internship/images/5-Workshop/IRMS/section-09-002.png)
**Bước 4: Kiểm tra alert-handler có được trigger bởi EventBridge không:**
findstr /C:"alert-handler" infrastructure\template.yaml
findstr /C:"AlertHandler" infrastructure\template.yaml
![EventBridge & GuardDuty 3](/Internship/images/5-Workshop/IRMS/section-09-003.png)
**Bước 5: Kiểm tra SNS topic:**
findstr /C:"TopicName" infrastructure\template.yaml
![EventBridge & GuardDuty 4](/Internship/images/5-Workshop/IRMS/section-09-004.png)
findstr /C:"SNS" infrastructure\template.yaml
![EventBridge & GuardDuty 5](/Internship/images/5-Workshop/IRMS/section-09-005.png)
**Bước 6: Kiểm tra quyền SNS publish:**
findstr /C:"SNS" infrastructure\template.yaml
![EventBridge & GuardDuty 6](/Internship/images/5-Workshop/IRMS/section-09-006.png)
**Bước 7: Kiểm tra quyền Lambda invoke từ EventBridge:**
findstr /C:"AWS::Lambda::Permission" infrastructure\template.yaml
findstr /C:"events.amazonaws.com" infrastructure\template.yaml
![EventBridge & GuardDuty 7](/Internship/images/5-Workshop/IRMS/section-09-007.png)
**Bước 8: Validate SAM template:**
sam validate --template-file infrastructure\template.yaml --region ap-southeast-1
Kết quả mong đợi:
D:\Thực Tập\irms-serverless\infrastructure\template.yaml is a valid SAM Template.

#### 5.5.9.6 Checklist 9
- ✅ Hiểu EventBridge dùng để nhận security event
- ✅ Hiểu Alert Handler Lambda tự động tạo incident
- ✅ Hiểu severity mapping từ GuardDuty sang IRMS
- ✅ Hiểu SNS dùng để gửi cảnh báo
- ✅ Đã kiểm tra AWS::Events::Rule
- ✅ Đã kiểm tra EventPattern
- ✅ Đã kiểm tra GuardDuty Finding pattern
- ✅ Đã kiểm tra alert-handler trong template.yaml
- ✅ Đã kiểm tra SNS Topic
- ✅ Đã kiểm tra SNSPublishMessagePolicy
- ✅ Đã kiểm tra Lambda permission cho EventBridge
- ✅ sam validate chạy thành công
- ✅ Chưa chạy sam build
- ✅ Chưa chạy sam deploy

### 5.5.10 Build & Deploy với AWS SAM

#### 5.5.10.1 Mục tiêu

Sau section này hệ thống sẽ được deploy lên AWS.
Các resource sẽ được tạo gồm:
```text
CloudFormation Stack
│
├── API Gateway
├── Lambda Functions
├── DynamoDB Tables
├── SNS Topic
├── EventBridge Rules
├── IAM Roles
├── CloudWatch Log Groups
└── Permissions
```

Frontend React vẫn chưa deploy ở section này.


#### 5.5.10.2 Kiểm tra AWS CLI
aws --version
Ví dụ:
aws-cli/2.xx.x

#### 5.5.10.3 Kiểm tra SAM CLI
sam --version
Ví dụ
SAM CLI, version 1.xx.x

#### 5.5.10.4 Kiểm tra Profile
aws configure list-profiles
Phải thấy
irms-shared
Sau đó
aws sts get-caller-identity --profile irms-shared
Nếu đúng sẽ trả về
Account
UserId
Arn

#### 5.5.10.5 Build Project
Trong thư mục project
cd /d "D:\Thực Tập\irms-serverless"
Build
sam build ^
--template-file infrastructure\template.yaml
SAM sẽ
- copy source Lambda
- cài requirements.txt
- sinh thư mục
.aws-sam
![Build & Deploy 1](/Internship/images/5-Workshop/IRMS/section-10-001.png)

#### 5.5.10.6 Kiểm tra Build
dir .aws-sam
phải thấy
build
cache
Sau đó
dir .aws-sam\build
phải thấy
IncidentCrudFunction
UploadEvidenceFunction
GenerateReportFunction
AlertHandlerFunction
AiAssistantFunction
template.yaml
![Build & Deploy 2](/Internship/images/5-Workshop/IRMS/section-10-002.png)

#### 5.5.10.7 Deploy Guided
Tiếp theo là deploy guided, nhưng trước khi chạy cần chuẩn bị Cognito parameter.
Chạy lệnh này để lấy User Pool ID:
aws cognito-idp list-user-pools --max-results 10 --region ap-southeast-1 --profile irms-shared
Và lấy App Client ID:
aws cognito-idp list-user-pool-clients --user-pool-id YOUR_USER_POOL_ID --region ap-southeast-1 --profile irms-shared
Thay YOUR_USER_POOL_ID bằng User Pool ID bạn thấy ở lệnh đầu.
Sau khi chạy lệnh lấy App Client ID:
aws cognito-idp list-user-pool-clients ^
--user-pool-id ap-southeast-1_NjzUFNCpi ^
--region ap-southeast-1 ^
--profile irms-shared

Lần đầu deploy
sam deploy --guided ^
--template-file .aws-sam\build\template.yaml ^
--profile irms-shared
SAM sẽ hỏi
Stack Name
Ví dụ
irms-serverless
Region
ap-southeast-1
Confirm changes before deploy
N
Allow SAM IAM
Y
Save configuration
Y
Sau lần đầu sẽ sinh
samconfig.toml
![Build & Deploy 3](/Internship/images/5-Workshop/IRMS/section-10-003.png)

#### 5.5.10.8 Deploy lần sau
Chỉ cần
sam build
rồi
sam deploy
không cần guided nữa.

#### 5.5.10.9 Validate Deployment
Kiểm tra stack
aws cloudformation list-stacks ^
--profile irms-shared
![Build & Deploy 4](/Internship/images/5-Workshop/IRMS/section-10-004.png)
Kiểm tra Lambda
aws lambda list-functions ^
--profile irms-shared
![Build & Deploy 5](/Internship/images/5-Workshop/IRMS/section-10-005.png)
![Build & Deploy 6](/Internship/images/5-Workshop/IRMS/section-10-006.png)
![Build & Deploy 7](/Internship/images/5-Workshop/IRMS/section-10-007.png)
Kiểm tra API
aws apigateway get-rest-apis ^
--profile irms-shared

#### 5.5.10.10 Checklist
- ✅ AWS CLI hoạt động
✅ SAM CLI hoạt động
✅ Profile irms-shared hoạt động
✅ sam build thành công
✅ .aws-sam được tạo
✅ Lambda build thành công
✅ sam deploy thành công
✅ CloudFormation Stack được tạo
✅ API Gateway được tạo
✅ Lambda được tạo
✅ DynamoDB được tạo
✅ EventBridge được tạo
✅ SNS Topic được tạo

### 5.5.11 Functional Testing & Validation
Mục tiêu
Chứng minh hệ thống IRMS không chỉ deploy thành công, mà các chức năng chính thật sự chạy được:
API Gateway → Lambda → DynamoDB / S3 / EventBridge / SNS / Secrets Manager
#### 5.5.11.1 Verify API Gateway Deployment
Mục tiêu
Xác nhận API Gateway đã deploy thành công, có Stage dev, có Invoke URL và các endpoint đúng.
Thực hiện
**Bước 1: Mở AWS Console**
Vào:
API Gateway → irms-api → Stages → dev
![Testing & Validation 1](/Internship/images/5-Workshop/IRMS/section-11-001.png)
Lưu ý: URL này chỉ là base URL, chưa phải endpoint chức năng.
**Bước 2: Liệt kê API Resources**
Chạy:
aws apigateway get-resources ^
--rest-api-id 07frck8bih ^
--region ap-southeast-1 ^
--profile irms-shared
![Testing & Validation 2](/Internship/images/5-Workshop/IRMS/section-11-002.png)
![Testing & Validation 3](/Internship/images/5-Workshop/IRMS/section-11-003.png)
![Testing & Validation 4](/Internship/images/5-Workshop/IRMS/section-11-004.png)
![Testing & Validation 5](/Internship/images/5-Workshop/IRMS/section-11-005.png)

#### 5.5.11.2 Test API Health Check
Mục tiêu
Kiểm tra luồng:
Client → API Gateway → IncidentCrud Lambda → Response
Thực hiện
Chạy:
curl.exe https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/ping
![Testing & Validation 6](/Internship/images/5-Workshop/IRMS/section-11-006.png)
Hoặc mở trình duyệt:
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/ping
![Testing & Validation 7](/Internship/images/5-Workshop/IRMS/section-11-007.png)

#### 5.5.11.3 Test Incident CRUD
🎯 Mục tiêu
Kiểm tra toàn bộ quy trình tạo và quản lý Incident trong hệ thống IRMS.
Sau bài test này, chúng ta sẽ xác nhận:
```text
Client
│
▼
API Gateway
│
▼
IncidentCrud Lambda
│
▼
DynamoDB (irms-incidents)
│
▼
```

CloudWatch Logs
Nếu thành công nghĩa là hệ thống backend đã xử lý được nghiệp vụ cốt lõi của IRMS.


Kiến trúc
```text
POST /incidents
        │
        ▼
API Gateway
        │
        ▼
IncidentCrud Lambda
        │
        ▼
DynamoDB
        │
        ▼
HTTP Response
```


📝 Các bước thực hiện
Bước 1 - Xác định API cần test
Trước tiên kiểm tra endpoint đã deploy.
Bạn đã có:
POST /incidents
GET  /incidents
GET  /incidents/{id}
PUT  /incidents/{id}
DELETE /incidents/{id}
Bước 2 - Kiểm tra Lambda đang mong đợi body như thế nào
Trước khi gọi API, chúng ta cần xem handler.py của incident-crud.
Hãy chạy:
type backend\incident-crud\handler.py
![Testing & Validation 8](/Internship/images/5-Workshop/IRMS/section-11-008.png)
Bước 3 - Gửi request tạo Incident
Mở CMD và chạy:
curl.exe -X POST ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents ^
-H "Content-Type: application/json" ^
-d "{\"title\":\"Unauthorized SSH Login\",\"description\":\"Multiple failed SSH login attempts detected\",\"severity\":\"High\",\"source\":\"manual\",\"assignedTo\":\"Security Analyst\"}"
![Testing & Validation 9](/Internship/images/5-Workshop/IRMS/section-11-009.png)
Nghĩa là endpoint /incidents đang được Cognito Authorizer bảo vệ. /ping public, còn CRUD thì cần JWT token.
Bạn cần lấy Access Token bằng Cognito. Chạy lệnh này, thay YOUR_USERNAME và YOUR_PASSWORD bằng user bạn đã tạo trước đó:
aws cognito-idp initiate-auth ^
--region ap-southeast-1 ^
--profile irms-shared ^
--client-id 7ulhp62hdg16m0s2dh6sr9mcvk ^
--auth-flow USER_PASSWORD_AUTH ^
--auth-parameters USERNAME=YOUR_USERNAME,PASSWORD=YOUR_PASSWORD
Sau khi chạy xong, bạn sẽ nhận được:
"IDToken": "eyJ..."
![Testing & Validation 10](/Internship/images/5-Workshop/IRMS/section-11-010.png)
Sau đó gọi lại API bằng token:
curl.exe -X POST ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents ^
-H "Content-Type: application/json" ^
-H "Authorization: Bearer YOUR_ID _TOKEN" ^
-d "{\"title\":\"Unauthorized SSH Login\",\"description\":\"Multiple failed SSH login attempts detected\",\"severity\":\"High\",\"source\":\"manual\",\"assignedTo\":\"Security Analyst\"}"
![Testing & Validation 11](/Internship/images/5-Workshop/IRMS/section-11-011.png)
Bước 4 – Kiểm tra Incident vừa tạo
Lấy danh sách Incident.
Chạy: curl.exe ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents ^
-H "Authorization: Bearer YOUR_ID_TOKEN"
Danh sách Incident.
![Testing & Validation 12](/Internship/images/5-Workshop/IRMS/section-11-012.png)
Bước 5 : GET chi tiết incident
Dùng ID vừa tạo:
INC-53DB6458
Chạy:
curl.exe ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents/INC-53DB6458 ^
-H "Authorization: Bearer YOUR_ID_TOKEN"
![Testing & Validation 13](/Internship/images/5-Workshop/IRMS/section-11-013.png)
Bước 6 : PUT cập nhật incident
Chạy:
curl.exe -X PUT ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents/INC-53DB6458 ^
-H "Content-Type: application/json" ^
-H "Authorization: Bearer YOUR_ID_TOKEN" ^
-d "{\"status\":\"In Progress\",\"assignedTo\":\"Security Analyst\",\"actor\":\"analyst@example.com\"}"
Mục tiêu: status đổi từ:
Open
thành:
In Progress
![Testing & Validation 14](/Internship/images/5-Workshop/IRMS/section-11-014.png)
**Bước 7: GET timeline**
Sau khi tạo và update, timeline phải có ít nhất 2 event:
curl.exe ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents/INC-53DB6458/timeline ^
-H "Authorization: Bearer YOUR_ID_TOKEN"
Mục tiêu thấy các event kiểu:
incident.created
incident.updated
![Testing & Validation 15](/Internship/images/5-Workshop/IRMS/section-11-015.png)
**Bước 8: Kiểm tra DynamoDB**
Vào AWS Console:
DynamoDB → Tables → irms-incidents → Explore table items
Tìm :INC-53DB6458
![Testing & Validation 16](/Internship/images/5-Workshop/IRMS/section-11-016.png)
Sau đó kiểm tra timeline:
DynamoDB → Tables → irms-timeline → Explore table items
Tìm các item có:
incidentId = INC-53DB6458
![Testing & Validation 17](/Internship/images/5-Workshop/IRMS/section-11-017.png)

#### 5.5.11.4 Test Evidence Upload
Đây là nơi chúng ta sẽ kiểm tra:

```text
Client
│
▼
API Gateway
│
▼
UploadEvidence Lambda
│
├──────── Generate Presigned URL
│
▼
Amazon S3
│
▼
DynamoDB (Metadata)
```


Mục tiêu
Xác nhận hệ thống có thể:
- Tạo metadata cho Evidence.
- Sinh Presigned URL để upload file lên Amazon S3.
- Upload file lên S3.
- Kiểm tra metadata trong DynamoDB.
- Lấy thông tin Evidence thông qua API.
Kiến trúc
```text
POST /evidence
        │
        ▼
API Gateway
        │
        ▼
UploadEvidence Lambda
        │
        ├──────── PutItem
        │
        ├──────── Generate Presigned URL
        │
        ▼
DynamoDB
        │
        ▼
Amazon S3
```


thực hiện
Bước 1. Kiểm tra source code Chạy:
more backend\upload-evidence\handler.py
Quan sát hai hàm:
- create_evidence()
- lambda_handler()

![Testing & Validation 18](/Internship/images/5-Workshop/IRMS/section-11-018.png)
![Testing & Validation 19](/Internship/images/5-Workshop/IRMS/section-11-019.png)
![Testing & Validation 20](/Internship/images/5-Workshop/IRMS/section-11-020.png)
Chức năng
create_evidence() thực hiện:
- Đọc request body.
- Sinh evidenceId.
- Tạo đường dẫn lưu file trên S3.
- Sinh Presigned URL.
- Lưu metadata vào DynamoDB.
- Trả về metadata và upload URL.
lambda_handler() định tuyến:
POST /evidence
đến
create_evidence()

Bước 2. Tạo file kiểm thử
Tạo file:
echo This is IRMS evidence test file > evidence-test.txt
Kiểm tra:
dir evidence-test.txt
![Testing & Validation 21](/Internship/images/5-Workshop/IRMS/section-11-021.png)
Bước 3. Tạo Evidence Metadata
Gọi API:
curl.exe -X POST ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/evidence ^
-H "Content-Type: application/json" ^
-H "Authorization: Bearer YOUR_ID_TOKEN" ^
-d "{\"incidentId\":\"INC-53DB6458\",\"fileName\":\"evidence-test.txt\",\"fileType\":\"text/plain\",\"description\":\"Test evidence upload from CMD\",\"uploadedBy\":\"analyst@example.com\",\"fileSize\":32}"
Lưu ý: Thay YOUR_ID_TOKEN bằng IdToken vừa lấy từ Amazon Cognito.
API trả về:
![Testing & Validation 22](/Internship/images/5-Workshop/IRMS/section-11-022.png)
Response trả về metadata và upload URL.
Bước 4. Upload file lên Amazon S3
Copy giá trị:
uploadUrl
trong response.
Thực hiện upload:
curl.exe -X PUT ^
"PASTE_UPLOAD_URL_HERE" ^
-H "Content-Type: text/plain" ^
--upload-file evidence-test.txt
Nếu thành công, lệnh sẽ không hiển thị lỗi và file sẽ được upload lên Amazon S3.
![Testing & Validation 23](/Internship/images/5-Workshop/IRMS/section-11-023.png)
Sau đó gọi GET evidence:
curl.exe ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/evidence/845d4a06-f3c2-4a4d-84bb-d937a522da46 ^
-H "Authorization: Bearer YOUR_ID_TOKEN"
![Testing & Validation 24](/Internship/images/5-Workshop/IRMS/section-11-024.png)

#### 5.5.11.5 Test AI Assistant

Mục tiêu: kiểm tra request đã xác thực có đi tới AI Assistant Lambda, Lambda đọc Groq API key từ Secrets Manager, và provider abstraction trả về structured response hoặc fallback response an toàn.

![Testing & Validation 25](/Internship/images/5-Workshop/IRMS/section-11-025.png)
![Testing & Validation 26](/Internship/images/5-Workshop/IRMS/section-11-026.png)

Kiểm tra Groq secret đã cấu hình:

```bash
aws secretsmanager get-secret-value ^
  --secret-id YOUR_GROQ_SECRET_NAME ^
  --region ap-southeast-1 ^
  --profile irms-shared
```

Gọi incident analysis:

```bash
curl.exe -X POST ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/ai/analyze ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"incident\":{\"title\":\"Unauthorized SSH Login\",\"description\":\"Multiple failed SSH login attempts detected from unknown IP\",\"severity\":\"High\",\"source\":\"manual\"}}"
```

Gọi explain severity:

```bash
curl.exe -X POST ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/ai/explain ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"incidentId\":\"INC-53DB6458\",\"severity\":\"High\"}"
```

Hỏi AI theo incident hiện tại:

```bash
curl.exe -X POST ^
  https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/ai/chat ^
  -H "Content-Type: application/json" ^
  -H "Authorization: Bearer YOUR_ID_TOKEN" ^
  -d "{\"incidentId\":\"INC-53DB6458\",\"question\":\"What evidence should I collect next?\"}"
```

Nếu Groq không khả dụng hoặc timeout, Lambda trả về fallback notification và rule-based analysis để người dùng vẫn tiếp tục được quy trình xử lý incident.

![Testing & Validation 28](/Internship/images/5-Workshop/IRMS/section-11-028.png)

#### 5.5.11.6 Test Generate Report
**Bước 1: Test GET /reports**
curl.exe ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/reports ^
-H "Authorization: Bearer YOUR_ID_TOKEN"
Mục tiêu: trả về danh sách report summary.
![Testing & Validation 29](/Internship/images/5-Workshop/IRMS/section-11-029.png)
**Bước 2: Test POST /reports/generate**
Dùng incident đã có:
INC-53DB6458
Chạy:
curl.exe -X POST ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/reports/generate ^
-H "Content-Type: application/json" ^
-H "Authorization: Bearer YOUR_ID_TOKEN" ^
-d "{\"incidentId\":\"INC-53DB6458\"}"
![Testing & Validation 30](/Internship/images/5-Workshop/IRMS/section-11-030.png)

#### 5.5.11.7 Test EventBridge Alert Automation
Phần này kiểm tra luồng tự động:
Simulated GuardDuty Event
↓
EventBridge Rule
↓
AlertHandler Lambda
↓
DynamoDB + SNS
**Bước 1: Tạo file event mẫu**
Tạo file:
notepad guardduty-event.json
Dán nội dung này:
[
{
"Source": "aws.guardduty",
"DetailType": "GuardDuty Finding",
"Detail": "{\"title\":\"Simulated SSH Brute Force Attack\",\"severity\":\"Critical\",\"description\":\"Simulated GuardDuty finding: multiple SSH brute force attempts detected from suspicious IP.\",\"source\":\"guardduty-simulation\",\"assignedTo\":\"soc-team\"}",
"EventBusName": "default"
}
]
Lưu file.
**Bước 2: Gửi event vào EventBridge**
aws events put-events ^
--entries file://guardduty-event.json ^
--region ap-southeast-1 ^
--profile irms-shared
![Testing & Validation 31](/Internship/images/5-Workshop/IRMS/section-11-031.png)
**Bước 3: Kiểm tra Incident tự động**
curl.exe ^
https://07frck8bih.execute-api.ap-southeast-1.amazonaws.com/dev/incidents ^
-H "Authorization: Bearer YOUR_ID_TOKEN"
Tìm incident: Simulated SSH Brute Force Attack
![Testing & Validation 32](/Internship/images/5-Workshop/IRMS/section-11-032.png)
**Bước 5: Kiểm tra DynamoDB**
Vào:
DynamoDB → irms-incidents → Explore table items
Tìm incident có:
source = guardduty-simulation
createdBy = alert-handler
![Testing & Validation 33](/Internship/images/5-Workshop/IRMS/section-11-033.png)
**Bước 6: Kiểm tra CloudWatch**
aws logs describe-log-groups ^
--log-group-name-prefix /aws/lambda/alert-handler ^
--region ap-southeast-1 ^
--profile irms-shared
![Testing & Validation 34](/Internship/images/5-Workshop/IRMS/section-11-034.png)
#### 5.5.11.8 CloudWatch Logs and Final Validation
Chạy các lệnh kiểm tra tổng thể:
aws cloudformation describe-stacks ^
--stack-name irms-serverless ^
--region ap-southeast-1 ^
--profile irms-shared ^
--query "Stacks[0].StackStatus"
![Testing & Validation 35](/Internship/images/5-Workshop/IRMS/section-11-035.png)
aws lambda list-functions ^
--region ap-southeast-1 ^
--profile irms-shared ^
--query "Functions[].FunctionName"
![Testing & Validation 36](/Internship/images/5-Workshop/IRMS/section-11-036.png)
aws dynamodb list-tables ^
--region ap-southeast-1 ^
--profile irms-shared
![Testing & Validation 37](/Internship/images/5-Workshop/IRMS/section-11-037.png)
aws s3 ls ^
--profile irms-shared
![Testing & Validation 38](/Internship/images/5-Workshop/IRMS/section-11-038.png)
aws events list-rules ^
--region ap-southeast-1 ^
--profile irms-shared
![Testing & Validation 39](/Internship/images/5-Workshop/IRMS/section-11-039.png)
Kết quả mong đợi cuối 11
Sau khi hoàn thành 11, bạn có thể chứng minh:
- ✅ API Gateway gọi được Lambda
✅ Incident CRUD hoạt động
✅ Timeline hoạt động
✅ Evidence upload lên S3 hoạt động
✅ AI Assistant gọi Groq hoặc fallback hoạt động
✅ EventBridge tự động tạo incident hoạt động
✅ SNS alert hoạt động
✅ Report CSV được tạo lên S3
✅ CloudWatch Logs có log kiểm chứng
