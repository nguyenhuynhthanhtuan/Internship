---
title: "Cấu hình hạ tầng"
date: 2026-07-17
weight: 3
chapter: false
pre: " <b> 5.3. </b> "
---

## 5.3. Cấu hình hạ tầng

#### Nội dung

- 5.3.3 Setup Authentication
- 5.3.4 Setup API Gateway
- 5.3.5 Setup DynamoDB
- 5.3.6 Setup S3 & Storage

### 5.3.3 Setup Authentication (Amazon Cognito)

#### 5.3.3.1 Chuẩn bị trước khi bắt đầu
Mở sẵn 2 thứ này trước:
Thứ 1 — AWS Console trên trình duyệt:
- Mở trình duyệt (Chrome/Edge), vào địa chỉ: https://console.aws.amazon.com
- Đăng nhập bằng IAM User của bạn
- Góc trên bên phải, kiểm tra region đang chọn — phải là Asia Pacific (Singapore) ap-southeast-1. Nếu không đúng, click vào tên region → chọn Asia Pacific (Singapore)
Thứ 2 — Terminal trên máy tính:
Windows: Nhấn phím Windows → gõ PowerShell → Enter. Hoặc mở Git Bash nếu đã cài Git.
Mac: Nhấn Cmd + Space → gõ Terminal → Enter.
Cấu hình AWS CLI
-Tạo accesskey
-Tạo profile, sau đó nhâp accsess key và secret key
Kiểm tra AWS CLI đã cấu hình đúng chưa bằng lệnh:

```bash
aws sts get-caller-identity --profile irms-shared
```

![Setup Authentication 1](/Internship/images/5-Workshop/IRMS/section-03-001.png)
Kết quả mong đợi (thấy Account ID và UserId là đúng):

```json
{
"UserId": "AIDXXXXXXXXXXXXXXXXX",
"Account": "123456789012",
"Arn": "arn:aws:iam::123456789012:user/your-username"
}
```

#### 5.3.3.2 Tạo Cognito User Pool (trên AWS Console)
**Bước 1: Trên AWS Console, thanh tìm kiếm phía trên gõ Cognito → click Amazon Cognito**
**Bước 2: Click nút Create user pool (góc trên bên phải)**
**Bước 3: Trang Configure sign-in experience:**
- Authentication providers: chọn Cognito user pool
- Cognito user pool sign-in options: tích ✅ vào Email
- Click Next
**Bước 4: Trang Configure security requirements:**
- Password policy mode: chọn Custom
- Minimum length: 8
- Tích ✅: Contains at least 1 uppercase, 1 lowercase, 1 number, 1 special character
- Multi-factor authentication: chọn No MFA (demo không cần)
- Click Next
**Bước 5: Trang Configure sign-up experience:**
- Giữ mặc định tất cả
- Click Next
**Bước 6: Trang Configure message delivery:**
- Email provider: chọn Send email with Cognito (miễn phí, đủ dùng cho demo)
- Click Next
**Bước 7: Trang Integrate your app:**
- User pool name: irms-user-pool
- App client name: irms-web-client
- Client secret: chọn Don't generate a client secret
- Click Next
**Bước 8: Trang Review and create:**
- Kéo xuống xem lại toàn bộ
- Click Create user pool
**Bước 9: Sau khi tạo xong, trang hiện ra User Pool mới. Lưu lại 2 giá trị này vào file notepad/txt trên máy:**
User Pool ID:  ap-southeast-1_XXXXXXXXX  ← thấy ở tab "Overview"
Client ID:     XXXXXXXXXXXXXXXXXXXXXXXXXX ← vào tab "App clients" để xem
2 giá trị này sẽ dùng nhiều lần ở các bước sau, lưu lại cẩn thận.

#### 5.3.3.3 Tạo 4 Groups (RBAC)
Vẫn ở trang Cognito User Pool vừa tạo:
**Bước 1: Click tab Groups → Click Create group**
**Bước 2: Tạo Group 1:**
Group name:  Admin
Description: Toàn quyền hệ thống
Precedence:  1
Click Create group
**Bước 3: Lặp lại, tạo thêm 3 group:**
Group name:  SecurityManager
Description: Xem tất cả Incident, phân công, xem báo cáo
Precedence:  2

Group name:  SecurityAnalyst
Description: Điều tra, upload bằng chứng, cập nhật trạng thái
Precedence:  3

