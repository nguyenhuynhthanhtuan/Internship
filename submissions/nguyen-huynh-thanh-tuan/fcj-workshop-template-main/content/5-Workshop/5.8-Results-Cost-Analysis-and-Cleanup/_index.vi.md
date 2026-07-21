---
title: "Kết quả, chi phí và dọn dẹp"
date: 2026-07-17
weight: 8
chapter: false
pre: " <b> 5.8. </b> "
---

## 5.8. Kết quả, chi phí và dọn dẹp

#### Nội dung

- 5.8.13 Kết quả, chi phí & dọn dẹp

### 5.8.13 Results, Cost Estimation and Resource Cleanup

#### 5.8.13.1 Project Results
Sau khi hoàn thành triển khai, hệ thống đạt được các chức năng chính:
- ✅ User Authentication (Amazon Cognito)
- ✅ Incident Management
- Create
- Update
- Delete
- View
- ✅ Incident Timeline
- ✅ Evidence Upload
- ✅ AI Incident Analysis
- ✅ Report Generation
- ✅ Dashboard Monitoring
- ✅ HTTPS Deployment
- ✅ Infrastructure as Code
Toàn bộ hệ thống được triển khai theo kiến trúc Serverless trên AWS.

#### 5.8.13.2 AWS Services Used

| Service | Purpose |
| --- | --- |
| Amazon Cognito | Authentication |
| Amazon API Gateway | REST API |
| AWS Lambda | Business Logic |
| Amazon DynamoDB | Incident Database |
| Amazon S3 | Evidence + Frontend |
| Amazon CloudFront | CDN + HTTPS |
| Amazon EventBridge | Event Trigger |
| Amazon SNS | Notification |
| AWS SAM | Infrastructure as Code |
| AWS IAM | Permission Management |

#### 5.8.13.3 Phân tích chi phí

Trong quá trình phát triển và triển khai IRMS, tôi sử dụng AWS Billing & Cost Management để kiểm tra chi phí theo từng dịch vụ. Báo cáo billing cho thấy workload demo vẫn nằm gần như hoàn toàn trong Free Tier. Phần lớn dịch vụ có chi phí bằng **USD 0.00**.

Điểm cần lưu ý là **AWS Secrets Manager** có ghi nhận chi phí **USD 0.17** cho secret lưu API key, nhưng khoản này được bù bởi AWS Free Tier/Credit **(USD 0.17)**. Vì vậy, chi phí AWS thực tế sau credit vẫn là **USD 0.00**.

| Dịch vụ AWS | Mức sử dụng chính ghi nhận | Amount (USD) | Ghi chú |
| --- | --- | --- | --- |
| Amazon API Gateway | 956 requests | 0.00 | Nằm trong free tier request |
| AWS Lambda | 501 requests, 32.507 GB-seconds | 0.00 | Nằm trong free tier Lambda |
| Amazon DynamoDB | 677 read request units, 44 write request units | 0.00 | Pay-per-request, mức demo rất nhỏ |
| Amazon Cognito | 4 monthly active users | 0.00 | Tài khoản demo cho Admin, Manager, Analyst, Auditor |
| Amazon S3 | 250 PUT/COPY/POST/LIST, 1,612 GET/other requests, 0.131 GB-Mo storage | 0.00 | Frontend hosting và evidence storage |
| Amazon CloudFront | 187 HTTPS requests, 8 invalidation URLs, khoảng 0.003 GB data out | 0.00 | Nằm trong global free tier |
| Amazon CloudWatch | 0.122 metrics, 6 GetMetricData metrics, log usage trong free tier | 0.00 | Logs, metrics và kiểm tra vận hành |
| Amazon EventBridge | 2 64K chunks | 0.00 | Event routing / scheduler |
| Amazon SNS | 79 requests | 0.00 | Notification test |
| AWS CloudFormation | 279 resource handler operations | 0.00 | Triển khai stack bằng IaC |
| AWS KMS | 29 requests tổng ở các region | 0.00 | KMS free tier request |
| AWS X-Ray | 699 traces stored | 0.00 | Trace phục vụ kiểm thử |
| AWS Secrets Manager | 0.426 secret-month, 72 API requests | 0.17 | Được bù bởi AWS Free Tier/Credit |

