---
title: "Tuần 4"
date: 2026-05-07
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

**Mốc thời gian:** 07/5 → 13/5 (5 ngày làm việc)

## Ngày 1 07/5: Tìm hiểu Amazon RDS

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon Relational Database Service (Amazon RDS) và vai trò của dịch vụ này như một hệ quản trị cơ sở dữ liệu quan hệ được quản lý trên nền tảng AWS. Trong quá trình thực hành, tôi nghiên cứu các hệ quản trị cơ sở dữ liệu mà Amazon RDS hỗ trợ, cấu hình một cơ sở dữ liệu, tìm hiểu các tùy chọn lưu trữ và học cách kết nối ứng dụng đến cơ sở dữ liệu thông qua Endpoint.

**Kiến thức đã học:** Tôi hiểu rằng Amazon RDS giúp đơn giản hóa việc quản trị cơ sở dữ liệu bằng cách tự động hóa các tác vụ như cập nhật phần mềm, sao lưu dữ liệu và bảo trì hệ thống, từ đó giúp người dùng tập trung hơn vào việc phát triển ứng dụng.


**Kết quả đạt được:** Tôi triển khai thành công một cơ sở dữ liệu Amazon RDS và nắm được quy trình cơ bản để tạo, cấu hình và quản lý cơ sở dữ liệu quan hệ trên AWS.


**Khó khăn và bài học:** Việc cấu hình cơ sở dữ liệu đòi hỏi phải cân nhắc kỹ các thiết lập về dung lượng lưu trữ, bảo mật và mạng nhằm đảm bảo cả hiệu năng lẫn khả năng truy cập an toàn.

## Ngày 2 - 12/5: CloudFront concepts

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon Aurora và so sánh dịch vụ này với các cơ sở dữ liệu quan hệ tiêu chuẩn trên Amazon RDS. Trong quá trình học, tôi nghiên cứu kiến trúc của Aurora, khả năng sẵn sàng cao (High Availability), cơ chế chuyển đổi dự phòng tự động (Automatic Failover) và các tính năng tối ưu hiệu năng. Tôi cũng tìm hiểu những trường hợp thực tế mà Aurora được ưu tiên sử dụng trong các hệ thống doanh nghiệp.

**Kiến thức đã học:** Tôi hiểu rằng Amazon Aurora được thiết kế để cung cấp hiệu năng cao hơn, khả năng mở rộng tốt hơn và độ sẵn sàng cao hơn so với các cơ sở dữ liệu quan hệ truyền thống, đồng thời vẫn tương thích với MySQL và PostgreSQL.

**Kết quả đạt được:** Tôi hiểu rõ sự khác biệt giữa Amazon Aurora và Amazon RDS tiêu chuẩn, đồng thời xác định được những trường hợp nên lựa chọn Aurora để triển khai hệ thống.

**Khó khăn và bài học:** Mặc dù Aurora sở hữu nhiều tính năng nâng cao, nhưng việc lựa chọn sử dụng cần dựa trên yêu cầu thực tế của ứng dụng thay vì mặc định sử dụng trong mọi trường hợp.

## Ngày 3 - 11/5: Tìm hiểu Amazon DynamoDB

**Công việc đã thực hiện:** TTôi tìm hiểu Amazon DynamoDB như một dịch vụ cơ sở dữ liệu NoSQL được quản lý hoàn toàn trên AWS. Trong bài thực hành, tôi nghiên cứu Table, Partition Key, Sort Key và cách tổ chức dữ liệu trong DynamoDB. Đồng thời, tôi tìm hiểu cách DynamoDB hỗ trợ các ứng dụng yêu cầu độ trễ thấp, khả năng mở rộng cao và giảm thiểu công việc quản trị cơ sở dữ liệu.


**Kiến thức đã học:** Tôi hiểu rằng DynamoDB phù hợp với các ứng dụng có số lượng truy cập lớn, cấu trúc dữ liệu linh hoạt và yêu cầu hiệu năng cao cùng khả năng tự động mở rộng.