Group name:  Auditor
Description: Chỉ xem, không chỉnh sửa
Precedence:  4
Sau khi xong, tab Groups phải hiện đủ 4 group như sau:
Admin            (Precedence: 1)
SecurityManager  (Precedence: 2)
SecurityAnalyst  (Precedence: 3)
Auditor          (Precedence: 4)
![Setup Authentication 2](/Internship/images/5-Workshop/IRMS/section-03-002.png)

#### 5.3.3.4 Tạo User test
Vẫn ở trang User Pool, click tab Users → Create user:
User 1 (Admin):
Invitation message:     Don't send an invitation
Email address:          admin@example.com
Email address verified: tích ✅
Temporary password:     chọn "Set a password"
Password:               YOUR_TEMP_PASSWORD
User must create a new password: bỏ tích ✅
Click Create user → click vào user vừa tạo → Add user to group → chọn Admin
![Setup Authentication 3](/Internship/images/5-Workshop/IRMS/section-03-003.png)
Lặp lại tương tự cho 3 user còn lại, gắn vào đúng group:
manager@example.com  → group SecurityManager  → pass: YOUR_TEMP_PASSWORD
analyst@example.com  → group SecurityAnalyst   → pass: YOUR_TEMP_PASSWORD
auditor@example.com  → group Auditor           → pass: YOUR_TEMP_PASSWORD
![Setup Authentication 4](/Internship/images/5-Workshop/IRMS/section-03-004.png)

#### 5.3.3.5 Verify bằng Terminal
Mở Terminal trên máy, chạy lệnh sau (thay YOUR_CLIENT_ID bằng Client ID đã lưu ở bước 3.2):

```bash
aws cognito-idp initiate-auth \
--auth-flow USER_PASSWORD_AUTH \
--auth-parameters USERNAME=analyst@example.com,PASSWORD=YOUR_TEMP_PASSWORD \
--client-id YOUR_CLIENT_ID \
--region ap-southeast-1 \
--profile irms-shared
Kết quả mong đợi — thấy 3 token là thành công:
```

json
{
"AuthenticationResult": {
"AccessToken": "eyJraWQiOi...",
"IdToken": "eyJraWQiOi...",
"RefreshToken": "eyJjdHki...",
"ExpiresIn": 3600,
"TokenType": "Bearer"
}
}
![Setup Authentication 5](/Internship/images/5-Workshop/IRMS/section-03-005.png)
![Setup Authentication 6](/Internship/images/5-Workshop/IRMS/section-03-006.png)
![Setup Authentication 7](/Internship/images/5-Workshop/IRMS/section-03-007.png)
Nếu thấy lỗi NotAuthorizedException → kiểm tra lại password hoặc email.
Nếu thấy lỗi UserNotFoundException → kiểm tra lại email đã tạo đúng chưa.
Kiểm tra JWT token:
- Copy toàn bộ chuỗi IdToken (dài, bắt đầu bằng eyJ...)
- Mở trình duyệt, vào https://jwt.io
- Paste vào ô Encoded bên trái
- Bên phải mục Payload phải thấy:

```json
{
"email": "analyst@example.com",
"cognito:groups": ["SecurityAnalyst"],
```

...
}
Thấy đúng 2 field trên →  Cognito hoàn thành.
![Setup Authentication 8](/Internship/images/5-Workshop/IRMS/section-03-008.png)
Nhận xét này rất chính xác — tất cả 8 điểm đều đúng. Tớ viết lại 4 theo đúng hướng đó:

### 5.3.4 Setup API Gateway
Lưu ý về cách tiếp cận: này chỉ dùng Console để hiểu cơ chế Cognito Authorizer và test JWT token. Toàn bộ API routes thật (/incidents, /evidence, /reports, /ai) sẽ do SAM tự sinh ở 7 khi deploy Lambda — không tạo tay ở đây để tránh conflict.

#### 5.3.4.1 Tạo REST API
**Bước 1: Trên AWS Console, thanh tìm kiếm gõ API Gateway → click API Gateway**
**Bước 2: Click Create API**
![Setup API Gateway 1](/Internship/images/5-Workshop/IRMS/section-04-001.png)
**Bước 3: Trang chọn loại API:**
- Chọn REST API (không phải REST API Private, không phải HTTP API)
- Click Build
![Setup API Gateway 2](/Internship/images/5-Workshop/IRMS/section-04-002.png)
**Bước 4: Điền thông tin:**
API details:        New API
API name:           irms-api-learning
Description:        Dùng để học Authorizer, sẽ xóa sau khi sang 7
API endpoint type:  Regional
Click Create API
![Setup API Gateway 3](/Internship/images/5-Workshop/IRMS/section-04-003.png)
⚠️ API này đặt tên irms-api-learning để phân biệt với API thật do SAM tạo sau. Sau khi test xong Authorizer ở này, API này có thể xóa đi — không ảnh hưởng gì đến hệ thống.