Một số dòng như AWS Glue request xuất hiện trong billing ở nhiều region nhưng đều có amount **USD 0.00** và không phải cost driver chính của IRMS. Vì vậy, phần tổng kết chi phí tập trung vào các dịch vụ thật sự được dùng trong kiến trúc IRMS.

#### 5.8.13.4 Chi phí AI Service

Phiên bản cuối sử dụng Groq cho workload AI trong giai đoạn development/demo. API key được lưu trong AWS Secrets Manager và chỉ được Lambda đọc khi cần gọi AI provider. Frontend không chứa API key.

| Hạng mục | Giá trị |
| --- | --- |
| Provider hiện tại | Groq |
| Model | `llama-3.1-8b-instant` |
| Mô hình chi phí | Groq Free Tier cho development/demo |
| Dịch vụ AWS liên quan | AWS Secrets Manager |
| Gross cost của Secrets Manager | USD 0.17 |
| Khoản bù Free Tier/Credit | (USD 0.17) |
| Net cost của Secrets Manager | USD 0.00 |
| Provider tương lai | Amazon Bedrock |

Trong phạm vi dự án:

- Chỉ gửi số lượng nhỏ request cho incident analysis, explain và chat.
- Không huấn luyện model.
- Không xử lý dữ liệu dung lượng lớn.
- Nếu Groq không khả dụng, Lambda trả về rule-based fallback response.

Chi phí AI hiện tại không đến từ Groq vì dự án dùng free tier cho development/demo. Trên AWS, cost driver liên quan đến AI chủ yếu là Secrets Manager do cần lưu API key an toàn. Amazon Bedrock chưa được bật trong phiên bản cuối, nên chưa phát sinh inference charge.

#### 5.8.13.5 Tổng chi phí dự án

| Hạng mục | Gross Cost | Credit / Free Tier | Net Cost |
| --- | --- | --- | --- |
| AWS services trong Free Tier | USD 0.00 | USD 0.00 | USD 0.00 |
| AWS Secrets Manager | USD 0.17 | (USD 0.17) | USD 0.00 |
| Groq API | USD 0.00 | Free Tier | USD 0.00 |
| Amazon Bedrock | USD 0.00 | Chưa bật | USD 0.00 |
| **Tổng thực tế** | **USD 0.17** | **(USD 0.17)** | **USD 0.00** |

```text
Primary AWS usage       : Lambda, API Gateway, CloudFront, S3, DynamoDB, Cognito, CloudWatch
Small gross cost driver : Secrets Manager (USD 0.17 before credit)
Current AI provider     : Groq for development/demo usage
Future AI provider      : Amazon Bedrock only if enabled
Final net AWS cost      : USD 0.00 after Free Tier/Credit
```

Demo được giữ ở mức cost-aware bằng cách dùng serverless services, theo dõi billing sau khi deploy, kiểm tra các tài nguyên đang chạy và cleanup tài nguyên không còn cần thiết.

#### 5.8.13.6 Resource Cleanup
Sau khi hoàn thành workshop hoặc quá trình đánh giá, cần dọn dẹp tài nguyên để tránh phát sinh chi phí ngoài ý muốn.
Khuyến nghị thực hiện theo thứ tự sau:
- Xóa CloudFront Distribution cũ (nếu còn).
- Xóa CloudFormation Stack:
aws cloudformation delete-stack \
--stack-name irms-serverless \
--region ap-southeast-1 \
--profile irms-shared
- Kiểm tra và xóa các tài nguyên còn sót:
- Amazon S3 Frontend Bucket (sau khi sao lưu nếu cần)
- Amazon S3 Evidence Bucket (sau khi sao lưu dữ liệu)
- Lambda Functions (nếu không bị xóa cùng stack)
- API Gateway (nếu không bị xóa cùng stack)
- DynamoDB Tables (nếu không bị xóa cùng stack)
- SNS Topic
- EventBridge Rules
- IAM Roles do dự án tạo riêng
- Secrets Manager Secret chứa Groq API key
- Chỉ giữ lại Amazon Cognito User Pool nếu có kế hoạch tiếp tục phát triển hoặc tái sử dụng cho các phiên bản sau.
