---
title: "Tuần 10"
date: 2026-06-18
weight: 10
chapter: false
pre: " <b> 1.10. </b> "
---

**Mốc thời gian:** 18/6 → 24/6 (5 ngày làm việc)

## Ngày 1 - 18/6: Chuẩn bị chọn đề tài

**Công việc đã thực hiện:** Tôi bắt đầu triển khai các chức năng backend của hệ thống Incident Response Management System (IRMS) bằng AWS Lambda. Tôi xây dựng và kiểm thử các hàm Lambda chịu trách nhiệm xử lý yêu cầu từ hệ thống và thực hiện các nghiệp vụ của ứng dụng. Tôi cũng tìm hiểu lại luồng thực thi của Lambda, cấu hình function, quyền IAM Role, biến môi trường và cách Lambda tích hợp với các dịch vụ AWS khác.

**Kiến thức đã học:** Tôi hiểu được cách AWS Lambda có thể xử lý logic backend trong môi trường Serverless mà không cần quản lý máy chủ truyền thống. Bên cạnh đó, tôi nhận thấy việc cấu hình đúng Execution Role và quyền truy cập là yếu tố quan trọng để Lambda có thể sử dụng được các tài nguyên cần thiết.
**Kết quả đạt được:** Tôi có tiêu chí chọn đề tài rõ hơn để trao đổi với nhóm.

**Khó khăn và bài học:** Cần chọn phạm vi vừa sức, không quá rộng so với thời gian còn lại.

## Ngày 2 - 19/6: Xây dựng API bằng Amazon API Gateway

**Công việc đã thực hiện:** Tôi phát triển các API Endpoint cho hệ thống IRMS bằng Amazon API Gateway. Tôi cấu hình các API Route, HTTP Method, xử lý request và tích hợp API Gateway với các hàm Lambda tương ứng. Các API tập trung vào những chức năng quản lý sự cố như tạo Incident mới, lấy thông tin sự cố và cập nhật trạng thái xử lý.

**Kiến thức đã học:** Tôi hiểu rằng API Gateway đóng vai trò là lớp giao tiếp giữa ứng dụng frontend và các hàm backend Serverless, chịu trách nhiệm tiếp nhận yêu cầu từ người dùng và chuyển tiếp đến Lambda để xử lý.

**Kết quả đạt được:** Tôi có bản service mapping ban đầu dựa trên kiến thức đã học.

**Khó khăn và bài học:** Sơ đồ nháp cần được cập nhật sau khi nhóm chốt yêu cầu thực tế.

## Ngày 3 - 22/6: Tích hợp DynamoDB trong quản lý dữ liệu Incident

**Công việc đã thực hiện:** Tôi tích hợp Amazon DynamoDB vào backend của hệ thống IRMS để lưu trữ và quản lý dữ liệu liên quan đến Incident. Tôi thiết kế các thao tác cơ bản như tạo mới, truy vấn và cập nhật thông tin sự cố. Ngoài ra, tôi nghiên cứu về Partition Key, cấu trúc Item và Access Pattern nhằm đảm bảo việc truy xuất dữ liệu được thực hiện hiệu quả.
## Ngày 4 - 25/6: Lên cấu trúc workshop

**Công việc đã thực hiện:** Tôi dự kiến cách chia workshop thành các phần: giới thiệu, chuẩn bị môi trường, cấu hình hạ tầng, phát triển ứng dụng, triển khai/kiểm thử, frontend integration và cleanup. Tôi cũng ghi chú rằng sidebar cần đánh số rõ ràng và các mục con phải xuất hiện đúng theo cấu trúc Hugo. Đây là bước chuẩn bị để khi dự án bắt đầu, tài liệu không bị rối.

**Kiến thức đã học:** Tôi hiểu được cách DynamoDB hỗ trợ các ứng dụng Serverless thông qua cơ sở dữ liệu NoSQL có khả năng mở rộng cao, độ trễ thấp và cấu trúc dữ liệu linh hoạt.

**Kết quả đạt được:** Tôi có cấu trúc ban đầu cho phần Workshop của báo cáo.

**Khó khăn và bài học:** Nếu numbering không thống nhất từ đầu, về sau sửa sidebar và internal link sẽ mất nhiều thời gian.

## Ngày 5 - Tích hợp SNS và đánh giá hệ thống

**Công việc đã thực hiện:** TTôi tích hợp Amazon SNS vào kiến trúc IRMS nhằm hỗ trợ xử lý thông báo khi xảy ra các sự kiện quan trọng trong hệ thống. Tôi tìm hiểu cách Lambda có thể gửi message đến SNS Topic và cách thông báo được phân phối đến người dùng hoặc quản trị viên đã đăng ký nhận thông báo. Tôi cũng rà soát lại sự kết hợp giữa Lambda, API Gateway, DynamoDB, SNS, IAM và CloudWatch để đảm bảo luồng xử lý Serverless hoạt động đúng theo thiết kế.

**Kiến thức đã học:** Tôi hiểu rằng các dịch vụ hướng sự kiện như SNS giúp tách biệt phần xử lý nghiệp vụ của ứng dụng với quá trình gửi thông báo, từ đó tăng khả năng mở rộng và tính linh hoạt của hệ thống.

**Kết quả đạt được:** Tôi đã hoàn thành việc tích hợp các dịch vụ AWS chính trong giai đoạn đầu phát triển backend của hệ thống IRMS và chuẩn bị nền tảng cho các bước phát triển tiếp theo.

**Khó khăn và bài học:** Cần ghi Worklog trung thực: các tuần trước là học và chuẩn bị, còn triển khai IRMS bắt đầu từ tuần có ngày 01/07.