#### 5.3.4.2 Tạo Cognito Authorizer
**Bước 1: Menu bên trái → click Authorizers → click Create authorizer**
**Bước 2: Điền thông tin:**
Authorizer name:    irms-cognito-authorizer
Authorizer type:    Cognito
Cognito user pool:  chọn "irms-user-pool"
Token source:       Authorization
Token validation:   (để trống)
Click Create authorizer
![Setup API Gateway 4](/Internship/images/5-Workshop/IRMS/section-04-004.png)
![Setup API Gateway 5](/Internship/images/5-Workshop/IRMS/section-04-005.png)

#### 5.3.4.3 Tạo 2 routes test
Vào Resources ở menu bên trái. Mục tiêu tạo 2 route để test rõ ràng:
GET /ping       → không có Authorizer → luôn trả 200
GET /ping-auth  → có Authorizer       → 401 nếu không có token, 200 nếu có token
Tạo /ping (không có Authorizer):
**Bước 1: Click vào / (root) → click Create resource**
Resource name:  ping
Resource path:  /ping
CORS:           KHÔNG tích (để SAM quản lý sau)
Click Create resource
**Bước 2: Click vào /ping → click Create method**
Method type:        GET
Integration type:   Mock
![Setup API Gateway 6](/Internship/images/5-Workshop/IRMS/section-04-006.png)
Click Create method
![Setup API Gateway 7](/Internship/images/5-Workshop/IRMS/section-04-007.png)
**Bước 3: Vào Integration Response → Mapping Templates → thêm template:**
Content-Type:   application/json
Template body:
{
"message": "pong",
"auth": false
}
Click Save
**Bước 4: Xác nhận /ping không gắn Authorizer:**
- Click method GET
- Tab Method request → Authorization: phải là NONE

Tạo /ping-auth (có Authorizer):
**Bước 1: Click vào / (root) → click Create resource**
Resource name:  ping-auth
Resource path:  /ping-auth
CORS:           KHÔNG tích
Click Create resource
**Bước 2: Click vào /ping-auth → click Create method**
Method type:        GET
Integration type:   Mock
Click Create method
![Setup API Gateway 8](/Internship/images/5-Workshop/IRMS/section-04-008.png)
**Bước 3: Vào Integration Response → Mapping Templates → thêm template:**
Content-Type:   application/json
Template body:
{
"message": "Authorized",
"auth": true
}
Click Save
![Setup API Gateway 9](/Internship/images/5-Workshop/IRMS/section-04-009.png)
**Bước 4: Gắn Authorizer vào /ping-auth:**
- Click method GET của /ping-auth
- Tab Method request → Authorization → click Edit
- Chọn irms-cognito-authorizer
- Click Save để lưu

#### 5.3.4.4 Deploy stage dev
**Bước 1: Click Deploy API (góc trên bên phải)**
**Bước 2: Cửa sổ Deploy API:**
Stage:       *New stage*
Stage name:  dev
Click Deploy
![Setup API Gateway 10](/Internship/images/5-Workshop/IRMS/section-04-010.png)
![Setup API Gateway 11](/Internship/images/5-Workshop/IRMS/section-04-011.png)
**Bước 3: Trang Stage Editor hiện ra → lưu Invoke URL:**
Invoke URL: https://ymag7i4369.execute-api.ap-southeast-1.amazonaws.com/dev
⚠️ URL này chỉ dùng để test Authorizer trong 4. URL thật của hệ thống sẽ do SAM tạo ở 7 và có giá trị khác — lúc đó dùng URL của SAM, bỏ URL này đi.

#### 5.3.4.5 Test bằng Postman
Cài Postman nếu chưa có:
- Vào https://www.postman.com/downloads/
- Download → cài đặt → mở lên → chọn Skip (không cần tạo account)
Lấy JWT token từ Cognito:
Mở Terminal (Windows: PowerShell hoặc Git Bash / Mac: Terminal), chạy lệnh sau (thay YOUR_CLIENT_ID bằng Client ID đã lưu ở 3):
aws cognito-idp initiate-auth \
--auth-flow USER_PASSWORD_AUTH \
--auth-parameters USERNAME=analyst@example.com,PASSWORD=YOUR_PASSWORD\
--client-id YOUR_CLIENT_ID \
--region ap-southeast-1 \
--profile irms-shared
![Setup API Gateway 12](/Internship/images/5-Workshop/IRMS/section-04-012.png)
Copy toàn bộ chuỗi IdToken (bắt đầu bằng eyJ...) vào notepad.

