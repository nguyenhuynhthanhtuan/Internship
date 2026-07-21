---
title: "Đề xuất"
date: 2026-07-03
weight: 2
chapter: false
pre: " <b> 2. </b> "
---


# IRMS - Incident Response Management System

## Đề xuất dự án

### 1. Tóm tắt

IRMS (Incident Response Management System) là nền tảng quản lý sự cố an ninh mạng theo hướng SaaS, được xây dựng trên các dịch vụ serverless của AWS. Dự án được thực hiện bởi nhóm 5 thành viên trong chương trình thực tập FCAJ. Phần đóng góp chính của tôi tập trung vào hạ tầng AWS, tích hợp API, triển khai, kiểm thử và tài liệu.

Hệ thống chuẩn hóa quy trình xử lý incident: phát hiện, phân loại, phân công, điều tra, lưu bằng chứng, báo cáo và hỗ trợ bằng AI. IRMS thay thế cách theo dõi rời rạc qua Email, Excel, Zalo hoặc tin nhắn bằng một web application tập trung. Final implementation sử dụng Groq với model `llama-3.1-8b-instant`, lưu API key trong AWS Secrets Manager và có rule-based fallback khi provider không khả dụng. Amazon Bedrock được giữ như provider tương lai thông qua cùng lớp abstraction.

### 2. Vấn đề cần giải quyết

Ở nhiều tổ chức vừa và nhỏ, các sự cố như S3 bucket public, đăng nhập bất thường hoặc phishing thường được xử lý qua các kênh không chính thức. Điều này gây khó khăn khi cần theo dõi người phụ trách, lưu bằng chứng, kiểm tra thay đổi trạng thái và tạo báo cáo sau sự cố.

IRMS giải quyết vấn đề này bằng cách đưa quy trình xử lý incident vào một hệ thống thống nhất trên AWS. Dự án ưu tiên serverless để giảm vận hành và kiểm soát chi phí trong môi trường demo.

### 3. Kiến trúc giải pháp

Hệ thống đi theo hướng Serverless-first và không dùng EC2, RDS hoặc NAT Gateway trong MVP để giảm chi phí và độ phức tạp vận hành.

![IRMS Solution Architecture](/Internship/images/2-Proposal/IRMS_architecture_v10.drawio.png)

#### Kiến trúc đã triển khai

- **Amazon CloudFront**: Phân phối frontend React và cung cấp truy cập HTTPS.
- **Amazon S3 (Frontend Hosting)**: Lưu static build output của React + Vite.
- **Amazon API Gateway (REST)**: Nhận request backend từ frontend.
- **Amazon Cognito**: Xác thực người dùng, phát hành JWT token và hỗ trợ phân quyền.
- **AWS Lambda**: Xử lý logic incident, evidence, report, alert và AI Assistant.
- **Amazon DynamoDB**: Lưu incidents, timeline, users và evidence metadata.
- **Amazon S3 (Evidence Store)**: Lưu evidence files và report artifacts.
- **Amazon EventBridge**: Route sự kiện và lịch chạy tự động.
- **Amazon SNS**: Gửi thông báo trong luồng alert.
- **AWS Secrets Manager**: Lưu Groq API key an toàn.
- **Amazon CloudWatch**: Lưu log và hỗ trợ troubleshooting.

#### Lớp AI provider

- **Provider hiện tại:** Groq.
- **Model:** `llama-3.1-8b-instant`.
- **Lưu secret:** AWS Secrets Manager.
- **Fallback:** Rule-based analysis khi provider không khả dụng hoặc timeout.
- **Future Enhancement:** Amazon Bedrock có thể được bật sau mà không đổi frontend API contract.

#### Future Enhancements

- **Amazon Route 53:** Custom domain và DNS management.
- **AWS WAF:** Lọc request bổ sung phía trước CloudFront.
- **Amazon Bedrock:** Provider AI native AWS khi yêu cầu dự án và quyền account cho phép.

### 4. Triển khai kỹ thuật

**Frontend:** React + Vite, tích hợp UI từ Stitch AI. Các màn hình chính gồm Dashboard, Incidents, Incident Detail, Evidence, Reports và AI Assistant Panel. Ứng dụng được build thành static assets, upload lên S3 và phân phối qua CloudFront.

**Backend:** Các Lambda Python đặt sau API Gateway REST. Cognito JWT authentication bảo vệ API. DynamoDB lưu dữ liệu vận hành, S3 lưu evidence và report artifacts.