**Kết quả đạt được:** Tôi hiểu rõ hơn về khái niệm cơ sở dữ liệu NoSQL cũng như sự khác biệt giữa DynamoDB và các cơ sở dữ liệu quan hệ truyền thống.

**Khó khăn và bài học:** Việc thiết kế Partition Key phù hợp rất quan trọng vì nó ảnh hưởng trực tiếp đến hiệu năng và khả năng mở rộng của cơ sở dữ liệu.


## Ngày 4 - 12/5: Amazon ElastiCache và tối ưu hiệu năng cơ sở dữ liệu

**Công việc đã thực hiện:** Tôi tìm hiểu Amazon ElastiCache và cách dịch vụ này cải thiện hiệu năng ứng dụng bằng cách giảm số lượng truy vấn gửi trực tiếp đến cơ sở dữ liệu. Tôi nghiên cứu các khái niệm cơ bản về Redis và Memcached, các trường hợp sử dụng phổ biến cũng như cách bộ nhớ đệm (Cache) giúp giảm thời gian phản hồi và nâng cao khả năng mở rộng của hệ thống.


**Kiến thức đã học:** Tôi hiểu rằng Cache là một kỹ thuật hiệu quả giúp tăng hiệu năng ứng dụng, giảm tải cho cơ sở dữ liệu và hỗ trợ các hệ thống có lượng truy cập lớn.

**Kết quả đạt được:** Tôi hiểu được vai trò của Amazon ElastiCache trong kiến trúc hệ thống trên nền tảng AWS và biết được những trường hợp nên áp dụng Cache để cải thiện hiệu năng tổng thể.

**Khó khăn và bài học:** Dữ liệu trong Cache có thể trở nên không đồng bộ với cơ sở dữ liệu nếu không được cập nhật đúng cách, vì vậy cần xây dựng chiến lược quản lý Cache phù hợp.

## Ngày 5 -13/5: Quản trị cơ sở dữ liệu và tổng kết tuần


**Công việc đã thực hiện:** Tôi tổng hợp lại các dịch vụ cơ sở dữ liệu đã học trong tuần, bao gồm Amazon RDS, Amazon Aurora, Amazon DynamoDB và Amazon ElastiCache. Tôi ghi lại những đặc điểm nổi bật, phương pháp quản lý, chiến lược sao lưu, khả năng giám sát hiệu năng cũng như các trường hợp sử dụng phổ biến của từng dịch vụ. Đồng thời, tôi cũng ôn tập các thao tác quản trị cơ bản như cấu hình cơ sở dữ liệu, theo dõi hiệu năng và xây dựng kế hoạch sao lưu, phục hồi dữ liệu.

**Kiến thức đã học:** Tôi nhận thấy mỗi dịch vụ cơ sở dữ liệu trên AWS được thiết kế để đáp ứng những nhu cầu khác nhau của ứng dụng, và việc lựa chọn giải pháp phù hợp phụ thuộc vào cấu trúc dữ liệu, khả năng mở rộng, hiệu năng và yêu cầu về tính sẵn sàng.

**Kết quả đạt được:** Tôi củng cố được kiến thức về các dịch vụ cơ sở dữ liệu trên AWS và hoàn thiện bộ ghi chú phục vụ cho các bài thực hành cũng như quá trình triển khai dự án trong những tuần tiếp theo.

**Khó khăn và bài học:** Việc so sánh giữa cơ sở dữ liệu quan hệ, cơ sở dữ liệu NoSQL và dịch vụ Cache giúp tôi hiểu rõ hơn vai trò của từng giải pháp. Tôi nhận thấy rằng lựa chọn kiến trúc cơ sở dữ liệu phù hợp là yếu tố quan trọng để xây dựng các ứng dụng Cloud có hiệu quả và khả năng mở rộng cao.