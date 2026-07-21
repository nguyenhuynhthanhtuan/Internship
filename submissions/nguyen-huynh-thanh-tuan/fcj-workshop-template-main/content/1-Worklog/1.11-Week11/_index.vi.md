---
title: "Tuần 11"
date: 2026-06-25
weight: 11
chapter: false
pre: " <b> 1.11. </b> "
---

**Mốc thời gian:** 25/6 → 1/7 (5 ngày làm việc)

> Dự án IRMS được thực hiện bởi nhóm 5 thành viên. Các ghi chú dưới đây tập trung vào phần đóng góp cá nhân của tôi trong quá trình phối hợp với nhóm.

## - 25/6: Kiểm thử chức năng các tính năng của IRMS

**Công việc đã thực hiện:** Tôi tham gia kiểm thử các chức năng chính của hệ thống IRMS sau giai đoạn phát triển ban đầu. Tôi kiểm tra các luồng nghiệp vụ quan trọng bao gồm xác thực người dùng, tạo sự cố, cập nhật trạng thái sự cố, theo dõi lịch sử xử lý, tải lên bằng chứng và tạo báo cáo. Tôi xác nhận rằng các yêu cầu từ frontend được xử lý chính xác thông qua API Gateway và Lambda trước khi lưu trữ hoặc truy xuất dữ liệu từ DynamoDB và S3.

Tôi rà soát lại các yêu cầu chính của hệ thống gồm quản lý sự cố, theo dõi timeline, tải lên bằng chứng, tạo báo cáo và xử lý cảnh báo. Sau đó, tôi liệt kê các dịch vụ AWS cần sử dụng cho dự án bao gồm Cognito, API Gateway, Lambda, DynamoDB, S3 Evidence Store, EventBridge, SNS, Secrets Manager, CloudWatch và CloudFront. Tôi cũng phân chia rõ các phần công việc mà tôi phụ trách trực tiếp và các phần cần phối hợp với các thành viên frontend, backend trong nhóm.

**Kiến thức đã học:** Tôi hiểu rằng kiểm thử chức năng là bước quan trọng nhằm đảm bảo mỗi tính năng của hệ thống hoạt động đúng theo yêu cầu ban đầu và các dịch vụ AWS có thể giao tiếp chính xác với nhau.

**Kết quả đạt được:** Nhóm có hướng triển khai rõ hơn, còn tôi có checklist AWS infrastructure và documentation để theo dõi trong các ngày tiếp theo.

**Khó khăn và bài học:** Ban đầu có nhiều ý tưởng mở rộng, nhưng cần ưu tiên phần cốt lõi để kịp tiến độ thực tập.

## Ngày 2 - 26/6: Kiểm thử phân quyền và bảo mật

**Công việc đã thực hiện:** Tôi tập trung kiểm thử cơ chế xác thực và phân quyền trong hệ thống IRMS. Tôi kiểm tra quá trình xác thực người dùng thông qua Cognito, kiểm tra JWT Token, cơ chế Authorizer của API Gateway và quyền IAM được cấp cho các tài nguyên AWS. Tôi thực hiện kiểm thử với nhiều trường hợp khác nhau như người dùng hợp lệ, token hết hạn và các yêu cầu không có quyền truy cập nhằm đảm bảo các API được bảo vệ không thể bị truy cập trái phép.

Tôi hỗ trợ mô tả luồng dữ liệu cho các chức năng Incident CRUD, Timeline, Evidence Upload và Report Generation. Với mỗi luồng, tôi ghi chú các thông tin đầu vào, đầu ra, các dịch vụ AWS liên quan và các điểm cần kiểm thử. Tôi cũng trao đổi với thành viên frontend để thống nhất endpoint, định dạng dữ liệu trả về và cách frontend gửi JWT trong phần Authorization Header.

**Kiến thức đã học:** Tôi hiểu rằng Authentication giúp xác minh danh tính người dùng, trong khi Authorization kiểm soát quyền truy cập vào tài nguyên và chức năng cụ thể. Cả hai cơ chế đều đóng vai trò quan trọng trong việc bảo vệ ứng dụng Serverless.

**Kết quả đạt được:** Các nhóm việc được tách rõ hơn, giúp tôi chuẩn bị template SAM và tài liệu workshop sát với luồng triển khai thật.

**Khó khăn và bài học:** Một số yêu cầu ban đầu còn chung chung, nên cần hỏi lại và ghi thành checklist cụ thể thay vì hiểu theo cảm tính.

## Ngày 3 - 29/6: Kiểm thử hiệu năng và tối ưu hệ thống

