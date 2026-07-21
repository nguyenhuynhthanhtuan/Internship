---
title: "Tuần 6"
date: 2026-05-21
weight: 6
chapter: false
pre: " <b> 1.6. </b> "
---

**Mốc thời gian:** 21/5 → 27/5 (5 ngày làm việc)

## Ngày 1 - 21/5: Tìm hiểu AWS Lambda

**Công việc đã thực hiện:** Tôi tìm hiểu AWS Lambda và cách mô hình điện toán không máy chủ (Serverless Computing) cho phép ứng dụng thực thi mã mà không cần quản lý máy chủ. Trong quá trình thực hành, tôi nghiên cứu Lambda Function, môi trường thực thi (Execution Environment), Trigger, IAM Role, Environment Variables và vòng đời hoạt động của Lambda. Tôi cũng thực hành tạo các Lambda Function đơn giản và tìm hiểu cách Lambda tự động xử lý yêu cầu khi được kích hoạt bởi các dịch vụ AWS khác.

**Kiến thức đã học:** Tôi hiểu rằng AWS Lambda giúp nhà phát triển tập trung vào việc xây dựng logic của ứng dụng, trong khi AWS tự động quản lý hạ tầng, khả năng mở rộng và việc vận hành máy chủ.

**Kết quả đạt được:** Tôi tạo và kiểm thử thành công các Lambda Function, đồng thời hiểu được quy trình triển khai và thực thi các hàm Serverless trên nền tảng AWS.

**Khó khăn và bài học:** Lambda có giới hạn về thời gian thực thi và tài nguyên sử dụng, vì vậy cần thiết kế logic ứng dụng hợp lý để tối ưu hiệu năng và tránh phát sinh chi phí không cần thiết.

## Ngày 2 - 22/5: Tìm hiểu Amazon API Gateway

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon API Gateway và cách dịch vụ này cung cấp các RESTful API cho ứng dụng Serverless. Trong quá trình thực hành, tôi nghiên cứu API Resources, Methods, Stages, cơ chế xử lý Request và Response cũng như cách tích hợp với AWS Lambda. Tôi cũng tìm hiểu cách API Gateway định tuyến các yêu cầu từ người dùng đến Lambda Function và hỗ trợ quản lý API một cách an toàn.


**Kiến thức đã học:** Tôi hiểu rằng API Gateway đóng vai trò là điểm truy cập chính của các ứng dụng Serverless bằng cách quản lý các yêu cầu API, định tuyến lưu lượng truy cập và tích hợp với các dịch vụ AWS.


**Kết quả đạt được:** Tôi hiểu được quy trình xây dựng API bằng Amazon API Gateway và cách tích hợp API với AWS Lambda.

**Khó khăn và bài học:** Mọi thay đổi cấu hình trong API Gateway cần được triển khai (Deploy) lên một Stage trước khi có thể được người dùng hoặc ứng dụng sử dụng.

## Ngày 3 - 25/5: Tìm hiểu Amazon SNS và Amazon SQS


**Công việc đã thực hiện:** Tôi tìm hiểu Amazon Simple Notification Service (SNS) và Amazon Simple Queue Service (SQS). Trong quá trình học, tôi nghiên cứu mô hình truyền thông điệp Publish/Subscribe của SNS và cơ chế hàng đợi tin nhắn (Message Queue) của SQS. Tôi cũng so sánh mục đích sử dụng của hai dịch vụ và tìm hiểu các tình huống thực tế cần gửi thông báo hoặc xử lý thông điệp bất đồng bộ.

**Kiến thức đã học:** Tôi hiểu rằng Amazon SNS được thiết kế để phát thông báo đến nhiều đối tượng nhận, trong khi Amazon SQS hỗ trợ truyền tải thông điệp bất đồng bộ một cách đáng tin cậy giữa các thành phần của hệ thống.

**Kết quả đạt được:** Tôi hiểu được cách Amazon SNS và Amazon SQS giúp tăng khả năng mở rộng, độ tin cậy và giảm sự phụ thuộc giữa các thành phần trong kiến trúc Serverless.

**Khó khăn và bài học:** Việc lựa chọn SNS hay SQS phụ thuộc vào yêu cầu giao tiếp của hệ thống, vì mỗi dịch vụ được thiết kế cho các mô hình truyền thông điệp khác nhau.

## Ngày 4  - 26/5: Tìm hiểu Amazon EventBridge


**Công việc đã thực hiện:** Tôi tìm hiểu Amazon EventBridge và cách kiến trúc hướng sự kiện (Event-Driven Architecture) hoạt động trên nền tảng AWS. Trong quá trình thực hành, tôi nghiên cứu Event Bus, Rules, Event Pattern và Scheduled Events. Tôi cũng tìm hiểu cách EventBridge tích hợp với AWS Lambda và các dịch vụ AWS khác để tự động hóa các quy trình xử lý dựa trên sự kiện phát sinh trong hệ thống.

**Kiến thức đã học:** Tôi hiểu rằng Amazon EventBridge cho phép các ứng dụng tự động phản hồi khi có sự kiện xảy ra, giúp giảm thiểu thao tác thủ công và nâng cao khả năng tự động hóa của hệ thống.

**Kết quả đạt được:** Tôi hiểu được quy trình cơ bản để tạo Event Rule và kết nối các dịch vụ AWS thông qua mô hình xử lý hướng sự kiện.

**Khó khăn và bài học:** Các Event Rule cần được cấu hình cẩn thận để đảm bảo chỉ những sự kiện mong muốn mới kích hoạt quy trình xử lý của ứng dụng.

## Ngày 5 - 27/5: Tổng kết kiến trúc Serverless

**Công việc đã thực hiện:** Tôi tổng hợp lại các dịch vụ AWS đã học trong tuần, bao gồm AWS Lambda, Amazon API Gateway, Amazon SNS, Amazon SQS và Amazon EventBridge. Tôi ghi lại cách các dịch vụ này phối hợp với nhau để xây dựng các ứng dụng Serverless có khả năng xử lý yêu cầu, trao đổi thông điệp và tự động phản hồi theo các sự kiện phát sinh. Đồng thời, tôi cũng tổng hợp các mô hình triển khai phổ biến và những Best Practices trong việc thiết kế kiến trúc Serverless có khả năng mở rộng.

**Kiến thức đã học:** Tôi nhận thấy rằng các ứng dụng Serverless được xây dựng thông qua sự kết hợp của nhiều dịch vụ được AWS quản lý, giúp nhà phát triển xây dựng các hệ thống có khả năng mở rộng và xử lý sự kiện mà không cần quản lý hạ tầng máy chủ.

**Kết quả đạt được:** Tôi củng cố được kiến thức về các dịch vụ Serverless trên AWS và hoàn thiện bộ ghi chú phục vụ cho các bài thực hành cũng như quá trình phát triển dự án trong những tuần tiếp theo.

**Khó khăn và bài học:** Việc xây dựng kiến trúc Serverless đòi hỏi phải hiểu rõ cách các dịch vụ AWS giao tiếp và phối hợp với nhau. Tích hợp các dịch vụ một cách hợp lý là yếu tố quan trọng để đảm bảo hệ thống hoạt động ổn định, có khả năng mở rộng và xử lý sự kiện hiệu quả.