Test 1 — /ping không cần token:
Mở Postman:
- Method: GET
- URL: https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/dev/ping
- Không thêm gì ở Headers
- Click Send
Kết quả mong đợi:
{
"message": "pong",
"auth": false
}
![Setup API Gateway 13](/Internship/images/5-Workshop/IRMS/section-04-013.png)
Status: 200 OK → ✅ API Gateway hoạt động

Test 2 — /ping-auth không có token:
- Method: GET
- URL: https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/dev/ping-auth
- Không thêm gì ở Headers
- Click Send
Kết quả mong đợi:
{
"message": "Unauthorized"
}
![Setup API Gateway 14](/Internship/images/5-Workshop/IRMS/section-04-014.png)
Status: 401 Unauthorized → ✅ Authorizer đang chặn đúng

Test 3 — /ping-auth có token:
- Method: GET
- URL: https://xxxxxxxxxx.execute-api.ap-southeast-1.amazonaws.com/dev/ping-auth
- Tab Headers → thêm:
Key:    Authorization
Value:  Bearer <eyJraWQiOi...>   ← paste IdToken vào đây
- Click Send
Kết quả mong đợi:
{
"message": "Authorized",
"auth": true
}
![Setup API Gateway 15](/Internship/images/5-Workshop/IRMS/section-04-015.png)
Status: 200 OK → ✅ Authorizer xác thực JWT thành công

Test 4 — /ping-auth với token sai:
- Giữ nguyên setup Test 3 nhưng sửa token thành chuỗi bất kỳ:
Authorization: Bearer thisisafaketoken
- Click Send
Kết quả mong đợi:
{
"message": "Unauthorized"
}
![Setup API Gateway 16](/Internship/images/5-Workshop/IRMS/section-04-016.png)
Status: 401 Unauthorized → ✅ Authorizer từ chối token không hợp lệ

#### 5.3.4.6 Checklist 4
Trước khi sang 5, xác nhận đủ các mục sau:
- ✅ API Gateway (Regional) đã tạo tên irms-api-learning
- ✅ Cognito Authorizer đã tạo, trỏ đúng vào irms-user-pool
- ✅ GET /ping  → Mock, không có Authorizer
- ✅ GET /ping-auth → Mock, có Cognito Authorizer
- ✅ Đã deploy stage dev
- ✅ Test Postman:
/ping          → 200 (không cần token)
/ping-auth     → 401 (không có token)
/ping-auth     → 200 (có token hợp lệ)
/ping-auth     → 401 (token sai)
- ✅ Đã lưu Invoke URL (chỉ dùng tạm cho này)

### 5.3.5 Setup DynamoDB

#### 5.3.5.1 Thiết kế Schema trước khi tạo
Trước khi vào Console, cần hiểu rõ cấu trúc dữ liệu. IRMS dùng 4 bảng:
Incidents          → lưu thông tin sự cố
Timeline           → lưu lịch sử từng bước xử lý
EvidenceMetadata   → lưu thông tin file bằng chứng
Users              → lưu thông tin người dùng
Chi tiết từng bảng:
Bảng Incidents:
Partition key:  incidentId (String)
GSI 1:          status-index     → query theo trạng thái (Open/Investigating/Closed)
GSI 2:          assignedTo-index → query theo người được phân công
Attributes:
incidentId      String   "INC-001"
title           String   "SQL Injection Attack"
severity        String   "Critical / High / Medium / Low"
status          String   "Open / Investigating / Containment / Resolved / Closed"
description     String   mô tả chi tiết
assignedTo      String   email người xử lý
createdBy       String   email người tạo
createdAt       String   ISO timestamp
updatedAt       String   ISO timestamp

Bảng Timeline:
Partition key:  incidentId (String)
Sort key:       timestamp  (String)
Attributes:
incidentId   String
timestamp    String   ISO timestamp
action       String   "Created / Assigned / Status Changed / Note Added"
description  String   mô tả hành động
performedBy  String   email người thực hiện

Bảng EvidenceMetadata:
Partition key:  evidenceId  (String)
GSI 1:          incidentId-index → query evidence theo incident
Attributes:
evidenceId    String   UUID
incidentId    String   "INC-001"
fileName      String   "screenshot.png"
fileType      String   "image/png"
s3Key         String   đường dẫn trong S3
uploadedBy    String   email người upload
uploadedAt    String   ISO timestamp
fileSize      Number   bytes