**Công việc đã thực hiện:** Tôi đánh giá hiệu năng của hệ thống thông qua việc theo dõi Lambda Execution Logs, thời gian phản hồi của API, thao tác trên DynamoDB và các chỉ số từ CloudWatch. Tôi phân tích các điểm có khả năng gây ảnh hưởng đến hiệu suất và xem xét các phương pháp tối ưu như cải thiện logic xử lý trong Lambda, giảm các thao tác database không cần thiết và sử dụng Access Pattern phù hợp trong DynamoDB.

Tôi kiểm tra các điểm kết nối giữa GuardDuty, EventBridge, Lambda Alert Handler và SNS để đảm bảo phần xử lý cảnh báo được mô tả chính xác trong tài liệu. Đối với AI Assistant, tôi ghi chú rõ rằng Lambda sẽ lấy thông tin bí mật từ Secrets Manager và gọi Groq API, tránh việc lưu API Key trong frontend hoặc tài liệu công khai. Tôi cũng cập nhật lại ghi chú để phân biệt giữa kiến trúc đã triển khai và các tính năng mở rộng trong tương lai.

**Kiến thức đã học:**Hiệu năng của hệ thống Serverless phụ thuộc vào cách thiết kế và tích hợp các dịch vụ với nhau, không chỉ phụ thuộc vào cấu hình riêng lẻ của từng dịch vụ.

**Kết quả đạt được:** Kiến trúc IRMS được chốt để dùng thống nhất trong Proposal, Workshop và Worklog.

**Khó khăn và bài học:** Nếu không thống nhất tên dịch vụ và hướng mũi tên từ đầu, các trang tài liệu sau đó rất dễ bị mâu thuẫn.

## Ngày 4 - 30/6: Sửa lỗi và cải thiện giao diện

**Công việc đã thực hiện:** Tôi phối hợp với các thành viên trong nhóm để xử lý các lỗi được phát hiện trong quá trình kiểm thử. Các công việc chính bao gồm điều chỉnh định dạng phản hồi API, xử lý các trạng thái lỗi trên frontend, cải thiện thông báo validation và điều chỉnh giao diện nhằm nâng cao trải nghiệm người dùng. Tôi cũng kiểm tra sự kết nối giữa các thành phần frontend và backend để đảm bảo dữ liệu được trao đổi chính xác.

Tôi thực hiện kiểm tra AWS SAM Template bằng lệnh sam validate, rà soát IAM Role của Lambda và kiểm tra các biến môi trường như tên bảng DynamoDB, tên bucket S3, tên secret và AI Provider. Tôi cũng ghi lại các bước cần đưa vào Workshop như cài đặt AWS CLI/SAM CLI, cấu hình profile, build, validate và deploy.

```bash
sam validate
sam build
sam deploy --guided
```

**Kiến thức đã học:** Một ứng dụng ổn định không chỉ cần backend xử lý chính xác mà còn cần giao diện rõ ràng, xử lý lỗi tốt và mang lại trải nghiệm sử dụng hợp lý.

**Kết quả đạt được:** Có nền tảng hạ tầng ban đầu để nhóm tiếp tục triển khai Lambda và kiểm thử API.

**Khó khăn và bài học:** Lỗi nhỏ trong template hoặc quyền IAM có thể làm deploy thất bại, nên cần validate sớm thay vì chờ đến cuối.

## Ngày 5 - 01/7: Hoàn thiện tài liệu kỹ thuật và đánh giá hệ thống

**Công việc đã thực hiện:** Tôi cập nhật tài liệu kỹ thuật cho dự án IRMS bao gồm mô tả kiến trúc hệ thống, cấu hình các dịch vụ AWS, kịch bản kiểm thử và hướng dẫn triển khai. Tôi đánh giá khả năng vận hành của hệ thống thông qua nhiều trường hợp kiểm thử như xác thực người dùng, quản lý sự cố, lưu trữ bằng chứng, gửi thông báo và giám sát hệ thống.

Tôi cũng ghi nhận các nội dung cần tiếp tục kiểm tra trong tuần tiếp theo như Cognito JWT, API Gateway CORS, Lambda Logs, thiết kế khóa DynamoDB, S3 Presigned URLs và triển khai CloudFront. Các nội dung chưa được kiểm chứng đầy đủ được đánh dấu để xác nhận thay vì ghi nhận là đã hoàn thành.

**Kiến thức đã học:** Tài liệu kỹ thuật hoàn chỉnh là một phần quan trọng trong quá trình phát triển phần mềm, giúp giải thích kiến trúc hệ thống, quy trình vận hành và hỗ trợ bảo trì trong tương lai.

**Kết quả đạt được:** Worklog, Proposal và Workshop bắt đầu đi theo cùng một timeline dự án từ tuần có ngày 01/07.

**Khó khăn và bài học:** Cần viết theo đóng góp cá nhân trong nhóm 5 người, không trình bày như một mình hoàn thành toàn bộ hệ thống.
