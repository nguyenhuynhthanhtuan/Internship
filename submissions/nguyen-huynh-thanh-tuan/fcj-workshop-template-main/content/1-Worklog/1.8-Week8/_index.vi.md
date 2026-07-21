---
title: "Tuần 8"
date: 2026-06-04
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

**Mốc thời gian:**  4/6 → 10/6 (5 ngày làm việc)

## Ngày 1 - 4/6: AWS WAF và bảo vệ ứng dụng web

**Công việc đã thực hiện:** Tôi tìm hiểu AWS WAF (Web Application Firewall) và cách dịch vụ này bảo vệ các ứng dụng web trước những hình thức tấn công phổ biến trên Internet. Trong quá trình học, tôi nghiên cứu về Web ACL, Managed Rules, Custom Rules, IP Filtering và Rate-based Rules. Tôi cũng tìm hiểu cách AWS WAF kết hợp với các dịch vụ như CloudFront và Application Load Balancer để lọc các HTTP request độc hại trước khi chúng truy cập đến ứng dụng.

**Kiến thức đã học:**Tôi hiểu rằng AWS WAF cung cấp một lớp bảo mật bổ sung ở tầng ứng dụng bằng cách giám sát và kiểm soát lưu lượng truy cập dựa trên các luật bảo mật được cấu hình trước.

**Kết quả đạt được:** Tôi hiểu hơn cách React app được build thành static files để host trên S3/CloudFront.

**Khó khăn và bài học:** Sai base URL hoặc env var lúc build có thể làm bản deploy gọi nhầm API.

## Ngày 2 -  5/6: AWS Shield và bảo vệ trước tấn công DDoS

**Công việc đã thực hiện:** Tôi tìm hiểu AWS Shield và cách AWS cung cấp khả năng bảo vệ trước các cuộc tấn công từ chối dịch vụ phân tán (DDoS). Tôi nghiên cứu sự khác biệt giữa AWS Shield Standard và AWS Shield Advanced, đồng thời tìm hiểu cách AWS Shield kết hợp với CloudFront, Route 53 và các dịch vụ Edge khác để nâng cao khả năng sẵn sàng của ứng dụng.


**Kiến thức đã học:**Tôi hiểu rằng bảo vệ trước DDoS là một thành phần quan trọng trong bảo mật Cloud, đặc biệt đối với các ứng dụng được công khai trên Internet.

**Kết quả đạt được:** Tôi biết các điểm cần kiểm tra khi API bảo vệ trả 401 hoặc 403.

**Khó khăn và bài học:** Không nên lưu token tùy tiện hoặc log token ra console khi test.

## Ngày 3 - 8/6: Amazon GuardDuty và phát hiện mối đe dọa


**Công việc đã thực hiện:** Tôi tìm hiểu Amazon GuardDuty và cách dịch vụ này sử dụng Threat Intelligence cùng Machine Learning để phát hiện các hoạt động đáng ngờ trong môi trường AWS. Trong quá trình nghiên cứu, tôi tìm hiểu các cảnh báo liên quan đến API call bất thường, thông tin xác thực bị xâm phạm, hoạt động mạng độc hại và các nguy cơ bảo mật tiềm ẩn.


**Kiến thức đã học:** Tôi hiểu rằng GuardDuty cung cấp khả năng giám sát bảo mật liên tục và hỗ trợ phát hiện các mối đe dọa mà không cần phải phân tích thủ công toàn bộ hoạt động của hệ thống.

**Kết quả đạt được:** Tôi có checklist kiểm tra CORS cho API Gateway và Lambda response.

**Khó khăn và bài học:** Cần test bằng browser thật sau khi deploy stage API Gateway.

## Ngày 4 - 9/6: Amazon Inspector và đánh giá lỗ hổng bảo mật

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon Inspector và cách dịch vụ này tự động đánh giá các lỗ hổng bảo mật trong tài nguyên AWS. Tôi nghiên cứu cách Inspector phân tích EC2 Instance, Container Image và các thư viện ứng dụng để phát hiện các vấn đề như lỗ hổng phần mềm hoặc cấu hình không an toàn.

**Kiến thức đã học:** Tôi hiểu rằng việc đánh giá lỗ hổng giúp tổ chức phát hiện các điểm yếu bảo mật trước khi chúng có thể bị khai thác bởi kẻ tấn công.

**Kết quả đạt được:** Tôi hiểu các trạng thái cần có khi gọi API: loading, success, empty, error và unauthorized.

**Khó khăn và bài học:** Nếu không tách API logic, việc đổi endpoint hoặc auth header sẽ rất khó kiểm soát.

## Ngày 5 - 10/6: AWS KMS, Secrets Manager và tổng kết bảo mật


**Công việc đã thực hiện:** Tôi tìm hiểu AWS Key Management Service (KMS) và AWS Secrets Manager trong việc quản lý khóa mã hóa và thông tin nhạy cảm. Trong quá trình thực hành, tôi nghiên cứu về Customer Managed Keys, quy trình mã hóa/giải mã dữ liệu, Key Policy và cách lưu trữ an toàn các thông tin bí mật của ứng dụng. Tôi cũng tìm hiểu cách Secrets Manager giúp ứng dụng truy xuất thông tin xác thực an toàn mà không cần lưu trực tiếp dữ liệu nhạy cảm trong mã nguồn.


**Kiến thức đã học:** Tôi hiểu rằng mã hóa dữ liệu và quản lý Secrets là những thành phần quan trọng trong bảo mật Cloud, giúp bảo vệ dữ liệu nhạy cảm và ngăn chặn việc lộ thông tin xác thực.

**Kết quả đạt được:** Tôi có checklist tích hợp để dùng khi nhóm bắt đầu nối frontend với backend IRMS.

**Khó khăn và bài học:** Cần test theo flow người dùng thật, không chỉ test từng API riêng lẻ.
