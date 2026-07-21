---
title: "Demo và kiểm thử dự án"
date: 2026-07-17
weight: 7
chapter: false
pre: " <b> 5.7. </b> "
---

## 5.7. Demo và kiểm thử dự án

#### Nội dung

- 5.7.1 Thông tin truy cập demo
- 5.7.2 Vai trò trong dự án
- 5.7.3 Kiểm tra đăng nhập và dashboard
- 5.7.4 Kiểm thử luồng Incident
- 5.7.5 Upload Evidence và xác minh dữ liệu
- 5.7.6 Phân tích Incident bằng AI
- 5.7.7 Kiểm thử AI Chat

Phần này nằm sau bước triển khai web. Mục tiêu là kiểm tra hệ thống IRMS đã deploy có thể được sử dụng trực tiếp bởi Security Analyst trên trình duyệt, không chỉ kiểm thử bằng lệnh backend.

### 5.7.1 Thông tin truy cập demo

Sử dụng URL frontend đã deploy qua CloudFront ở phần trước.

| Mục | Giá trị |
| --- | --- |
| Link dự án | [https://d1cdyvxfzvnsxg.cloudfront.net/](https://d1cdyvxfzvnsxg.cloudfront.net/) |
| Luồng test chính | Security Analyst |

Các tài khoản demo có thể dùng để kiểm thử. Luồng hướng dẫn chính sử dụng tài khoản Analyst; nếu muốn test thêm các vai trò khác, vui lòng liên hệ để được cấp thông tin đăng nhập:

| Vai trò | Tài khoản | Mật khẩu |
| --- | --- | --- |
| Admin | `admin@irms-demo.com` | Liên hệ để được cấp khi cần test |
| Manager | `manager@irms-demo.com` | Liên hệ để được cấp khi cần test |
| Analyst | `analyst@irms-demo.com` | Liên hệ để được cấp khi cần test |
| Auditor | `auditor@irms-demo.com` | Liên hệ để được cấp khi cần test |

### 5.7.2 Vai trò trong dự án

| Vai trò | Trách nhiệm chính | Dùng trong phần test |
| --- | --- | --- |
| Security Analyst | Đăng nhập, tạo incident, cập nhật timeline, upload evidence và dùng AI Assistant | Có |
| Incident Manager | Theo dõi status, severity, assignee và report output | Tùy chọn |
| AWS Infrastructure / Backend | Duy trì Cognito, API Gateway, Lambda, DynamoDB, S3, EventBridge, SNS, CloudFront và Secrets Manager | Hỗ trợ xác minh |
| AI Assistant | Đưa ra phân tích tư vấn và hỗ trợ hỏi đáp bảo mật | Có |
| Report / Alert Handler | Tạo report và hỗ trợ notification theo event | Hỗ trợ xác minh |

### 5.7.3 Kiểm tra đăng nhập và dashboard

Mở link dự án trên trình duyệt và đăng nhập bằng tài khoản Analyst demo ở bảng trên.

![Màn hình đăng nhập IRMS](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-06.png)

Sau khi đăng nhập, kiểm tra dashboard hiển thị các thẻ trạng thái incident, recent incidents và security posture.

![Dashboard IRMS](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-07.png)

Kết quả mong đợi:

- Analyst được xác thực bằng Amazon Cognito.
- Frontend nhận session hợp lệ và gọi được các route API Gateway có bảo vệ.
- Dashboard hiển thị dữ liệu thật thay vì màn hình mock.

### 5.7.4 Kiểm thử luồng Incident

Tạo một incident mới từ trang Incidents.

![Tạo incident mới](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-09.png)

Submit form và kiểm tra incident vừa tạo xuất hiện trong danh sách.

![Tạo incident thành công](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-10.png)

Mở trang chi tiết incident và kiểm tra status, severity, assignee, timeline và evidence panel.

![Chi tiết incident](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-11.png)

Thêm một timeline entry cho giai đoạn investigating hoặc containment.

![Thêm timeline entry](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-12.png)

![Timeline entry đã lưu](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-13.png)

Kết quả mong đợi:

- Incident được tạo thông qua API có Cognito bảo vệ.
- Bản ghi incident được lưu vào DynamoDB.
- Mỗi thay đổi trạng thái hoặc ghi chú xử lý được ghi lại trong timeline.

### 5.7.5 Upload Evidence và xác minh dữ liệu

Chọn một file evidence local và gắn với incident đang xử lý.

![Form upload evidence](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-14.png)

Frontend tự nhận diện tên file, MIME type và dung lượng file trước khi upload.

![Thông tin evidence](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-15.png)

Nhấn submit evidence. Browser upload file trực tiếp lên Amazon S3 bằng presigned PUT URL có thời hạn ngắn.

![Submit evidence](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-16.png)

Xác minh object vừa upload trong private S3 evidence bucket.

![Evidence object trong S3](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-17.png)

Xác minh metadata tương ứng trong DynamoDB.

![Evidence metadata trong DynamoDB](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-18.png)

Kết quả mong đợi:

- Cognito token chỉ được gửi đến API Gateway.
- Request upload lên S3 không chứa Cognito token.
- File evidence gốc được lưu trong S3.
- Metadata của evidence được lưu riêng trong DynamoDB.

### 5.7.6 Phân tích Incident bằng AI

Mở trang AI Assistant và chọn chế độ **AI Analyst**.

![Trang AI Assistant](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-19.png)

Chọn incident vừa tạo để phân tích.

![Chọn incident cho AI analysis](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-20.png)

Xem kết quả phân tích có cấu trúc.

![Kết quả AI Analyst](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-21.png)

Kết quả AI tóm tắt impact, mức ưu tiên phản hồi, đề xuất xử lý và ngữ cảnh hỗ trợ.

![AI findings](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-22.png)

Điểm cần xác minh:

- AI chỉ đóng vai trò tư vấn.
- Status, severity và assignee của incident không tự thay đổi nếu người dùng có quyền chưa chỉnh sửa.
- API key của AI provider được Lambda đọc từ Secrets Manager, không xuất hiện ở frontend.

### 5.7.7 Kiểm thử AI Chat

Chuyển sang chế độ **AI Chat**.

![Chế độ AI Chat](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-24.png)

Nhập một prompt liên quan đến incident đã chọn, ví dụ:

```text
Explain why this incident has its current severity.
```

![Phản hồi AI Chat](/Internship/images/5-Workshop/IRMS-demo-test/demo-test-25.png)

Kết quả mong đợi:

- AI Chat trả lời câu hỏi của analyst dựa trên incident context.
- Chat chỉ truy xuất các incident mà Security Analyst đã xác thực được phép xem.
- Phản hồi AI được dùng làm hướng dẫn, không tự động thay đổi dữ liệu incident.

