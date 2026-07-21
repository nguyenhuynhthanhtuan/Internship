---
title: "Tuần 3"
date: 2026-04-30
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

**Mốc thời gian:** 30/4 → 6/5 (5 ngày làm việc)

## Ngày 1 - 30/4: Tìm hiểu Amazon S3

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon S3 và mô hình lưu trữ đối tượng (Object Storage). Trong quá trình thực hành, tôi tạo S3 Bucket, tải lên và quản lý các Object, cấu hình các thiết lập của Bucket cũng như kiểm tra quyền truy cập. Ngoài ra, tôi cũng tìm hiểu sự khác biệt giữa Object Storage và File Storage truyền thống, đồng thời nghiên cứu các trường hợp sử dụng phổ biến của Amazon S3 như lưu trữ dữ liệu ứng dụng, nội dung cho Static Website và các tệp sao lưu.


**Kiến thức đã học:** Tôi hiểu rằng Amazon S3 là dịch vụ lưu trữ đối tượng có độ bền dữ liệu rất cao, được thiết kế để lưu trữ và truy xuất dữ liệu hiệu quả, đồng thời đảm bảo khả năng sẵn sàng và mở rộng linh hoạt.


**Kết quả đạt được:** Tôi tạo và quản lý thành công các S3 Bucket, đồng thời nắm được quy trình cơ bản để lưu trữ và truy cập dữ liệu trên Amazon S3.


**Khó khăn và bài học:** Quyền truy cập của Bucket cần được cấu hình cẩn thận vì các thiết lập không chính xác có thể vô tình khiến dữ liệu bị công khai ra bên ngoài.


## Ngày 2 - 1/5: Amazon EFS và lưu trữ tệp dùng chung

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon Elastic File System (EFS) và cách dịch vụ này cung cấp hệ thống lưu trữ tệp dùng chung cho nhiều EC2 Instance. Trong quá trình học, tôi so sánh Amazon EFS với Amazon EBS và Amazon S3 để hiểu rõ sự khác nhau về mục đích sử dụng, đặc điểm hiệu năng và các tình huống triển khai phù hợp của từng dịch vụ.


**Kiến thức đã học:** Tôi hiểu rằng Amazon EFS phù hợp với các ứng dụng cần hệ thống tệp dùng chung, trong khi Amazon EBS cung cấp lưu trữ dạng Block Storage còn Amazon S3 được thiết kế cho lưu trữ dạng Object Storage.

**Kết quả đạt được:** Tôi hiểu được sự khác biệt giữa ba dịch vụ lưu trữ của AWS và xác định được các trường hợp sử dụng phù hợp cho từng giải pháp.


**Khó khăn và bài học:** Mặc dù cả ba dịch vụ đều phục vụ mục đích lưu trữ dữ liệu, nhưng việc lựa chọn đúng dịch vụ phụ thuộc vào kiến trúc hệ thống và yêu cầu truy cập của ứng dụng.

## Ngày 3 - 4/5: Amazon S3 Storage Classes và Lifecycle Policy

**Công việc đã thực hiện:** Tôi tìm hiểu các lớp lưu trữ (Storage Classes) của Amazon S3, bao gồm Standard, Standard-IA, One Zone-IA, Intelligent-Tiering, Glacier Instant Retrieval, Glacier Flexible Retrieval và Glacier Deep Archive. Đồng thời, tôi thực hành cấu hình Lifecycle Policy để tự động chuyển dữ liệu giữa các lớp lưu trữ hoặc tự động xóa dữ liệu sau một khoảng thời gian xác định.

**Kiến thức đã học:** Tôi hiểu rằng việc lựa chọn lớp lưu trữ phù hợp và cấu hình Lifecycle Policy giúp tối ưu đáng kể chi phí lưu trữ, đồng thời vẫn đảm bảo khả năng truy cập dữ liệu theo nhu cầu sử dụng của doanh nghiệp.

**Kết quả đạt được:** Tôi cấu hình thành công Lifecycle Policy và hiểu được cách quản lý dữ liệu tự động nhằm tối ưu chi phí lưu trữ trên AWS.


**Khó khăn và bài học:** Việc tối ưu lưu trữ cần được tính toán cẩn thận vì lựa chọn lớp lưu trữ không phù hợp có thể làm tăng chi phí truy xuất dữ liệu hoặc ảnh hưởng đến khả năng truy cập.

## Ngày 4 - 5/5: AWS Backup và phục hồi dữ liệu

**Công việc đã thực hiện:** Tôi tìm hiểu AWS Backup và cách dịch vụ này hỗ trợ quản lý sao lưu tập trung cho nhiều dịch vụ AWS. Trong quá trình thực hành, tôi nghiên cứu Backup Plan, Backup Vault, thời gian lưu trữ (Retention Period) và Recovery Point. Đồng thời, tôi thực hành tạo các tác vụ sao lưu và tìm hiểu quy trình cơ bản để khôi phục dữ liệu từ bản sao lưu.

**Kiến thức đã học:** Tôi hiểu rằng việc sao lưu dữ liệu định kỳ là một phần quan trọng trong kế hoạch khắc phục thảm họa (Disaster Recovery), giúp nâng cao khả năng bảo vệ dữ liệu và đảm bảo tính liên tục của hệ thống.


**Kết quả đạt được:** Tôi hiểu rõ hơn về quy trình quản lý sao lưu cũng như tầm quan trọng của việc xây dựng kế hoạch sao lưu và chính sách lưu trữ dữ liệu phù hợp.


**Khó khăn và bài học:** Chỉ tạo bản sao lưu là chưa đủ, việc kiểm tra quy trình phục hồi dữ liệu cũng cần được thực hiện thường xuyên để đảm bảo dữ liệu có thể được khôi phục thành công khi xảy ra sự cố.


## Ngày 5 - 6/5: Tổng kết các dịch vụ lưu trữ

**Công việc đã thực hiện:** Tôi tổng hợp lại toàn bộ các dịch vụ lưu trữ đã học trong tuần, bao gồm Amazon S3, Amazon EFS, Storage Classes, Lifecycle Policy, Versioning và AWS Backup. Tôi ghi lại các đặc điểm chính, ưu điểm cũng như những trường hợp sử dụng phổ biến của từng dịch vụ. Đồng thời, tôi cũng tổng hợp các bước cấu hình và những Best Practices trong việc quản lý lưu trữ và bảo vệ dữ liệu trên nền tảng AWS.


**Kiến thức đã học:** Tôi nhận thấy AWS cung cấp nhiều giải pháp lưu trữ khác nhau nhằm đáp ứng từng nhu cầu của doanh nghiệp, và việc lựa chọn dịch vụ phù hợp cần dựa trên các yếu tố như hiệu năng, tính sẵn sàng, độ bền dữ liệu và chi phí.

**Kết quả đạt được:** Tôi củng cố được kiến thức về các dịch vụ lưu trữ trên AWS và hoàn thiện bộ ghi chú phục vụ cho các bài thực hành cũng như quá trình phát triển dự án trong những tuần tiếp theo.


**Khó khăn và bài học:** Việc tổng hợp và so sánh các dịch vụ lưu trữ sau khi hoàn thành các bài Lab giúp tôi hiểu rõ hơn sự khác biệt giữa từng dịch vụ và nâng cao khả năng lựa chọn giải pháp lưu trữ phù hợp cho từng tình huống thực tế.
