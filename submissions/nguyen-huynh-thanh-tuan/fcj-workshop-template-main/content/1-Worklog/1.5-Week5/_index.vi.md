---
title: "Tuần 5"
date: 2026-05-14
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

**Mốc thời gian:** 14/5 → 20/5 (5 ngày làm việc)

## Ngày 1 -14/5: Tìm hiểu Elastic Load Balancer

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon Elastic Load Balancer (ELB) và cách dịch vụ này phân phối lưu lượng truy cập đến nhiều EC2 Instance. Trong quá trình thực hành, tôi nghiên cứu các loại Load Balancer trên AWS, tìm hiểu Listener, Target Group và Health Check, đồng thời học cách ELB giúp nâng cao tính sẵn sàng và độ tin cậy của các ứng dụng triển khai trên nền tảng đám mây.


**Kiến thức đã học:** Tôi hiểu rằng Elastic Load Balancer tự động phân phối các yêu cầu đến những EC2 Instance đang hoạt động bình thường, giúp ứng dụng vẫn duy trì khả năng phục vụ ngay cả khi một hoặc nhiều máy chủ gặp sự cố.

**Kết quả đạt được:** Tôi hiểu được nguyên lý hoạt động của Elastic Load Balancer và vai trò của dịch vụ này trong việc nâng cao tính sẵn sàng cũng như phân phối lưu lượng truy cập cho ứng dụng.

**Khó khăn và bài học:** Việc cân bằng tải phụ thuộc vào cấu hình chính xác của Target Group và Health Check. Nếu cấu hình không đúng, các EC2 Instance vẫn hoạt động nhưng có thể không nhận được lưu lượng truy cập.

## Ngày 2 - 15/5: Amazon EC2 Auto Scaling

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon EC2 Auto Scaling và cách các chính sách mở rộng (Scaling Policies) tự động điều chỉnh số lượng EC2 Instance theo nhu cầu sử dụng của hệ thống. Trong quá trình thực hành, tôi nghiên cứu Auto Scaling Group, Launch Template, các chính sách mở rộng và cơ chế theo dõi tình trạng hoạt động của các Instance. Tôi cũng tìm hiểu cách Auto Scaling kết hợp với Elastic Load Balancer để duy trì hiệu năng ứng dụng khi lưu lượng truy cập thay đổi.

**Kiến thức đã học:** Tôi hiểu rằng Auto Scaling giúp tối ưu cả hiệu năng hệ thống và chi phí hạ tầng bằng cách tự động tăng hoặc giảm tài nguyên tính toán theo nhu cầu thực tế.


**Kết quả đạt được:** Tôi hiểu rõ hơn cách Auto Scaling duy trì tính sẵn sàng cao của hệ thống đồng thời giảm thiểu các chi phí vận hành không cần thiết.

**Khó khăn và bài học:** Các chính sách Auto Scaling cần được cấu hình hợp lý vì nếu mở rộng quá nhanh sẽ làm tăng chi phí, trong khi mở rộng không đủ sẽ ảnh hưởng đến hiệu năng của ứng dụng.

## Ngày 3 - 18/5: Tìm hiểu Amazon Route 53

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon Route 53 và cách dịch vụ DNS này định tuyến các yêu cầu của người dùng đến tài nguyên trên AWS. Trong quá trình học, tôi nghiên cứu Hosted Zone, các loại bản ghi DNS (DNS Record), Routing Policy và cách quản lý tên miền. Tôi cũng tìm hiểu cách Route 53 tích hợp với Elastic Load Balancer và các dịch vụ AWS khác để nâng cao tính sẵn sàng của hệ thống.


**Kiến thức đã học:** Tôi hiểu rằng Route 53 đóng vai trò quan trọng trong việc định tuyến lưu lượng truy cập một cách ổn định và hỗ trợ xây dựng các kiến trúc Cloud có tính sẵn sàng cao.

**Kết quả đạt được:** Tôi hiểu được mối quan hệ giữa tên miền, bản ghi DNS và các tài nguyên AWS khi triển khai ứng dụng trên nền tảng đám mây.

**Khó khăn và bài học:** Các thay đổi về cấu hình DNS cần có thời gian để đồng bộ (Propagation), do đó các thay đổi sẽ không có hiệu lực ngay lập tức.


## Ngày 4 - 19/5: Tìm hiểu Amazon CloudFront

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon CloudFront như một dịch vụ mạng phân phối nội dung (Content Delivery Network - CDN). Trong quá trình thực hành, tôi nghiên cứu cách CloudFront phân phối nội dung thông qua các Edge Location, giúp giảm độ trễ và cải thiện hiệu năng của ứng dụng web. Tôi cũng tìm hiểu về Cache Behavior, Origin Configuration, hỗ trợ HTTPS và cơ chế Cache Invalidation.

**Kiến thức đã học:** Tôi hiểu rằng CloudFront giúp phân phối nội dung nhanh hơn bằng cách lưu bộ nhớ đệm (Cache) các tài nguyên tĩnh tại các Edge Location gần người dùng, đồng thời giảm tải cho máy chủ gốc (Origin Server).


**Kết quả đạt được:** Tôi hiểu được vai trò của CloudFront trong việc nâng cao hiệu năng ứng dụng và cung cấp giải pháp phân phối nội dung hiệu quả.

**Khó khăn và bài học:**  Dữ liệu được lưu trong Cache có thể chưa được cập nhật ngay sau khi triển khai phiên bản mới, vì vậy cần thực hiện Cache Invalidation khi cần thiết để người dùng luôn truy cập được nội dung mới nhất.

## Ngày 5 - - 20/5: Tổng kết kiến trúc tính sẵn sàng cao

**Công việc đã thực hiện:** Tôi tổng hợp lại các dịch vụ AWS đã học trong tuần, bao gồm Elastic Load Balancer, Auto Scaling, Route 53 và CloudFront. Tôi ghi lại cách các dịch vụ này phối hợp với nhau để xây dựng kiến trúc Cloud có tính sẵn sàng cao và khả năng mở rộng tốt. Đồng thời, tôi cũng tổng hợp quy trình triển khai, các lưu ý khi cấu hình và những Best Practices nhằm nâng cao độ ổn định cũng như hiệu năng của hệ thống.


**Kiến thức đã học:** Tôi nhận thấy rằng để xây dựng một hệ thống Cloud có tính sẵn sàng cao cần có sự kết hợp của nhiều dịch vụ AWS, thay vì chỉ phụ thuộc vào một thành phần riêng lẻ.

**Kết quả đạt được:** Tôi củng cố được kiến thức về các dịch vụ hỗ trợ High Availability trên AWS và hoàn thiện bộ ghi chú phục vụ cho các bài thực hành cũng như việc thiết kế kiến trúc hệ thống trong các giai đoạn tiếp theo.

**Khó khăn và bài học:** Tính sẵn sàng cao không chỉ phụ thuộc vào việc triển khai nhiều dịch vụ mà còn yêu cầu cấu hình và tích hợp chúng một cách chính xác. Việc lập kế hoạch cẩn thận và kiểm tra thường xuyên là yếu tố quan trọng để đảm bảo hệ thống hoạt động ổn định và tin cậy.