Bảng Users:
Partition key:  email (String)
Attributes:
email         String
fullName      String
role          String   "Admin / SecurityManager / SecurityAnalyst / Auditor"
createdAt     String

#### 5.3.5.2 Tạo bảng Incidents
**Bước 1: Trên AWS Console, thanh tìm kiếm gõ DynamoDB → click DynamoDB**
**Bước 2: Click Create table**
**Bước 3: Điền thông tin cơ bản:**
Table name:           irms-incidents
Partition key:        incidentId    Type: String
Sort key:             (để trống)
![Setup DynamoDB 1](/Internship/images/5-Workshop/IRMS/section-05-001.png)
**Bước 4: Table settings → chọn Customize settings**
**Bước 5: Table class: DynamoDB Standard**
**Bước 6: Read/write capacity settings:**
Capacity mode: On-demand
![Setup DynamoDB 2](/Internship/images/5-Workshop/IRMS/section-05-002.png)
Chọn On-demand vì demo không có traffic ổn định, chỉ trả phí theo lượt request thực tế — tránh bị tính phí khi bảng không dùng.
**Bước 7: Encryption: Owned by Amazon DynamoDB (mặc định, đủ cho demo)**
![Setup DynamoDB 3](/Internship/images/5-Workshop/IRMS/section-05-003.png)
**Bước 8: Click Create table, chờ khoảng 30 giây đến khi Status chuyển sang Active**
![Setup DynamoDB 4](/Internship/images/5-Workshop/IRMS/section-05-004.png)

#### 5.3.5.3 Tạo GSI cho bảng Incidents
Sau khi bảng Active, tạo Global Secondary Index để query nhanh theo status và assignedTo:
**Bước 1: Click vào bảng irms-incidents → tab Indexes → click Create index**
![Setup DynamoDB 5](/Internship/images/5-Workshop/IRMS/section-05-005.png)
Tạo GSI 1 — query theo status:
Partition key:   status      Type: String
Sort key:        createdAt   Type: String
Index name:      status-index
Projected attributes: All
Click Create index → chờ Status chuyển sang Active
![Setup DynamoDB 6](/Internship/images/5-Workshop/IRMS/section-05-006.png)
Tạo GSI 2 — query theo người được phân công:
Click Create index lần nữa:
Partition key:   assignedTo   Type: String
Sort key:        createdAt    Type: String
Index name:      assignedTo-index
Projected attributes: All
Click Create index → chờ Active
![Setup DynamoDB 7](/Internship/images/5-Workshop/IRMS/section-05-007.png)

#### 5.3.5.4 Tạo bảng Timeline
**Bước 1: Click Create table**
**Bước 2:**
Table name:      irms-timeline
Partition key:   incidentId    Type: String
Sort key:        timestamp     Type: String
Sort key là timestamp vì trong 1 incident có nhiều mốc thời gian, sort key giúp tự động sắp xếp theo thứ tự thời gian.
**Bước 3: Table settings → Customize settings → Capacity mode: On-demand**
**Bước 4: Click Create table → chờ Active**
![Setup DynamoDB 8](/Internship/images/5-Workshop/IRMS/section-05-008.png)

#### 5.3.5.5 Tạo bảng EvidenceMetadata
**Bước 1: Click Create table**
**Bước 2:**
Table name:      irms-evidence-metadata
Partition key:   evidenceId    Type: String
Sort key:        (để trống)
**Bước 3: Table settings → Customize settings → Capacity mode: On-demand**
**Bước 4: Click Create table → chờ Active**
**Bước 5: Tạo GSI để query evidence theo incidentId:**
Tab Indexes → Create index:
Partition key:   incidentId    Type: String
Sort key:        uploadedAt    Type: String
Index name:      incidentId-index
Projected attributes: All
Click Create index → chờ Active
![Setup DynamoDB 9](/Internship/images/5-Workshop/IRMS/section-05-009.png)

#### 5.3.5.6 Tạo bảng Users
**Bước 1: Click Create table**
**Bước 2:**
Table name:      irms-users
Partition key:   email    Type: String
Sort key:        (để trống)
**Bước 3: Table settings → Customize settings → Capacity mode: On-demand**
**Bước 4: Click Create table → chờ Active**
![Setup DynamoDB 10](/Internship/images/5-Workshop/IRMS/section-05-010.png)

