---
title: "Tuần 9"
date: 2026-06-11
weight: 9
chapter: false
pre: " <b> 1.9. </b> "
---

**Mốc thời gian:** 11/6 → 17/6 (5 ngày làm việc)


## Ngày 1 - 11/6: Giới thiệu dự án và phân tích yêu cầu hệ thống

**Công việc đã thực hiện:** Tôi bắt đầu thực hiện dự án nhóm với tên **Incident Response Management System (IRMS) trên nền tảng AWS Serverless**. Trong giai đoạn đầu, tôi tham gia trao đổi với các thành viên trong nhóm để phân tích yêu cầu hệ thống, xác định mục tiêu chính và xây dựng các chức năng cốt lõi của ứng dụng. Nhóm tập trung tìm hiểu quy trình quản lý sự cố, bao gồm tạo sự cố, theo dõi tiến trình xử lý, quản lý bằng chứng, cập nhật trạng thái và tạo báo cáo.


**Kiến thức đã học:**Tôi hiểu rằng phân tích yêu cầu là bước quan trọng trước khi triển khai hệ thống vì giúp xác định phạm vi, các chức năng cần thiết và giải pháp kỹ thuật phù hợp.

**Kết quả đạt được:** Tôi có danh sách chức năng cốt lõi để thảo luận với nhóm khi bắt đầu dự án.

**Khó khăn và bài học:** Nếu chỉ nghĩ theo service kỹ thuật mà bỏ qua workflow, hệ thống dễ thiếu chức năng quan trọng.

## Ngày 2 - 12/6: Thiết kế kiến trúc hệ thống

**Công việc đã thực hiện:** Tôi tham gia thiết kế kiến trúc tổng thể của hệ thống IRMS dựa trên mô hình AWS Serverless. Nhóm đã thảo luận cách kết hợp các dịch vụ AWS như Amazon Cognito, API Gateway, AWS Lambda, DynamoDB, Amazon S3, CloudWatch, EventBridge và SNS để xây dựng một hệ thống có khả năng mở rộng và đảm bảo tính bảo mật. Tôi cũng tìm hiểu vai trò của từng dịch vụ trong kiến trúc và cách dữ liệu được luân chuyển giữa các thành phần trong hệ thống.


**Kiến thức đã học:** Tôi hiểu cách nhiều dịch vụ AWS Managed Service có thể được tích hợp với nhau để xây dựng một ứng dụng Serverless hoàn chỉnh mà không cần quản lý máy chủ truyền thống.

**Kết quả đạt được:** Tôi có bản nháp để mang vào buổi thảo luận nhóm về DynamoDB.

**Khó khăn và bài học:** Cần tránh thiết kế quá phức tạp khi chưa rõ phạm vi demo.

## Ngày 3 - 15/6: Thiết kế luồng dữ liệu và quy trình hoạt động của hệ thống


**Công việc đã thực hiện:** Tôi phối hợp cùng nhóm để xây dựng sơ đồ luồng dữ liệu và luồng xử lý yêu cầu của hệ thống IRMS. Tôi phân tích cách dữ liệu di chuyển từ ứng dụng Frontend đến API Gateway, đi qua các hàm Lambda và tương tác với DynamoDB, S3. Ngoài ra, tôi cũng xem xét các quy trình chính như tạo sự cố, cập nhật trạng thái sự cố, tải lên tệp bằng chứng và truy xuất báo cáo.

**Kiến thức đã học:** Tôi hiểu rằng sơ đồ luồng dữ liệu giúp mô tả trực quan cách các thành phần trong hệ thống giao tiếp với nhau, từ đó hỗ trợ quá trình triển khai và kiểm thử dễ dàng hơn.

**Kết quả đạt được:** Tôi có bản nháp endpoint để thảo luận và điều chỉnh cùng nhóm.

**Khó khăn và bài học:** Không nên tạo quá nhiều route vượt phạm vi demo vì sẽ làm testing và documentation nặng hơn.

## Ngày 4 - 16/6: Phân chia nhiệm vụ và lựa chọn công nghệ

**Công việc đã thực hiện:** Tôi tham gia phân chia nhiệm vụ cho các thành viên dựa trên yêu cầu dự án và khả năng của từng người. Nhóm đã trao đổi về các phần công việc như phát triển Backend, tích hợp Frontend, thiết kế cơ sở dữ liệu, cấu hình bảo mật và triển khai hệ thống. Đồng thời, nhóm cũng thống nhất lựa chọn các dịch vụ AWS phù hợp cho từng thành phần dựa trên các tiêu chí về hiệu năng, bảo mật và chi phí.


**Kiến thức đã học:**  Tôi hiểu rằng việc phân chia nhiệm vụ hiệu quả giúp các thành viên có thể phát triển từng phần riêng biệt nhưng vẫn đảm bảo khả năng tích hợp giữa các thành phần trong hệ thống.


**Kết quả đạt được:** Tôi có hướng sơ bộ cho phần report generation và alert automation.

**Khó khăn và bài học:** Cần phân biệt rõ phần sẽ triển khai thật và phần chỉ là đề xuất mở rộng.

## Ngày 5 - 17/6: Lập kế hoạch dự án và chuẩn bị triển khai

**Công việc đã thực hiện:** Tôi gom toàn bộ ghi chú từ các tuần trước thành checklist trước khi vào dự án: authentication, API Gateway, Lambda handler, DynamoDB access pattern, S3 evidence, EventBridge/SNS, CloudWatch log, frontend integration và deployment. 

Tôi cùng nhóm xem xét kế hoạch phát triển dự án và chuẩn bị các nội dung cần thiết trước khi bắt đầu giai đoạn triển khai. Tôi tổng hợp các dịch vụ AWS được lựa chọn, các thành phần trong kiến trúc, quy trình phát triển và các bước thực hiện dự kiến. Ngoài ra, tôi cũng chuẩn bị các ghi chú kỹ thuật liên quan đến xác thực người dùng, phát triển API, thao tác cơ sở dữ liệu, quản lý lưu trữ và giám sát hệ thống.

**Kiến thức đã học:** Tôi nhận thấy việc chuẩn bị kỹ lưỡng trước khi triển khai giúp giảm thiểu lỗi kỹ thuật và giúp quá trình phát triển hệ thống được tổ chức khoa học hơn.

**Kết quả đạt được:** Tôi có checklist kỹ thuật và câu hỏi để dùng trong buổi bắt đầu IRMS ở tuần 11.

**Khó khăn và bài học:** Không nên ghi trong Worklog rằng dự án đã làm từ tuần này; đây chỉ là nghiên cứu và chuẩn bị.