**AI Assistant:** AI Lambda cung cấp `POST /ai/analyze`, `POST /ai/explain` và `POST /ai/chat`. Các endpoint này dùng provider abstraction. Final implementation gọi Groq và trả fallback response có cấu trúc nếu provider không khả dụng.

**Infrastructure as Code:** AWS SAM định nghĩa resource serverless và cấu hình deploy. Backend được kiểm tra bằng `sam validate`, build bằng `sam build` và deploy bằng `sam deploy`.

**Source Control:** GitHub được dùng để phối hợp nhóm, làm việc trên feature branch và review trước khi merge.

### 5. Timeline & Milestones

- **Tuần 1:** Thiết lập AWS account, IAM users, budget monitoring, SAM CLI, GitHub workflow, Cognito, API Gateway và test Lambda đầu tiên.
- **Tuần 2:** Hoàn thành Incident CRUD, access pattern DynamoDB, tích hợp frontend API và kiểm thử login-to-incident.
- **Tuần 3:** Hoàn thành evidence upload, report generation, EventBridge/SNS alert automation và kiểm thử deployment.
- **Tuần 4:** Hoàn thành CloudFront/S3 hosting, tích hợp AI Assistant với Groq, cấu hình Secrets Manager, test các API route có Cognito và end-to-end validation.
- **Giai đoạn cuối:** Rà soát workshop, đồng bộ tài liệu song ngữ, khảo sát Bedrock như provider tương lai, thay credential demo bằng placeholder và hoàn thiện báo cáo cuối.

### 6. Ước tính chi phí

Kiến trúc serverless không có compute server chạy liên tục. Chi phí chủ yếu đến từ request, storage, log và secret.

| Service | Yếu tố chi phí |
| --- | --- |
| AWS Lambda | Số lần gọi và thời gian chạy |
| API Gateway | Số REST API requests |
| DynamoDB | Read/write capacity và storage |
| Amazon S3 | Frontend/evidence storage và requests |
| CloudFront | Data transfer và requests |
| Cognito | Monthly active users |
| EventBridge | Events và scheduler |
| SNS | Notification requests |
| Secrets Manager | Số lượng secrets |
| CloudWatch | Logs và metrics |
| Groq | Development/demo usage theo free tier của provider |
| Amazon Bedrock | Chỉ phát sinh inference cost nếu bật trong tương lai |

Trong phạm vi internship demo, mức sử dụng AWS nhỏ. Nhóm vẫn kiểm tra Billing và Cost Explorer định kỳ, đồng thời dọn tài nguyên không dùng sau khi test.

### 7. Đánh giá rủi ro

**Rủi ro 1: AI provider bị giới hạn quota hoặc không khả dụng**

Giải pháp: Đặt AI phía sau provider abstraction. Provider hiện tại là Groq, API key lưu trong Secrets Manager và Lambda trả rule-based fallback nếu provider không khả dụng.

**Rủi ro 2: Chi phí AWS vượt mức dự kiến**

Giải pháp: Ưu tiên serverless, tránh EC2/RDS/NAT Gateway, theo dõi Billing/Cost Explorer và dọn tài nguyên test sau khi xác minh.

**Rủi ro 3: Conflict code khi 5 thành viên cùng làm**

Giải pháp: Dùng feature branch, giữ Lambda độc lập, thống nhất API contract sớm và review trước khi merge.

**Rủi ro 4: Thành viên bị chặn bởi dependency**

Giải pháp: Ưu tiên Cognito, API Gateway và API contract từ sớm. Frontend có thể dùng mock data trong lúc backend hoàn thiện endpoint.

**Rủi ro 5: Lộ demo credentials hoặc secrets trong tài liệu**

Giải pháp: Dùng placeholder trong documentation, lưu provider API key trong Secrets Manager và không publish password, access key, token hoặc presigned URL đầy đủ.

### 8. Kết quả kỳ vọng

Khi kết thúc dự án, nhóm có một hệ thống IRMS chạy trên AWS với các chức năng:

- Đăng nhập và phân quyền bằng Cognito.
- Tạo, xem, cập nhật, phân công và xóa incidents.
- Ghi timeline cho quá trình điều tra.
- Upload và quản lý evidence bằng S3 presigned URL.
- Tạo alert qua EventBridge/SNS automation.
- AI-assisted incident analysis, severity explanation và incident Q&A.
- Fallback response khi AI provider không khả dụng.
- Xuất report data và tài liệu hóa quy trình cleanup.