#### 5.3.5.7 Thêm dữ liệu mẫu để test
Sau khi tạo đủ 4 bảng, thêm vài record mẫu vào bảng Incidents để test Lambda sau này:
**Bước 1: Click vào bảng irms-incidents → tab Explore items → click Create item**
![Setup DynamoDB 11](/Internship/images/5-Workshop/IRMS/section-05-011.png)
**Bước 2: Chọn JSON view, paste nội dung sau:**
{
"incidentId": { "S": "INC-001" },
"title": { "S": "Public S3 Bucket Detected" },
"severity": { "S": "Critical" },
"status": { "S": "Open" },
"description": { "S": "S3 bucket company-data đang bị public access, có thể lộ dữ liệu khách hàng" },
"assignedTo": { "S": "analyst@example.com" },
"createdBy": { "S": "admin@example.com" },
"createdAt": { "S": "2026-06-01T08:00:00Z" },
"updatedAt": { "S": "2026-06-01T08:00:00Z" }
}
Click Create item
**Bước 3: Tạo thêm record thứ 2:**
{
"incidentId": "INC-002",
"title": "SSH Brute Force Attack",
"severity": "High",
"status": "Investigating",
"description": "Phát hiện 500 lần đăng nhập SSH thất bại từ IP 203.0.113.42 trong 10 phút",
"assignedTo": "analyst@example.com",
"createdBy": "manager@example.com",
"createdAt": "2026-06-02T10:30:00Z",
"updatedAt": "2026-06-02T11:00:00Z"
}
Click Create item
![Setup DynamoDB 12](/Internship/images/5-Workshop/IRMS/section-05-012.png)
![Setup DynamoDB 13](/Internship/images/5-Workshop/IRMS/section-05-013.png)

#### 5.3.5.8 Verify bằng Console
**Bước 1: Click vào bảng irms-incidents → tab Explore items**
**Bước 2: Thấy 2 record INC-001 và INC-002 → ✅**
![Setup DynamoDB 14](/Internship/images/5-Workshop/IRMS/section-05-014.png)
**Bước 3: Test query theo GSI:**
- Click Scan/Query → chọn Query
- Index: chọn status-index
- Partition key value: Open
- Click Run
- Kết quả phải trả về INC-001 → ✅ GSI hoạt động đúng
![Setup DynamoDB 15](/Internship/images/5-Workshop/IRMS/section-05-015.png)

#### 5.3.5.9 Hybrid Deployment với AWS SAM
Trong workshop này, các bảng Amazon DynamoDB được tạo thủ công bằng AWS Console ở các bước trước nhằm giúp người học hiểu rõ cách thiết kế schema, Partition Key, Sort Key và Global Secondary Index (GSI).
Sau khi hoàn thành phần thực hành với Console, dự án IRMS chuyển sang mô hình Hybrid Deployment.
Ở mô hình này:
- Amazon Cognito và Amazon DynamoDB được giữ nguyên từ các bước cấu hình bằng Console.
- AWS SAM sẽ chỉ triển khai các thành phần còn lại của hệ thống như:
- Amazon API Gateway
- AWS Lambda
- Amazon S3
- Amazon SNS
- Amazon EventBridge
- AWS Secrets Manager
Điều này giúp:
- Tránh tạo trùng các bảng DynamoDB.
- Đảm bảo dữ liệu mẫu đã tạo ở 5 vẫn được giữ nguyên.
- Giúp người học vừa hiểu cách cấu hình thủ công, vừa làm quen với Infrastructure as Code (IaC) bằng AWS SAM.
Các bảng DynamoDB được SAM sử dụng
Thay vì tạo mới, template AWS SAM sẽ tham chiếu trực tiếp đến các bảng đã tồn tại:

| Table | Mục đích |
| --- | --- |
| irms-incidents | Lưu thông tin Incident |
| irms-timeline | Lưu Timeline xử lý |
| irms-evidence-metadata | Lưu metadata Evidence |
| irms-users | Lưu thông tin người dùng |

Tên các bảng sẽ được truyền vào Lambda thông qua SAM Parameters và Environment Variables, vì vậy không cần tạo lại bằng CloudFormation.
File template sử dụng
Trong repository IRMS Source Code, toàn bộ hạ tầng được quản lý bởi:
infrastructure/template.yaml
Template này đã được cấu hình theo mô hình Hybrid Deployment, bao gồm:
- Sử dụng Amazon Cognito User Pool đã tạo sẵn.
- Sử dụng các DynamoDB Tables đã tạo ở 5.
- Tạo mới:
- Amazon API Gateway
- AWS Lambda Functions
- Amazon S3 Buckets
- Amazon SNS
- Amazon EventBridge
- AWS Secrets Manager
Kiểm tra cấu hình
Có thể kiểm tra template trước khi triển khai bằng lệnh:
sam validate ^
  --template-file infrastructure/template.yaml ^
  --region ap-southeast-1
Kết quả mong đợi:
Template provided at 'infrastructure/template.yaml' was successfully validated.
![Setup DynamoDB 16](/Internship/images/5-Workshop/IRMS/section-05-016.png)

#### 5.3.5.10 Checklist 5
- ✅ Đã tạo thành công 4 bảng DynamoDB:
- irms-incidents
- irms-timeline
- irms-evidence-metadata
- irms-users
- ✅ Hai Global Secondary Index của bảng irms-incidents hoạt động:
- status-index
- assignedTo-index
- ✅ Global Secondary Index của bảng irms-evidence-metadata hoạt động:
- incidentId-index
- ✅ Đã thêm 2 bản ghi mẫu (INC-001, INC-002) vào bảng irms-incidents.
- ✅ Đã kiểm tra truy vấn thông qua status-index trả về đúng kết quả.
- ✅ Đã xác thực (sam validate) thành công Hybrid SAM Template cho giai đoạn triển khai tiếp theo.

### 5.3.6 Setup S3 & Storage

#### 5.3.6.1 Storage Design
IRMS sử dụng 2 S3 bucket với mục đích hoàn toàn khác nhau:
irms-frontend-031577240048-ap-southeast-1   → lưu React SPA (public qua CloudFront)
irms-evidence-031577240048-ap-southeast-1   → lưu file bằng chứng (private, chỉ Lambda truy cập)
Lý do thêm Account ID và Region vào tên bucket: S3 bucket name là unique toàn cầu — dùng Account ID + Region đảm bảo tên luôn độc nhất, tránh conflict với người khác.
So sánh 2 bucket:

|  | irms-frontend-... | irms-evidence-... |
| --- | --- | --- |
| Public access | Block (chỉ CloudFront được đọc) | Block hoàn toàn |
| Ai truy cập | CloudFront | Lambda function |
| Lưu gì | HTML, CSS, JS, assets | Screenshot, log, PCAP, PDF report |
| Versioning | Không cần | Bật (bảo vệ bằng chứng) |
| CORS | Không cần | Cần (presigned URL upload từ browser) |

#### 5.3.6.2 Evidence Upload Flow

Cần hiểu luồng upload file bằng chứng để hiểu tại sao Evidence bucket cần CORS:
```text
Browser                Lambda                    S3
│                      │                       │
│──POST /evidence──────►│                       │
│  (filename, type)     │                       │
│                       │──GeneratePresignedURL─►│
│                       │◄──presignedUrl─────────│
│◄──presignedUrl────────│                       │
│                       │                       │
│──PUT presignedUrl──────────────────────────────►│
│  (file binary)        │                  file saved
│◄──200 OK──────────────────────────────────────│
│                       │                       │
│──POST /evidence/{id} ►│                      │
│  (evidenceId)         │──PutItem──────────────►DynamoDB
│◄──200 OK──────────────│
```

Ưu điểm của Presigned URL: File upload thẳng từ browser lên S3, không đi qua Lambda — tránh giới hạn 6MB payload của Lambda và tiết kiệm bandwidth.


#### 5.3.6.3 Kiểm tra cấu hình S3 trong template.yaml
Template đã có sẵn trong repo — này chỉ đọc hiểu cấu hình, không thêm resource mới.
Mở file infrastructure/template.yaml, tìm phần S3 và đọc từng thuộc tính:
Frontend Bucket:
FrontendBucket:
Type: AWS::S3::Bucket
Properties:
BucketName: !Ref FrontendBucketName
`# Block toàn bộ public access`
`# CloudFront sẽ truy cập qua OAC (setup ở phần sau)`
PublicAccessBlockConfiguration:
BlockPublicAcls: true
BlockPublicPolicy: true
IgnorePublicAcls: true
RestrictPublicBuckets: true
Những điểm cần lưu ý:
- BlockPublicAcls: true → không ai được set ACL public cho object
- BlockPublicPolicy: true → không ai được tạo bucket policy cho phép public
- Không có WebsiteConfiguration vì frontend phân phối qua CloudFront, không phải S3 static website hosting trực tiếp
Evidence Bucket:
EvidenceBucket:
Type: AWS::S3::Bucket
Properties:
BucketName: !Ref EvidenceBucketName
PublicAccessBlockConfiguration:
BlockPublicAcls: true
BlockPublicPolicy: true
IgnorePublicAcls: true
RestrictPublicBuckets: true
`# Versioning ON: bảo vệ bằng chứng khỏi bị xóa/ghi đè`
VersioningConfiguration:
Status: Enabled
`# CORS: cho phép browser PUT file qua Presigned URL`
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
Những điểm cần lưu ý:
- VersioningConfiguration: Enabled → mỗi lần upload file cùng tên sẽ tạo version mới, không ghi đè — quan trọng với bằng chứng pháp lý
- CorsRules cho phép PUT từ AllowedOrigins: "*" → browser có thể upload thẳng qua Presigned URL
- MaxAge: 3600 → browser cache CORS preflight response 1 tiếng, giảm số lần OPTIONS request

#### 5.3.6.4 Kiểm tra Parameters trong template.yaml
Tìm phần Parameters, xác nhận có đủ 2 bucket name và các giá trị default đúng:
Parameters:

`# ── S3 ──────────────────────────────────────────────────────`
FrontendBucketName:
Type: String
Default: irms-frontend-031577240048-ap-southeast-1

EvidenceBucketName:
Type: String
Default: irms-evidence-031577240048-ap-southeast-1
Nếu thấy đúng 2 tên này → ✅ tiếp tục.
![Setup S3 & Storage 1](/Internship/images/5-Workshop/IRMS/section-06-001.png)

#### 5.3.6.5 Kiểm tra Environment Variables Lambda trong template.yaml
Lambda cần biết tên Evidence bucket để tạo Presigned URL. Tìm phần Globals xác nhận có:
Globals:
Function:
Environment:
Variables:
EVIDENCE_BUCKET: !Ref EvidenceBucketName
Hoặc từng Lambda Upload Evidence có riêng:
UploadEvidenceFunction:
Type: AWS::Serverless::Function
Properties:
Environment:
Variables:
EVIDENCE_BUCKET: !Ref EvidenceBucketName

#### 5.3.6.6 Kiểm tra samconfig.toml
Mở file infrastructure/samconfig.toml, xác nhận đúng cấu hình:
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
Thay các giá trị XXXXXXXXX bằng giá trị thật đã lưu ở 3.
![Setup S3 & Storage 2](/Internship/images/5-Workshop/IRMS/section-06-002.png)

#### 5.3.6.7 Validate Template
Sau khi xác nhận đủ các mục trên, chạy lệnh validate để kiểm tra template không có lỗi cú pháp:
Mở Terminal, di chuyển vào thư mục root của repo:
cd irms
Chạy validate:
sam validate ^
--template-file infrastructure/template.yaml ^
--region ap-southeast-1
Kết quả mong đợi:
Template provided at 'infrastructure/template.yaml' was successfully validated.
![Setup S3 & Storage 3](/Internship/images/5-Workshop/IRMS/section-06-003.png)
Xử lý lỗi thường gặp:
Nếu thấy:
Error: Template format error: YAML not well-formed
→ Lỗi cú pháp YAML, thường do indent sai. Mở template.yaml kiểm tra lại dòng được chỉ ra trong thông báo lỗi. Mỗi cấp indent phải là 2 spaces, không dùng tab.
Nếu thấy:
Error: [InvalidResourceException] Resource 'XYZ' not found
→ Một resource đang tham chiếu đến resource chưa có trong template. Bình thường nếu là tham chiếu CloudFront/WAF (sẽ thêm ở sau), cần hỏi lại team.

#### 5.3.6.8 Checklist 6
- ✅ Hiểu rõ mục đích 2 bucket:
irms-frontend-031577240048-ap-southeast-1  → React SPA, qua CloudFront
irms-evidence-031577240048-ap-southeast-1  → file bằng chứng, qua Lambda
- ✅ Hiểu luồng Presigned URL (Browser → Lambda lấy URL → PUT thẳng lên S3)
- ✅ Đọc hiểu cấu hình FrontendBucket trong template.yaml:
BlockPublicAcls, BlockPublicPolicy = true
- ✅ Đọc hiểu cấu hình EvidenceBucket trong template.yaml:
Versioning ON, CORS cho PUT
- ✅ Xác nhận Parameters FrontendBucketName và EvidenceBucketName đúng tên
- ✅ Xác nhận EVIDENCE_BUCKET có trong Environment Variables của Lambda
- ✅ Xác nhận samconfig.toml dùng stack_name = "irms-dev"
- ✅ sam validate chạy thành công
